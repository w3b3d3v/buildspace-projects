Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/6950ef0c080da83e41eaf2aaa5dde20b).

# Usando a Macro Runtime

Finalmente, vamos adicionar a macro `#[macros::runtime]` ao nosso arquivo `main.rs` e realmente limpar um monte de código repetitivo.

## Macro Runtime

[Youtube](https://www.youtube.com/watch?v=qANsLBpuRlc)

O objetivo da macro `#[macros::runtime]` é eliminar todas as funções repetitivas que implementamos para o `Runtime`, incluindo `fn new()` e `fn execute_block()`. Semelhante à macro `Call`, ela também gera o `enum RuntimeCall` e toda a lógica de `dispatch` para redespachar para os pallets.

Aplicamos o atributo `#[macros::runtime]` no topo do objeto `struct Runtime` principal.

### Parse

Para gerar o código que queremos, precisamos acompanhar:

1. O nome da `struct` que representa nosso Runtime. Normalmente é `Runtime`, mas fornecemos flexibilidade para o desenvolvedor.
2. A lista de Pallets incluídos no nosso `Runtime`
   1. Seus nomes, conforme especificado pelo usuário.
   2. O tipo específico para seu `Pallet`, por exemplo, `balances::Pallet` vs `proof_of_existence::Pallet`.

Todas essas informações são rastreadas na struct `RuntimeDef`.

Também estamos verificando se a definição do nosso `Runtime` sempre contém o System Pallet e o faz como o primeiro pallet na nossa definição de `Runtime`. Explicaremos mais sobre a suposição das macros abaixo.

### Expand

Depois de analisar todos os dados que precisamos, só precisamos gerar o código que esperamos.

Começando com `let runtime_impl = quote!`, você verá que todo o bloco de código `impl Runtime` foi engolido pela macro. Como sabemos todos os pallets no seu `Runtime`, podemos implementar automaticamente funções como `new()`. A função `execute_block` não aproveita nenhum dos dados analisados, mas o código é completamente repetitivo, então o escondemos.

Depois, temos outro bloco de código sendo gerado com `let dispatch_impl = quote!`, que é o `enum RuntimeCall` e a implementação de `Dispatch for Runtime`.

Novamente, devido às peculiaridades de usar macros, nosso enum `RuntimeCall` terá variantes em `snake_case` que correspondem exatamente ao nome dos campos na struct `Runtime`.

## Suposições da Macro

Uma das suposições programadas nessas macros é a existência do System Pallet. Por exemplo, na lógica de `execute_block`, precisamos acessar tanto `system.inc_block_number` quanto `system.inc_nonce`.

Algumas suposições a nível de macro são intencionais e, na verdade, definem as decisões arquitetônicas do framework que projeta essas macros. Este é o caso com o System Pallet, já que tanto do framework de blockchain depende de uma camada meta-consistente.

Outras suposições existem apenas porque é mais fácil escrever a macro se a suposição for feita.

A principal conclusão aqui é que as macros quase sempre podem continuar a melhorar, proporcionando experiências de usuário cada vez melhores para os desenvolvedores. Basta alguém identificar quais melhorias precisam ser feitas e alguém programar essas melhorias no código de macro de baixo nível.

## Exercícios:

### Adicione a Macro Runtime

Vamos finalmente passar pelos passos para adicionar o atributo `#[macros::runtime]` ao seu `Runtime`.

1. No `main.rs`, adicione `#[macros::runtime]` no topo da sua `pub struct Runtime`.
2. Remova todo o bloco de código `impl Runtime`.
3. Remova todo o `enum RuntimeCall`.
4. Remova toda a implementação de `Dispatch for Runtime`.
5. Atualize as instâncias do enum `RuntimeCall` para usar `snake_case`:
   - Altere `RuntimeCall::Balances` para `RuntimeCall::balances`.
   - Altere `RuntimeCall::ProofOfExistence` para `RuntimeCall::proof_of_existence`.

E é isso! Você completou o tutorial completo para construir uma máquina de estado simples em Rust. 🎉

No `main.rs`:

```rust
mod balances;
mod proof_of_existence;
mod support;
mod system;

use crate::support::Dispatch;

// Esses são os tipos concretos que usaremos na nossa máquina de estado simples.
// Os módulos são configurados diretamente para esses tipos e satisfazem todos os nossos
// requisitos de trait.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
	pub type Extrinsic = crate::support::Extrinsic<AccountId, crate::RuntimeCall>;
	pub type Header = crate::support::Header<BlockNumber>;
	pub type Block = crate::support::Block<Header, Extrinsic>;
	pub type Content = &'static str;
}

// Estas são todas as chamadas que são expostas ao mundo.
// Note que é apenas uma acumulação das chamadas expostas por cada módulo.
/* TODO: Remova o `RuntimeCall`. Isso agora é gerado pela `#[macros::runtime]`. */
pub enum RuntimeCall {
	Balances(balances::Call<Runtime>),
	ProofOfExistence(proof_of_existence::Call<Runtime>),
}

// Este é o nosso Runtime principal.
// Ele acumula todos os diferentes pallets que queremos usar.
/* TODO: Adicione o atributo `#[macros::runtime]` aqui e remova o código duplicado listado pelos TODOs. */
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet<Self>,
	balances: balances::Pallet<Self>,
	proof_of_existence: proof_of_existence::Pallet<Self>,
}

