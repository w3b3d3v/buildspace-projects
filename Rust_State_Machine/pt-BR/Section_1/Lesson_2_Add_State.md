# Adicionando Estado ao Nosso Pallet

[Youtube](https://www.youtube.com/watch?v=CCxkdf2VX8w)

Então, vamos adicionar algum estado simples ao nosso módulo `balances.rs`.

Podemos fazer isso adicionando campos na nossa struct `Pallet`.

Para um sistema de saldo, realmente só precisamos acompanhar uma coisa: **quanto saldo cada usuário tem em nosso sistema**.

Para isso, usaremos um `BTreeMap`, que podemos importar da biblioteca `std` do Rust.

`Maps` são objetos simples de `chave -> valor`, permitindo-nos definir um armazenamento de tamanho arbitrário onde podemos mapear algum identificador de usuário (`chave`) para o saldo da conta deles (`valor`).

1. Importe o objeto `BTreeMap`.

    ```rust
    use std::collections::BTreeMap;
    ```

2. Crie um campo `balances` em `Pallet` usando o `BTreeMap`.
   Para a `chave`, usaremos uma string estática simples por enquanto. Dessa forma, podemos acessar usuários como `"alice"`, `"bob"`, etc. Isso será alterado no futuro.

   Para o `valor`, usaremos um `u128`, que é o maior tipo suportado nativamente no Rust. Isso permitirá que nossos usuários tenham saldos muito grandes se quisermos.

   No final, isso ficará assim:

    ```rust
    pub struct Pallet {
        balances: BTreeMap<String, u128>,
    }
    ```

3. Finalmente, precisamos de uma maneira de inicializar este objeto e seu estado. Para isso, vamos implementar uma função na `Pallet` chamada `fn new()`:

    ```rust
    impl Pallet {
        pub fn new() -> Self {
            Self {
                balances: BTreeMap::new(),
            }
        }
    }
    ```

## Exercício:

No `balances.rs`:

```rust
use std::collections::BTreeMap;

pub struct Pallet {
    // Um armazenamento simples mapeando contas (`String`) para seus saldos (`u128`).
    /* TODO: Adicionar um campo `balances` que é um `BTreeMap` de `String` para `u128`. */
}

impl Pallet {
    /// Cria uma nova instância do módulo de saldos.
    pub fn new() -> Self {
        /* TODO: Retornar uma nova instância da struct `Pallet`. */
        unimplemented!() // Remova esta linha após implementar a função
    }
}
```

Você pode confirmar neste ponto que tudo ainda deve estar compilando, e que você não cometeu nenhum erro pequeno.

Em seguida, vamos realmente começar a **usar** este módulo.

## Notas

É importante notar que isso **NÃO** é como o armazenamento de Pallet funciona com o Polkadot SDK, mas apenas uma emulação simples dos comportamentos.

No Polkadot SDK, existe uma camada de armazenamento separada que gerencia um banco de dados de chave-valor adequado que contém todas as informações (passadas e presentes) do nosso sistema de blockchain. Existem abstrações que se parecem e se comportam como um `BTreeMap` no Polkadot SDK, mas a lógica subjacente que mantém esses dados é muito mais complexa.

Usar campos simples em uma struct mantém este projeto simples e ilustra que cada Pallet realmente deve gerenciar seu próprio armazenamento. No entanto, essa simplificação também leva a problemas se você projetar sistemas mais complexos onde múltiplos pallets interagem entre si.

Não teremos nenhuma interação entre pallets neste projeto, no entanto, isso é definitivamente viável com o Polkadot SDK e um banco de dados adequado.