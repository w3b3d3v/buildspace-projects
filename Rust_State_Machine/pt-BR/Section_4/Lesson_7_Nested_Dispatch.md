Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/752f5f7451846f9bd6b9bbcfc29c8fa6).

# Dispatch Aninhado

[Youtube](https://youtu.be/alTqa9Idu2I?si=2A7-tHZALDiPw7sv)

Agora que definimos a lógica de dispatch em nível de Pallet no próprio Pallet, devemos atualizar nosso Runtime para aproveitar essa lógica.

Depois disso, sempre que a lógica do Pallet for atualizada, a lógica de dispatch do Runtime também será automaticamente atualizada e roteará as chamadas diretamente. Isso torna nosso código mais fácil de gerenciar e evita potenciais erros ou manutenções futuras.

## Chamadas Aninhadas

O Pallet de Saldos agora expõe sua própria lista de chamadas em `balances::Call`. Em vez de listar todas elas novamente no Runtime, podemos usar um enum aninhado para rotear nossas chamadas corretamente.

Imagine a seguinte construção:

```rust
pub enum RuntimeCall {
	Balances(balances::Call<Runtime>),
}
```

Neste caso, temos uma variante `RuntimeCall::Balances`, que contém um tipo `balances::Call`. Isso significa que podemos acessar todas as chamadas expostas por `balances:Call` sob essa variante. À medida que criamos mais pallets ou estendemos nossas chamadas, essa estrutura aninhada se escala muito bem.

Chamamos o `RuntimeCall` de "enum externo" e o `balances::Call` de "enum interno". Essa construção de usar enums externos e internos é muito comum no SDK do Polkadot.

## Redespachando para o Pallet

Nossa lógica atual de dispatch chama diretamente as funções no Pallet. Como mencionamos, ter essa lógica fora do Pallet pode aumentar o fardo da manutenção ou erros.

Mas agora que definimos a lógica de dispatch em nível de Pallet no próprio Pallet, podemos usar isso para tornar o dispatch do Runtime mais extensível.

Para fazer isso, em vez de chamar diretamente a função do Pallet, podemos extrair a chamada interna do `RuntimeCall` e, em seguida, usar o `balances::Pallet` para despachar essa chamada para a lógica apropriada.

Isso seria algo assim:

```rust
match runtime_call {
	RuntimeCall::Balances(call) => {
		self.balances.dispatch(caller, call)?;
	},
}
```

Aqui você pode ver que a primeira coisa que fazemos é verificar se a chamada é uma variante `Balances`, então extraímos dela a `call`, que é um tipo `balances::Call`, e então usamos `self.balances`, que é um `balances::Pallet`, para despachar a `balances::Call`.

## Atualizando Seu Bloco

Como atualizamos a construção do enum `RuntimeCall`, também precisaremos atualizar nossa construção de `Block` na função `fn main`. Nada mágico aqui, apenas a necessidade de construir um enum aninhado usando tanto `RuntimeCall::Balances` quanto `balances::Call::Transfer`.

## Exercícios:

### Habilite o Dispatch Aninhado

Agora é a hora de completar esta etapa e colar juntos o dispatch em nível de Pallet com a lógica de dispatch em nível de Runtime.

Siga os `TODO`s fornecidos no template para obter sua lógica de dispatch de ponta a ponta em execução.

No `main.rs`:

```rust
/// ...código anterior.

// Estas são todas as chamadas que são expostas ao mundo.
// Observe que é apenas uma acumulação das chamadas expostas por cada módulo.
pub enum RuntimeCall {
	/* TODO: Transforme isso em uma enumeração aninhada onde a variante `Balances` contém um `balances::Call`. */
	BalancesTransfer { to: types::AccountId, amount: types::Balance },
}

/// ...código anterior.

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
			/*
				TODO:
				Ajuste esta lógica para lidar com as enumerações aninhadas e simplesmente chame a lógica `dispatch`
  				na chamada de saldos, em vez de na função diretamente
			*/
			RuntimeCall::BalancesTransfer { to, amount } => {
				self.balances.transfer(caller, to, amount)?;
			},
		}
		Ok(())
	}
}

fn main() {
    /// ...código anterior.

    // Aqui estão os extrínsecos do nosso bloco.
    // Você pode adicionar ou remover estes com base nos módulos e chamadas que você configurou.
    let block_1 = types::Block {
        header: support::Header { block_number: 1 },
        extrinsics: vec![
            /* TODO: Atualize seus extrínsecos para usar o enum aninhado. */
            support::Extrinsic {
                caller: alice.clone(),
                call: RuntimeCall::Balances(balances::Call::Transfer { to: bob, amount: 20 }),
            },
            support::Extrinsic {
                caller: alice,
                call: RuntimeCall::Balances(balances::Call::Transfer { to: charlie, amount: 20 }),
            },
        ],
    };

    /// ...código anterior.
}
```

### 🚀 Continue Voando Alto
VOCÊ ESTÁ INDO MUITO BEM.

A Seção 4 está terminada! A reta final começou. Excelente trabalho :).