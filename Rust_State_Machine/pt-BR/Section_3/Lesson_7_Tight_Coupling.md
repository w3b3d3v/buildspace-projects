Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/0c6e73f0bdd65cac4eea182985930d7a).

# Acoplamento Apertado

[Youtube](https://youtu.be/UgJkeZ7E-Fw?si=Y6zozHd9uLJ5Ev6W)

Você pode ter notado alguma redundância ao tornar nossos pallets genéricos e configuráveis. Ambos os pallets definiram um tipo `AccountId`, e tecnicamente poderíamos definir seu tipo concreto de maneira diferente!

Não gostaríamos disso em uma blockchain de produção real. Em vez disso, gostaríamos de definir tipos comuns em um único lugar e usá-los em todos os lugares.

## Herança de Traits

Rust tem a capacidade de herdar traits umas das outras. Ou seja, para você implementar alguma trait, você também precisa implementar todas as traits das quais ela herda.

Vamos ver alguns exemplos.

### Funções de Traits

Podemos estender nosso exemplo anterior para mostrar o que a herança de traits faz com as funções:

```rust
pub trait GetName {
    // retorna uma string representando o nome do objeto
    fn name() -> String;
}

pub trait SayName: GetName {
    // imprimirá o nome de `name()` no console
    fn say_name() {
        println!("{}", Self::name());
    }
}
```

Note como na definição da `trait SayName`, referenciamos `GetName` após um dois-pontos. Isso significa que para implementar `SayName`, seu objeto também deve implementar `GetName`. Observe que poderíamos até programar uma implementação "padrão" de `get_name` usando a função `Self::name()`.

Então, quando implementamos essas traits, fica assim:

```rust
struct Shawn;
impl GetName for Shawn {
    fn name() -> String {
        return "shawn".to_string();
    }
}

impl SayName for Shawn {}
```

Poderíamos escolher implementar nossa própria versão da função `SayName`, por exemplo assim:

```rust
impl SayName for Shawn {
    fn say_name() {
        println!("My name is {}!", Self::name());
    }
}
```

Mas não precisamos fazer isso. O que precisamos fazer é garantir que `GetName` esteja implementado para `Shawn` ou não será possível usar a trait `SayName`. Novamente, não usaremos isso em nosso tutorial, mas é bom ver exemplos de como isso pode ser usado.

### Tipos Associados

Em vez de redefinir `type AccountId` em cada Pallet que precisa dele, e se o definíssemos apenas em `system::Config`, e herdássemos esse tipo em outras configurações de Pallet?

Vamos ver como isso ficaria:

```rust
pub trait Config: crate::system::Config {
    type Balance: Zero + CheckedSub + CheckedAdd + Copy;
}
```

Aqui você pode ver que nossa trait `balances::Config` está herdando da trait `crate::system::Config`. Isso significa que todos os tipos definidos por `system::Config`, incluindo `AccountId`, são acessíveis através da trait `balances::Config`. Por causa disso, não precisamos redefinir o tipo `AccountId` em `balances::Config`.

No ecossistema do Polkadot SDK, chamamos isso de "acoplamento apertado" porque um runtime que contém o Pallet de Saldos deve conter também o Pallet de Sistema. Em um sentido, esses dois pallets estão fortemente acoplados um ao outro. Na verdade, com Substrate, todos os pallets estão fortemente acoplados ao Pallet de Sistema, porque o Pallet de Sistema fornece todos os meta-tipos para o seu sistema de blockchain.

## Exercícios:

### Acople Firmemente os Saldos ao Sistema

Vamos remover a definição redundante de `AccountId` da `Config` do Pallet de Saldos.

1. Herde a trait `crate::system::Config` na trait `balances::Config`.
2. Remova o tipo `AccountId` da definição de `balances::Config`.
3. Implemente `crate::system::Config` para `TestConfig`.
4. No `main.rs`, simplesmente remova `type AccountId` de `balances::Config`.

No `main.rs`:

```rust
/// ...código anterior.

impl balances::Config for Runtime {
    /* TODO: Depois de herdar do trait `system::Config`, você não precisará de `AccountId` aqui. */
	type AccountId = types::AccountId;
    type Balance = types::Balance;
}

/// ...código anterior.
```

No `balances.rs`:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
use std::collections::BTreeMap;

/// A característica de configuração do Módulo Balances.
/// Contém os tipos básicos necessários para lidar com saldos.
/*
    TODO:
    Acople firmemente os saldos ao pallet do sistema, herdando a característica `system::Config`.
    Depois disso, você não precisará redefinir o tipo `AccountId` aqui.
*/
pub trait Config {
	/// Um ​​tipo que pode identificar uma conta em nossa máquina de estado.
  	/// Em um blockchain real, você gostaria que esta fosse uma chave pública criptográfica.
	type AccountId: Ord + Clone;
	/// Um ​​tipo que pode representar o saldo de uma conta.
  	/// Geralmente este é um grande número inteiro sem sinal.
	type Balance: Zero + CheckedSub + CheckedAdd + Copy;
}

/// ...código anterior.

#[cfg(test)]
mod tests {
    struct TestConfig;

	/* TODO: Implemente `crate::system::Config` para `TestConfig` para fazer seus testes funcionarem novamente. */

	impl super::Config for TestConfig {
		type AccountId = String;
		type Balance = u128;
	}

	/// ...código anterior.
    
}
```

### 🌟 Você é uma estrela

VOCÊ CONSEGUIU.

Você completou com sucesso a seção 3! Trabalho fantástico :).