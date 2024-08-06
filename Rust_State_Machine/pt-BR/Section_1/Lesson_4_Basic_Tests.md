Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/9171430e3a44c42513b5c1104ef972e2).

# Teste Básico de Saldo

[Youtube](https://www.youtube.com/watch?v=Y_G6xJomcJU)

Agora que temos os conceitos básicos do nosso `Pallet` configurados, vamos realmente interagir com ele.

Para isso, voltaremos ao arquivo `main.rs` e criaremos nosso primeiro `#[test]` que utilizará o código que escrevemos até agora.

1. No seu arquivo `src/balances.rs`, adicione um novo `#[test]` chamado `fn init_balances()`:

    ```rust
    #[test]
    fn init_balances() { }
    ```

2. Para começar nosso teste, precisamos inicializar uma nova instância do nosso `Pallet`:

    ```rust
    #[test]
    fn init_balances() {
        let mut balances = balances::Pallet::new();
    }
    ```

    Observe que tornamos essa variável `mut` já que planejamos modificar nosso estado usando nossa API recém-criada.

3. Finalmente, vamos verificar se nossas APIs de leitura e escrita estão funcionando como esperado:

    ```rust
    #[test]
    fn init_balances() {
        let mut balances = balances::Pallet::new();

        assert_eq!(balances.balance("alice".to_string()), 0);
        balances.set_balance("alice".to_string(), 100);
        assert_eq!(balances.balance("alice".to_string()), 100);
        assert_eq!(balances.balance("bob".to_string()), 0);
    }
    ```

4. Podemos executar nossos testes usando `cargo test`, onde esperamos que você veja que ele passa.

Espero que neste ponto você possa começar a ver os primeiros passos da sua simples máquina de estados blockchain.

# Exercício:

No `balances.rs`:

```rust
#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn init_balances() {
        /* TODO: Crie uma variável mutável `balances`, que é uma nova instância de `Pallet`. */
        let mut balances = Pallet::new();

        /* TODO: Verifique se o saldo de `alice` começa em zero. */
        assert_eq!(balances.balance(&"alice".to_string()), 0);

        /* TODO: Defina o saldo de `alice` para 100. */
        balances.set_balance(&"alice".to_string(), 100);

        /* TODO: Verifique se o saldo de `alice` agora é 100. */
        assert_eq!(balances.balance(&"alice".to_string()), 100);

        /* TODO: Verifique se o saldo de `bob` não mudou e é 0. */
        assert_eq!(balances.balance(&"bob".to_string()), 0);
    }
}
```

Compartilhe seu [#progress](https://discord.com/channels/898706705779687435/980906289968345128) no Discord e faça o dia do Yan melhor!