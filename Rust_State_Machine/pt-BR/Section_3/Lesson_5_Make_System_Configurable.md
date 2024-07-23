Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/ccc53d36900918042c4a7c665fcb9804).

# Tornando o Sistema Configurável

[Youtube](https://youtu.be/nmoTi2SvEsU?si=c-lcw6g5aBjS0Bvx)

Temos mais um passo a dar para tornar nosso Runtime o mais genérico e configurável possível.

Para fazer isso, precisaremos aproveitar as traits.

## Traits Personalizadas

Já usamos traits fornecidas para nós a fim de tornar nossos tipos genéricos.

Vamos dar uma olhada rápida em como você pode definir uma trait personalizada:

```rust
pub trait Config {}
```

As traits podem conter duas coisas:

1. Funções que devem ser implementadas pelo tipo
2. Tipos associados

### Funções Personalizadas

O uso mais óbvio das traits é definir funções personalizadas.

Vamos supor que queremos expor uma função que retorne o nome de algo.

Você poderia criar uma trait `GetName`:

```rust
pub trait GetName {
	fn name() -> String;
}
```

Então, você poderia implementar essa trait para qualquer objeto.

```rust
struct Shawn;
impl GetName for Shawn {
	fn name() -> String {
		return "shawn".to_string();
	}
}
```

E então chamar essa função no objeto que a implementa.

```rust
fn main() {
	println!("{}", Shawn::name());
}
```

Não usaremos essa característica das traits em nossa blockchain simples, mas há muitos casos de uso para isso ao desenvolver sistemas de blockchain mais complexos.

### Tipos Associados

A outra coisa que você pode fazer com traits é definir Tipos Associados.

Isso é abordado no [capítulo 19 do Livro de Rust](https://doc.rust-lang.org/book/ch19-03-advanced-traits.html) sob "Advanced Traits".

Vamos aprender esse conceito primeiro olhando para o problema que estamos tentando resolver.

Até agora, nosso código de blockchain simples parece perfeitamente bom com tipos genéricos. No entanto, vamos imaginar que nossa blockchain se torna mais e mais complexa, exigindo mais e mais tipos genéricos.

Por exemplo:

```rust
pub struct Pallet<AccountId, BlockNumber, BlockLength, BlockWeight, Hash, Nonce, Runtime, Version, ...> {
	// um monte de coisas
}
```

Imagine que toda vez que você quisesse instanciar essa struct, você precisaria preencher cada um desses tipos. Bem, os sistemas se tornam tão complexos, e mais, e a capacidade de abstrair esses tipos um nível mais adiante pode realmente simplificar seu código e torná-lo muito mais legível.

Para isso, usaremos uma trait com um monte de tipos associados:

```rust
pub trait Config {
	type AccountId: Ord;
	type BlockNumber: Zero + One + AddAssign + Copy;
	type Nonce: Zero + One + Copy;
	// e mais, se necessário
}
```

Então, podemos definir nosso tipo genérico usando um único parâmetro genérico!

```rust
pub struct Pallet<T: Config> {
	block_number: T::BlockNumber,
	nonce: BTreeMap<T::AccountId, T::Nonce>,
}
```

e implementar funções usando:

```rust
impl<T: Config> Pallet<T> {
	// funções usando tipos de T aqui
}
```

Vamos tentar entender essa sintaxe rapidamente.

1. Há um tipo genérico `T`. `T` não tem um nome significativo porque representa um monte de coisas, e isso é a convenção mais comumente usada em Rust.
2. `T` é obrigado a implementar a trait `Config`, que definimos anteriormente.
3. Porque `T` implementa `Config`, e `Config` tem os tipos associados `AccountId`, `BlockNumber`, e `Nonce`, podemos acessar esses tipos assim:
	- `T::AccountId`
	- `T::BlockNumber`
	- `T::Nonce`

Não há diferença significativa entre o que tínhamos antes com 3 parâmetros genéricos e um único parâmetro genérico representado por uma trait `Config`, mas certamente torna tudo mais escalável, fácil de ler e fácil de configurar.

Neste contexto, chamamos a trait `Config` porque é usada para configurar todos os tipos para nosso Pallet.

### Implementando a Trait Config

Vamos completar isso mostrando como você pode realmente implementar e usar a trait `Config`.

Assim como antes, precisamos de algum objeto que implemente essa trait. No nosso caso, podemos usar a struct `Runtime` em si.

```rust
impl system::Config for Runtime {
	type AccountId = String;
	type BlockNumber = u32;
	type Nonce = u32;
}
```

Então, ao definir o `system::Pallet` dentro do `Runtime`,  podemos usar a seguinte sintaxe:

```rust
pub struct Runtime {
	system: system::Pallet<Self>,
}
```

Aqui estamos basicamente dizendo que `Pallet` usará `Runtime` como seu tipo genérico, mas isso é definido dentro do `Runtime`, então nos referimos a ele como `Self`.

Ufa. Isso foi bastante.

Vamos praticar tudo o que você aprendeu para criar a trait `Config` para o seu Pallet de Sistema e, em seguida, configurar o pallet para o `Runtime` no `main.rs`.

# Exercícios:

### Torne Seu Sistema Configurável

1. Defina a trait `Config` que terá seus 3 tipos associados `AccountId`, `BlockNumber` e `Nonce`.
2. Certifique-se de que esses tipos tenham suas restrições de traits definidas em `Config`.
3. Atualize sua struct `Pallet` para usar `T: Config` e referencie seus tipos usando a sintaxe `T::`.
4. Atualize todas as suas funções para usar a sintaxe `T::`.
5. Atualize seu teste, criando uma struct `TestConfig`, implementando `Config` para ela e usando-a para instanciar sua struct `Pallet`.
6. Vá para o seu arquivo `main.rs` e implemente `system::Config` para a struct `Runtime`.
7. Atualize sua definição de `Runtime` para instanciar `system::Pallet` com `Self`.

Novamente, este é um grande passo para novos desenvolvedores Rust e um local comum onde as pessoas podem ficar muito confusas.

Você terá a oportunidade de fazer todo esse processo novamente para o Pallet de Saldos, então não tenha medo de espiar a solução desta vez se não conseguir fazer seu código funcionar.

Realmente dedique tempo para entender esta etapa, o que está acontecendo e o que toda essa sintaxe significa para o Rust.

Lembre-se de que Rust é uma linguagem completamente segura quanto a tipos, então, no final do dia, todos esses tipos genéricos e configurações precisam fazer sentido para o compilador Rust.

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

/*
	TODO:
	Implemente a característica `system::Config` que você criou em seu `Runtime`.
  	Use `Self` para satisfazer o parâmetro genérico necessário para `system::Pallet`.
*/

// Este é o nosso Runtime principal.
// Acumula todos os diferentes paletes que queremos utilizar.
```

On `system.rs`:

```rust
use core::ops::AddAssign;
use num::traits::{One, Zero};
use std::collections::BTreeMap;

/*
	TODO: Combine todos os tipos genéricos e seus limites de características em um único `pub trait Config`.
  	Quando terminar, seu `Pallet` pode simplesmente ser definido com `Pallet<T: Config>`.
*/

/// Este é o Palete do Sistema.
/// Ele lida com o estado de baixo nível necessário para seu blockchain.
#[derive(Debug)]
pub struct Pallet<AccountId, BlockNumber, Nonce> {
	/// O número do bloco atual.
	block_number: BlockNumber,
	/// Um mapa de uma conta até seu nonce.
	nonce: BTreeMap<AccountId, Nonce>,
}

/*
	TODO: Atualize todas essas funções para usar seu novo traço de configuração.
*/

/// ...código anterior.

#[cfg(test)]
mod test {
	/*
		TODO: Crie uma `struct TestConfig` e implemente `super::Config` nela com tipos concretos.
  		Use esta estrutura para instanciar seu `Pallet`.
	*/

	/// ...código anterior.
}
```

Se você tiver alguma dúvida ou precisar de mais assistência para implementar traits personalizadas ou configurar seu Sistema, sinta-se à vontade para pedir suporte no canal [#🆘・section-3](https://discord.com/channels/898706705779687435/980905695689998366) no Discord. Estamos aqui para apoiá-lo! 🌟