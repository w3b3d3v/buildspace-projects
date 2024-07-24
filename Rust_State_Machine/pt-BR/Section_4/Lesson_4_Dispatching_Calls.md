Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/20468d188032e4744deb00649e07a3ce).

# Despachando Chamadas

Construímos nossa lógica de `execute_block` dependendo da lógica de `dispatch` que ainda não implementamos.

Vamos fazer isso agora.

## Adicionando Nossas Chamadas

A lógica de dispatch é toda sobre direcionar o extrínseco de um usuário para a função adequada do Pallet. Até agora, a única função chamável pelo usuário que criamos é a função `transfer` no Pallet de Saldos.

Então vamos adicionar essa chamada ao nosso enum `RuntimeCall`.

Nossa função `transfer` espera 3 entradas:

- `caller`: A conta que está chamando a função de transferência e cujo saldo será reduzido.
- `to`: A conta para onde os fundos serão enviados.
- `amount`: A quantidade de fundos a ser transferida.

No entanto, lembre-se de que nossa lógica de `dispatch` já possui informações sobre o `caller`, que vem do `Extrinsic` no `Block`. Portanto, não precisamos desses dados novamente no `RuntimeCall`.

Na verdade, toda `Call` em nosso runtime deve omitir o `caller`, sabendo que ele está sendo fornecido por nossa lógica de `dispatch`.

Então, ao adicionar uma nova variante ao `RuntimeCall`, ela deve se parecer com isso:

```rust
pub enum RuntimeCall {
	BalancesTransfer { to: types::AccountId, amount: types::Balance },
}
```

Um usuário enviando um extrínseco para nossa máquina de estados pode usar essa variante do enum para especificar qual função deseja chamar (`transfer`) e os parâmetros necessários para essa chamada.

## Lógica de Dispatch

A lógica central na função `dispatch` é uma simples declaração `match`.

Basicamente, dada uma `RuntimeCall`, precisamos fazer o match na variante fornecida para nós e então passar os parâmetros apropriados para a função correta do Pallet. Como mencionado anteriormente, `dispatch` já tem acesso às informações do `caller`, então a lógica final é tão simples quanto:

```rust
match runtime_call {
	RuntimeCall::BalancesTransfer { to, amount } => {
		self.balances.transfer(caller, to, amount)?;
	}
}
```

A lógica de dispatch é realmente tão simples!

Observe que propagamos quaisquer erros retornados por nossa chamada de função com o operador `?`. Isso é importante se você quiser ver as mensagens de erro que configuramos na lógica de `execute_block`.

## Exercícios:

### Escreva Sua Lógica de Dispatch

Siga os `TODO`s fornecidos no template para construir seu `RuntimeCall` e completar sua lógica de `dispatch`.

No `main.rs`:

```rust
/// ...código anterior.

// Estas são todas as chamadas expostas para o mundo.
// Note que é apenas uma acumulação das chamadas expostas por cada módulo.
pub enum RuntimeCall {
	/* TODO: Crie uma variante do enum `BalancesTransfer` que contém campos nomeados:
		- `to`: um `AccountId`
		- `amount`: um `Balance`
	*/
}

/// ...código anterior.

impl crate::support::Dispatch for Runtime {
	type Caller = <Runtime as system::Config>::AccountId;
	type Call = RuntimeCall;
	// Despacha uma chamada em nome de um chamador. Incrementa o nonce do chamador.
	//
	// Despachar permite-nos identificar qual chamada de módulo subjacente queremos executar.
	// Note que extraímos o `caller` do extrínseco e usamos essa informação
	// para determinar em nome de quem estamos executando a chamada.
	fn dispatch(
		&mut self,
		caller: Self::Caller,
		runtime_call: Self::Call,
	) -> support::DispatchResult {
		/*
			TODO:
			Use uma declaração de match para direcionar o `runtime_call` para chamar a função apropriada no
			nosso pallet. Neste caso, há apenas `self.balances.transfer`.

			Sua `runtime_call` não conterá as informações do caller que são necessárias para fazer a
			chamada `transfer`, mas você tem essas informações a partir dos argumentos para a função `dispatch`.

			Você deve propagar quaisquer erros da chamada de volta a esta função.
		*/
		Ok(())
	}
}
/// ...código anterior.
```

## 🥳 Parabéns! 

Você completou com sucesso a lógica de dispatch! Este é um grande passo para tornar seu runtime mais funcional e flexível. Agora você pode processar chamadas de usuário e direcioná-las para as funções apropriadas dentro dos seus pallets.

Se você tiver alguma dúvida ou encontrar problemas, não hesite em pedir ajuda no canal [#🆘・section-4](https://discord.com/channels/898706705779687435/980905761783832637) no Discord. Estamos aqui para te apoiar! 🚀