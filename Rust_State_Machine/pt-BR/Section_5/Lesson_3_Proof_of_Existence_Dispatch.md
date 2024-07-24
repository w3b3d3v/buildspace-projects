Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/29701a39662dbff21d9f82a1abbdc318).

# Adicionar Dispatch para Prova de Existência

[Youtube](https://youtu.be/SMlaSsr0UOk?si=uyKxutZeccHgB7Kw)

Já estabelecemos o pipeline de dispatch aninhado para Pallets no `Runtime`.

Vamos construir a lógica de dispatch em nível de Pallet para a Prova de Existência para aproveitar isso.

# Exercícios:

### Criar Dispatch em Nível de Pallet

Não há nada de novo aqui, mas deixamos mais para você preencher do que antes.

1. Crie as variantes para `CreateClaim` e `RevokeClaim` para o seu enum `Call`.
2. Implemente o trait `Dispatch` para o seu `Pallet`.

Se você ficar preso, tente não olhar a solução fornecida aqui, mas em vez disso, veja o que você fez no Pallet de Saldos (Balances). Tudo o que fizemos aqui, já fizemos no passado. Esta é uma oportunidade para identificar onde você pode ter dúvidas ou mal-entendidos.

No `proof_of_existence.rs`:

```rust
/// ...código anterior.

// Um enum público que descreve as chamadas que queremos expor ao despachante.
// Devemos esperar que o chamador de cada chamada seja fornecido pelo despachante,
// e não incluído como um parâmetro da chamada.
pub enum Call<T: Config> {
	/*
		TODO:
		Crie variantes para:
		- `CreateClaim`
		- `RevokeClaim`

		Lembre-se de que você só precisa passar os dados da `claim`, pois as informações do `caller`
		são passadas na lógica de `dispatch`.
	*/
	RemoveMe(core::marker::PhantomData<T>),
}

/// Implementação da lógica de dispatch, mapeando de `POECall` para a função subjacente apropriada que queremos executar.
impl<T: Config> crate::support::Dispatch for Pallet<T> {
	/*
		TODO:
		Implemente `crate::support::Dispatch` para `Pallet<T>`.

		Na sua lógica de `dispatch`, faça correspondência na `call` e encaminhe os dados de `caller`
		e `claim` para a função apropriada.
	*/
}

/// ...código anterior.
```

Hey! Great job on adding the Proof of Existence dispatch logic!

Ei! Ótimo trabalho ao adicionar a lógica de dispatch para Prova de Existência!

Se você não postar uma captura de tela no [#progress](https://discord.com/channels/898706705779687435/980906289968345128) mostrando suas novas chamadas CreateClaim e RevokeClaim em ação, Anna pode enlouquecer! E se você não conhece a Anna, claramente não está compartilhando seu progresso o suficiente.

A propósito -- se você quiser fazer um tweet dizendo ao mundo que você acabou de implementar a Prova de Existência na sua blockchain e marcar [@web3dev_](https://x.com/web3dev_) Esse tweet pode inspirar alguém a se juntar ao web3 e isso nos apoia!