Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/a86959a1c50628cb2af1bdde52d55199).

# Dispatch em Nível de Pallet

Queremos tornar nosso código mais modular e extensível.

Atualmente, todo o dispatch ocorre através do `RuntimeCall`, o que codifica a lógica de dispatch para cada um dos Pallets em nosso sistema.

O que preferimos é que a lógica de dispatch em nível de Pallet viva no próprio Pallet, e nosso Runtime aproveite isso. Já vimos de ponta a ponta o que é necessário para configurar o dispatch de chamadas, então vamos fazer isso novamente no nível de Pallet.

## Chamada de Pallet

Para tornar nosso sistema mais extensível, queremos manter todas as chamadas de um pallet definidas no nível do pallet.

Para isso, definimos um `enum Call` em nosso Pallet de Saldos, e, assim como antes, introduzimos uma nova variante de enum representando a função que queremos chamar.

Observe que este enum precisa ser genérico sobre `T: Config` porque precisamos acessar os tipos definidos por nossa trait de configuração!

## Dispatch de Pallet

Você também notará no template, incluímos a estrutura para você implementar o dispatch em nível de Pallet.

Tudo deve parecer o mesmo que o dispatch em nível de Runtime, exceto que o `type Call` é a chamada em nível de Pallet que acabamos de criar.

Assim como antes, você simplesmente precisa fazer o match da variante `Call` com a função apropriada e passar os parâmetros necessários para a função.

## Exercícios:

### Crie Seu Dispatch em Nível de Pallet

Siga os `TODO`s no template para completar a lógica para o dispatch em nível de Pallet.

No próximo passo, usaremos essa lógica para melhorar nossa lógica de dispatch em nosso Runtime.

No `balances.rs`:

```rust
/// ... código anterior.

// Um ​​enum público que descreve as chamadas que queremos expor ao despachante.
// Devemos esperar que o chamador de cada chamada seja fornecido pelo despachante,
// e não incluído como parâmetro da chamada.
pub enum Call<T: Config> {
	/* TODO: Crie uma variante enum `Transfer` que contenha campos nomeados:
	- `to`: um `T::AccountId`
	- `amount`: a `T::Saldo`
	*/
	/* TODO: Remova o espaço reservado `RemoveMe`. */
	RemoveMe(core::marker::PhantomData<T>),
}

/// Implementação da lógica de despacho, mapeamento de `BalancesCall` para o subjacente apropriado
/// função que queremos executar.
impl<T: Config> crate::support::Dispatch for Pallet<T> {
	type Caller = T::AccountId;
	type Call = Call<T>;

	fn dispatch(
		&mut self,
		caller: Self::Caller,
		call: Self::Call,
	) -> crate::support::DispatchResult {
		/* TODO: use uma instrução `match` para rotear a `Call` para a função de palete apropriada. */
		Ok(())
	}
}

/// ... código anterior.
```