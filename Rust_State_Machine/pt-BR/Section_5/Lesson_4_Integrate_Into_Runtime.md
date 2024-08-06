Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/4a8f9dec88337d3b6f40e4d6840dc305).

# Integrar PoE ao seu Runtime

[Youtube](https://www.youtube.com/watch?v=E318nxGLXb8)

O pallet de Prova de Existência está concluído, mas ainda precisamos integrá-lo ao seu Runtime.

Vamos dar uma olhada nesse processo.

## Passos de Integração

1. O primeiro passo é adicionar o campo `proof_of_existence` ao seu `struct Runtime`.
2. Em seguida, você precisa atualizar sua função `fn new()` para também inicializar `proof_of_existence`.
3. Depois, crie um novo `type Content` concreto que seja um `&'static str`. Como mencionado, normalmente isso seria um hash, mas para simplificar, estamos usando novamente uma string estática simples.

	> Se você quiser usar um hash agora ou no futuro, seria tão simples quanto atualizar essa única linha para mudar todos os tipos no seu Runtime e Pallet. Esse é o tipo de flexibilidade que estamos buscando!

4. Em seguida, implemente `proof_of_existence::Config` para `Runtime`, usando seu `types::Content`.
5. Neste ponto, tudo já deve compilar com sucesso, então use isso como um ponto de verificação.
6. Introduza uma nova variante `ProofOfExistence` para o `RuntimeCall`.
7. Finalmente, atualize sua lógica `fn dispatch` para lidar com o redespacho das chamadas `ProofOfExistence` para o `proof_of_existence::Pallet`.

Espero que, com esse processo, você possa ver como todas as abstrações que introduzimos tornaram a integração de novos Pallets ao seu runtime bastante fácil.

Em breve, tornaremos esse processo ainda mais fácil usando macros!

No `main.rs`:

```rust
/// ...código anterior.

// Estes são os tipos concretos que usaremos em nossa máquina de estado simples.
// Os módulos são configurados diretamente para esses tipos, e eles satisfazem todos os nossos requisitos de traits.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
	pub type Extrinsic = crate::support::Extrinsic<AccountId, crate::RuntimeCall>;
	pub type Header = crate::support::Header<BlockNumber>;
	pub type Block = crate::support::Block<Header, Extrinsic>;
	/* TODO: Adicione o tipo concreto `Content` para o seu runtime. */
}

// Estas são todas as chamadas que são expostas ao mundo.
// Note que é apenas uma acumulação das chamadas expostas por cada módulo.
pub enum RuntimeCall {
	Balances(balances::Call<Runtime>),
	/* TODO: Adicione uma variante `ProofOfExistence` para acessar `proof_of_existence::Call`. */
}

// Este é o nosso Runtime principal.
// Ele acumula todos os diferentes pallets que queremos usar.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet<Self>,
	balances: balances::Pallet<Self>,
	/* TODO: Adicione o campo `proof_of_existence` ao seu `Runtime`. */
}

/// ...código anterior.

/* TODO: Implemente `proof_of_existence::Config` para `Runtime`. */

impl Runtime {
	// Crie uma nova instância do Runtime principal, criando uma nova instância de cada pallet.
	fn new() -> Self {
		Self {
			system: system::Pallet::new(),
			balances: balances::Pallet::new(),
			/* TODO: Inicialize o pallet `proof_of_existence`. */
		}
	}

    /// ...código anterior.
}

impl crate::support::Dispatch for Runtime {
	type Caller = <Runtime as system::Config>::AccountId;
	type Call = RuntimeCall;
	// Despacha uma chamada em nome de um chamador. Incrementa o nonce do chamador.
	//
	// Dispatch nos permite identificar qual chamada do módulo subjacente queremos executar.
	// Note que extraímos o `caller` da extrinsic, e usamos essa informação
	// para determinar em nome de quem estamos executando a chamada.
	fn dispatch(
		&mut self,
		caller: Self::Caller,
		runtime_call: Self::Call,
	) -> support::DispatchResult {
		// Esta declaração de correspondência nos permitirá rotear corretamente `RuntimeCall`s
		// para a função apropriada no nível do pallet.
		match runtime_call {
			RuntimeCall::Balances(call) => {
				self.balances.dispatch(caller, call)?;
			},
			/* TODO: Despache `calls` para o pallet `ProofOfExistence`. */
		}
		Ok(())
	}
}

/// ...código anterior.
```