impl system::Config for Runtime {
	type AccountId = types::AccountId;
	type BlockNumber = types::BlockNumber;
	type Nonce = types::Nonce;
}

impl balances::Config for Runtime {
	type Balance = types::Balance;
}

impl proof_of_existence::Config for Runtime {
	type Content = types::Content;
}

/* TODO: Remova tudo isso. Agora é gerado pelo atributo `#[macros::runtime]`. */
impl Runtime {
	// Cria uma nova instância do Runtime principal, criando uma nova instância de cada pallet.
	fn new() -> Self {
		Self {
			system: system::Pallet::new(),
			balances: balances::Pallet::new(),
			proof_of_existence: proof_of_existence::Pallet::new(),
		}
	}

	// Executa um bloco de extrínsecos. Aumenta o número do bloco.
	fn execute_block(&mut self, block: types::Block) -> support::DispatchResult {
		self.system.inc_block_number();
		if block.header.block_number != self.system.block_number() {
			return Err(&"block number does not match what is expected");
		}
		// Um ​​erro extrínseco não é suficiente para tornar o bloco inválido. Nós capturamos o
		// resultado e emitimos uma mensagem de erro se alguma for emitida.
		for (i, support::Extrinsic { caller, call }) in block.extrinsics.into_iter().enumerate() {
			self.system.inc_nonce(&caller);
			let _res = self.dispatch(caller, call).map_err(|e| {
				eprintln!(
					"Extrinsic Error\n\tBlock Number: {}\n\tExtrinsic Number: {}\n\tError: {}",
					block.header.block_number, i, e
				)
			});
		}
		Ok(())
	}
}

/* TODO: Remova tudo isso também. A lógica de despacho é gerada automaticamente. */
impl crate::support::Dispatch for Runtime {
	type Caller = <Runtime as system::Config>::AccountId;
	type Call = RuntimeCall;
	// Despacha uma chamada em nome de um chamador. Aumenta o nonce do chamador.
	//
	// Dispatch nos permite identificar qual chamada de módulo subjacente queremos executar.
	// Observe que extraímos o `chamador` do extrínseco e usamos essa informação
	// para determinar em nome de quem estamos executando a chamada.
	fn dispatch(
		&mut self,
		caller: Self::Caller,
		runtime_call: Self::Call,
	) -> support::DispatchResult {
		// Esta instrução match nos permitirá rotear corretamente `RuntimeCall`s
		// para a função de nível de palete apropriada.
		match runtime_call {
			RuntimeCall::Balances(call) => {
				self.balances.dispatch(caller, call)?;
			},
			RuntimeCall::ProofOfExistence(call) => {
				self.proof_of_existence.dispatch(caller, call)?;
			},
		}
		Ok(())
	}
}

/* TODO: Atualize os extrínsecos para corresponder ao `RuntimeCall` gerado automaticamente. */
fn main() {
	// Cria uma nova instância do Runtime.
	// Ele irá instanciar com ele todos os módulos que utiliza.
	let mut runtime = Runtime::new();
	let alice = "alice".to_string();
	let bob = "bob".to_string();
	let charlie = "charlie".to_string();

	// Inicializa o sistema com algum equilíbrio inicial.
	runtime.balances.set_balance(&alice, 100);

	// Aqui estão os extrínsecos do nosso bloco.
	// Você pode adicionar ou remover estes com base nos módulos e chamadas que você configurou.
	let block_1 = types::Block {
		header: support::Header { block_number: 1 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::Balances(balances::Call::transfer {
					to: bob.clone(),
					amount: 20,
				}),
			},
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::Balances(balances::Call::transfer { to: charlie, amount: 20 }),
			},
		],
	};

	let block_2 = types::Block {
		header: support::Header { block_number: 2 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::create_claim {
					claim: &"Hello, world!",
				}),
			},
			support::Extrinsic {
				caller: bob.clone(),
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::create_claim {
					claim: &"Hello, world!",
				}),
			},
		],
	};

	let block_3 = types::Block {
		header: support::Header { block_number: 3 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice,
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::revoke_claim {
					claim: &"Hello, world!",
				}),
			},
			support::Extrinsic {
				caller: bob,
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::create_claim {
					claim: &"Hello, world!",
				}),
			},
		],
	};

	// Executa os extrínsecos que compõem nossos blocos.
	// Se houver algum erro, nosso sistema entra em pânico, pois não devemos executar blocos inválidos.
	runtime.execute_block(block_1).expect("invalid block");
	runtime.execute_block(block_2).expect("invalid block");
	runtime.execute_block(block_3).expect("invalid block");

	// Simplesmente imprima o formato de depuração do nosso estado de tempo de execução.
	println!("{:#?}", runtime);
}
```

Adicionar o atributo `#[macros::runtime]` é a etapa final para organizar e melhorar seu código de tempo de execução. Parabéns por chegar ao final deste tutorial!

Se você tiver dúvidas ou precisar de ajuda, sinta-se à vontade para perguntar no canal [#🆘・seção-6](https://discord.com/channels/898706705779687435/1261079463731662959) no Discord.

E lembre-se: ajudar outras pessoas com suas dúvidas também pode fortalecer sua própria compreensão. Compartilhar conhecimento é uma ótima maneira de solidificar o que você aprendeu! 🚀