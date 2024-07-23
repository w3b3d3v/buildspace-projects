Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/e3330a586bff7e01cc4ab920cab026be).

# Tornando o Pallet de Saldos Configurável

Não há nada novo para aprender nesta etapa, apenas repetindo o mesmo processo que fizemos para o Pallet de Sistema no Pallet de Saldos.

Neste caso, nossa trait `Config` terá apenas dois tipos associados: `AccountId` e `Balance`.

Para esta etapa, tente evitar olhar a solução e, em vez disso, consulte as alterações que você fez para tornar o Pallet de Sistema configurável.

## Exercícios:

1. Defina a trait `Config` que terá seus tipos associados.
2. Certifique-se de que esses tipos tenham suas restrições de traits definidas em `Config`.
3. Atualize sua struct `Pallet` para usar `T: Config` e referencie seus tipos usando a sintaxe `T::`.
4. Atualize todas as suas funções para usar a sintaxe `T::`.
5. Atualize seu teste, criando uma struct `TestConfig`, implementando `Config` para ela e usando-a para instanciar sua struct `Pallet`.
6. Vá para o seu arquivo `main.rs` e implemente `balances::Config` para a struct `Runtime`.
7. Atualize sua definição de `Runtime` para instanciar `balances::Pallet` com `Self`.

Se você chegou até aqui, acho que é justo dizer que você superou a parte mais difícil deste tutorial e a parte mais difícil de usar Rust no Substrate.

É importante dar um passo para trás e lembrar que, embora essas abstrações tornem seu código um pouco mais complicado de entender completamente, também tornam seu código extremamente flexível, sem custo para desempenho e segurança graças ao compilador Rust.

No `main.rs`:

```rust
mod balances;
mod system;

// Estes são os tipos concretos que usaremos em nossa máquina de estados simples.
// Os módulos são configurados diretamente para esses tipos e satisfazem todos os nossos
// requisitos de característica.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
}

// Este é o nosso Runtime principal.
// Acumula todos os diferentes paletes que queremos utilizar.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet<Self>,
	balances: balances::Pallet<types::AccountId, types::Balance>,
}

impl system::Config for Runtime {
	type AccountId = types::AccountId;
	type BlockNumber = types::BlockNumber;
	type Nonce = types::Nonce;
}

/*
	TODO:
	Implemente o trait `balances::Config` que você criou em seu `Runtime`.
 	Use `Self` para satisfazer o parâmetro genérico necessário para `balances::Pallet`.
*/

impl Runtime {
	// Crie uma nova instância do Runtime principal, criando uma nova instância de cada palete.
	fn new() -> Self {
		Self { system: system::Pallet::new(), balances: balances::Pallet::new() }
	}
}
```

On `balances.rs`:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
use std::collections::BTreeMap;

/*
	TODO: Combine todos os tipos genéricos e seus limites de características em um único `pub trait Config`.
  	Quando terminar, seu `Pallet` pode simplesmente ser definido com `Pallet<T: Config>`.
*/

/// Este é o Módulo de Saldos.
/// É um módulo simples que monitora quanto saldo cada conta tem neste máquina de estado.
#[derive(Debug)]
pub struct Pallet<AccountId, Balance> {
	// Um mapeamento simples de armazenamento de contas para seus saldos.
	balances: BTreeMap<AccountId, Balance>,
}

/*
	TODO: Atualize todas essas funções para usar seu novo traço de configuração.
*/

/// ...código anterior.

#[cfg(test)]
mod tests {
	/*
		TODO: Crie uma `struct TestConfig` e implemente `super::Config` nela com tipos concretos.
  		Use esta estrutura para instanciar seu `Pallet`.
	*/

    /// ...código anterior.
}
```

Parabéns por ter chegado até aqui e superado a parte mais difícil do tutorial! Continuamos avançando.