Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/1bb9765d3c037b651b5b1154c2e586e3).

# Derive Debug

[Youtube](https://www.youtube.com/watch?v=fgm7YbP_-PE)

Em Rust, macros `derive` fornecem uma maneira conveniente de implementar automaticamente a funcionalidade de traits para estruturas de dados personalizadas.

## Macros

Em termos mais simples, Macros são códigos Rust que escrevem mais códigos Rust.

As macros podem tornar seu código mais fácil de ler, ajudar a evitar repetição e até permitir que você crie suas próprias regras especiais para codificar em Rust.

Usaremos (mas não escreveremos) macros extensivamente perto do final deste tutorial, e você verá o quão poderosas elas podem ser.

Por enquanto, trate-as como "mágica".

## Traits

Pense nas traits em Rust como regras compartilhadas para diferentes tipos. Elas permitem que você defina um conjunto de coisas que os tipos devem ser capazes de fazer. Dessa forma, você pode garantir que diferentes partes do seu código sigam as mesmas regras.

Dê uma olhada neste [exemplo](https://doc.rust-lang.org/rust-by-example/trait.html) ou releia o [Rust Book](https://doc.rust-lang.org/book/ch10-02-traits.html) se precisar relembrar sobre Traits.

Faremos e usaremos traits personalizadas mais adiante neste tutorial, mas saiba que neste passo `#[derive(Debug)]` é uma macro que implementa a trait `Debug` para seus tipos personalizados.

### Trait Debug

A trait Debug em Rust faz parte da biblioteca padrão e é usada para imprimir e formatar valores para fins de depuração. Ela fornece uma implementação padrão através da anotação `#[derive(Debug)]`.

Por exemplo:

```rust
#[derive(Debug)]
pub struct MyStruct {
    field1: i32,
    field2: String,
}
```

Com a trait `Debug` derivada, você pode agora imprimir a `struct` no console:

```rust
let my_instance = MyStruct { field1: 42, field2: "Hello".to_string() };
println!("{:#?}", my_instance);
```

Os caracteres `:#?` ajudam a [formatar](https://doc.rust-lang.org/std/fmt/) a saída para torná-la mais legível.

## Derivar a Trait Debug para Seu Runtime

Este é um passo muito simples, mas útil!

Queremos ser capazes de imprimir o estado atual do nosso `Runtime` no final do nosso `main` para permitir que inspecionemos facilmente como ele se parece e garantir que tudo esteja funcionando como esperado.

Para fazer isso, precisamos adicionar `#[derive(Debug)]` à `struct Runtime`.

No entanto... a `struct Runtime` é composta de `system::Pallet` e `balances::Pallet`, então essas structs TAMBÉM precisam implementar a trait Debug.

Complete os `TODO`s nos diferentes arquivos do seu projeto e imprima seu runtime final no final da função `main`.

## Exercícios

No `main.rs`:

```rust
mod balances;
mod system;

// Este é o nosso Runtime principal.
// Acumula todos os diferentes pallets que queremos utilizar.
/* TODO: Adicione a macro derivada para implementar a característica `Debug` para `Runtime`. */
pub struct Runtime {
	system: system::Pallet,
	balances: balances::Pallet,
}

fn main() {
    
	/// ... código anterior.

 	//segunda transação
	runtime.system.inc_nonce(&alice);
	let _res = runtime.balances.transfer(alice, charlie, 20).map_err(|e| eprintln!("{}", e));

    /* TODO: Imprime o estado final do tempo de execução após todas as transações. */
}

```

No `balances.rs`:

```rust
use std::collections::BTreeMap;

/// Este é o Módulo de Saldos.
/// É um módulo simples que monitora quanto saldo cada conta tem nesta máquina de estados.
/* TODO: Adicione a macro derivada para implementar a característica `Debug` para `Pallet`. */
#[derive(Debug)]
pub struct Pallet {
    // Um mapeamento simples de armazenamento de contas (`String`) para seus saldos (`u128`).
    balances: BTreeMap<String, u128>,
}
```

No `system.rs`:

```rust
use std::collections::BTreeMap;

/// Este é o Pallet do Sistema.
/// Ele lida com o estado de baixo nível necessário para sua blockchain.
/* TODO: Adicione a macro derivada para implementar a característica `Debug` para `Pallet`. */
#[derive(Debug)]
pub struct Pallet {
    /// O número do bloco atual.
    block_number: u32,
    /// Um mapa de uma conta para seu nonce.
    nonce: BTreeMap<String, u32>,
}
```

### 👑 You dropped this
VOCÊ CONSEGUIU.

Isso é um grande feito, oficialmente terminamos a seção 2!
Bom trabalho :).