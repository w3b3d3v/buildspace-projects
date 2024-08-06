# Adicionando a Macro Call ao Balances

[Youtube](https://www.youtube.com/watch?v=fvIw0ezyZLg)

Vamos começar adicionando a macro `#[macros::call]` ao nosso Pallet de Balances.

## A Macro Call

O objetivo da macro `#[macros::call]` é gerar automaticamente o `enum Call` a partir das funções do pallet e da lógica de `Dispatch` em nível de pallet encontrada em cada Pallet.

Podemos colocar o atributo `#[macros::call]` sobre nosso `impl<T: Config> Pallet<T>` onde as funções chamáveis são implementadas. A partir daí, a macro pode analisar todo o objeto e extrair os dados necessários. Nem todas as suas funções são destinadas a serem chamáveis, então você pode isolar as funções que devem estar em seu próprio `impl<T: Config> Pallet<T>`, conforme o template.

### Analisar

Para gerar o código que queremos, precisamos rastrear:

1. Cada função chamável que o desenvolvedor deseja expor através do Runtime.
   1. O nome dessa função.
   2. Os nomes e tipos dos argumentos dessa função.
2. O nome do `struct` onde essas funções são implementadas. Normalmente, isso é `Pallet`, mas podemos permitir flexibilidade no nome para o desenvolvedor.

Essas coisas são rastreadas com `CallDef` e `CallVariantDef`.

Além disso, durante o processo de análise, talvez queiramos verificar certas consistências no código sendo analisado. Nesse caso, exigimos que toda função chamável tenha `caller` como seu primeiro parâmetro com o tipo `T::AccountId`. Isso deve fazer sentido para você, já que projetou várias funções chamáveis diferentes, e todas seguem esse padrão.

Essa lógica de verificação é tratada pela função `fn check_caller_arg`.

### Expandir

Depois de analisarmos todos os dados necessários, gerar o código é bastante direto.

Se você descer para `let dispatch_impl = quote!`, verá um monte de código que se parece com os templates que usamos anteriormente no tutorial. Apenas deixamos marcadores onde a lógica de geração da macro deve colocar todas as informações para escrever o código de que precisamos.

## Peculiaridades das Macros

As macros geralmente são muito "peculiares" quando você as usa. Como toda a entrada que entra na macro é outro código, às vezes o formato desse código pode não corresponder ao que você espera.

Por exemplo, o `enum Call` original que construímos parece:

```rust
pub enum Call<T: Config> {
	Transfer { to: T::AccountId, amount: T::Balance },
}
```

A variante é chamada `Transfer` porque a função que representa é nomeada `fn transfer`.

No entanto, se quisermos gerar o `enum Call`, e só temos `fn transfer`, de onde tiraremos a string específica `Transfer` com `T` maiúsculo?

É possível fazer manipulação de strings e ajustar tudo para torná-lo consistente com o que o Rust espera, mas, nesse caso, é melhor que nossas macros façam modificações mínimas no código escrito pelo usuário.

O que isso significa?

Quando a macro `#[macros::call]` gera nosso `enum Call`, na verdade, ficará assim:

```rust
#[allow(non_camel_case_types)]
pub enum Call<T: Config> {
	transfer { to: T::AccountId, amount: T::Balance },
}
```

Aqui, você vê que `transfer` é exatamente a string que vem do nome da função. Normalmente, todas as variantes de enum devem ser `CamelCase`, mas como as funções rust são `snake_case`, nosso enum terá variantes que também são `snake_case`. Não veremos nenhum aviso sobre isso porque ativamos `#[allow(non_camel_case_types)]`.

Em última análise, isso não tem impacto significativo no seu código subjacente. É apenas ergonomia e expectativas.

De fato, as macros podem ser peculiares, mas a quantidade de tempo que economizam faz com que valham a pena.

## Exercícios:

### Hora de Adicionar Sua Macro Call

1. Se você ainda não fez, mova sua função `transfer` para seu próprio `impl<T: Config> Pallet<T>`. Queremos aplicar a macro apenas a essa função, então precisamos isolá-la das outras funções que não são destinadas a serem chamáveis.
2. Adicione o atributo `#[macros::call]` sobre este novo `impl<T: Config> Pallet<T>`.
3. Exclua seu `enum Call` existente.
4. Exclua sua implementação existente de `Dispatch for Pallet`.
5. Em seu arquivo `main.rs`, altere as instâncias de `balances::Call::Transfer` para `balances::Call::transfer` com um `t` minúsculo.

Neste ponto, tudo deve compilar como antes! Estamos testemunhando o poder das macros para gerar código para nós automaticamente!

No `balances.rs`:

```rust
/// ...código anterior.

/* TODO: Adicione o atributo `#[macros::call]` aqui. */
impl<T: Config> Pallet<T> {
	/// ...código anterior.
}

// Um enum público que descreve as chamadas que queremos expor ao dispatcher.
// Devemos esperar que o chamador de cada chamada seja fornecido pelo dispatcher,
// e não incluído como um parâmetro da chamada.
/* TODO: Remova o `enum Call`, isso está sendo gerado automaticamente por `#[macros::call]`. */
pub enum Call<T: Config> {
	Transfer { to: T::AccountId, amount: T::Balance },
}

/// Implementação da lógica de dispatch, mapeando de `BalancesCall` para a função subjacente apropriada
/// que queremos executar.
/* TODO: Remova esta impl de `Dispatch`, isso também está sendo gerado por `#[macros::call]`. */
impl<T: Config> crate::support::Dispatch for Pallet<T> {
	type Caller = T::AccountId;
	type Call = Call<T>;

	fn dispatch(
		&mut self,
		caller: Self::Caller,
		call: Self::Call,
	) -> crate::support::DispatchResult {
		match call {
			Call::Transfer { to, amount } => {
				self.transfer(caller, to, amount)?;
			},
		}
		Ok(())
	}
}

/// ...código anterior.
```

No `main.rs`:

```rust
/// ...código anterior.

fn main() {
	/// ...código anterior.

	// Aqui estão as extrinsics em nosso bloco.
	// Você pode adicionar ou remover esses com base nos módulos e chamadas que configurou.
	let block_1 = types::Block {
		header: support::Header { block_number: 1 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				/* TODO: Atualize o nome do enum para corresponder ao que é gerado com a macro. */
				call: RuntimeCall::Balances(balances::Call::Transfer {
					to: bob.clone(),
					amount: 20,
				}),
			},
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::Balances(balances::Call::Transfer { to: charlie, amount: 20 }),
			},
		],
	};

	/// ...código anterior.
}
```