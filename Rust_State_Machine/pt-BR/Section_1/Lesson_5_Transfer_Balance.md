Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/03914776998cb74eaa0a6adaa7edacec).

# Habilitando Transferências de Saldos

[Youtube](https://www.youtube.com/watch?v=TWcQsUKtcTY)

Agora que inicializamos e começamos a usar nosso módulo de saldos, vamos adicionar provavelmente a API mais importante: `transfer`.

## Aprender

Antes de escrever nossa função, é importante revisar alguns dos princípios de blockchain e Rust.

### Atores Maliciosos

Em um sistema de blockchain, a segurança é primordial. Atores maliciosos podem tentar explorar vulnerabilidades, como saldos insuficientes durante transferências de fundos, ou problemas de overflow/underflow. As operações de matemática segura e mecanismos de tratamento de erros do Rust ajudam a mitigar esses riscos.

### Matemática Segura

As operações de matemática segura do Rust previnem overflow e underflow. Os métodos `checked_add` e `checked_sub` retornam uma `Option` que permite lidar com possíveis erros aritméticos de forma segura.

No Rust, o tipo Option é uma parte fundamental da biblioteca padrão, projetada para lidar com cenários onde um valor pode ou não estar presente. É comumente usado em situações onde o resultado de uma operação pode ser indefinido ou ausente.

Métodos como `checked_add` e `checked_sub` retornam `Option` para indicar sucesso ou falha devido a overflow ou underflow.

```rust
let result = a.checked_add(b);
match result {
    Some(sum) => println!("Sum: {}", sum),
    None => println!("Overflow occurred."),
}
```

### Tratamento de Erros

No Rust, o tratamento de erros é uma parte integral da escrita de código robusto e seguro. O tipo Result é comumente usado para funções que podem encontrar erros durante sua execução.

O tipo Result é uma enum definida na biblioteca padrão. Ela tem duas variantes: `Ok(value)` para um resultado bem-sucedido e `Err(error)` para um erro:

```rust
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

`T` e `E` são parâmetros genéricos que permitem customizar o tipo de resultado conforme suas necessidades. Para os propósitos deste tutorial, sempre retornaremos `Ok(())` quando tudo ocorrer bem, e um `Err(&static str)` para descrever quaisquer erros com uma string básica.

Você pode então definir o tipo `Result` assim:

```rust
Result<(), &'static str>
```

### Options e Results

Você pode usar o tipo `Option` para acionar um `Err`, o que é útil quando você só quer que sua função execute quando tudo corre conforme o esperado.

Nesse contexto, queremos uma função que retorne um erro sempre que alguma operação de matemática segura retornar `None`.

Para isso, podemos encadear `ok_or` juntamente com `?` diretamente após a operação de matemática segura, assim:

```rust
let new_from_balance = from_balance
    .checked_sub(amount)
    .ok_or("Not enough funds.")?;
```

Se `checked_sub` retornar `None`, retornaremos um `Err` com a mensagem `"Not enough funds."` que pode ser exibida ao usuário. Caso contrário, se `checked_sub` retornar `Some(value)`, atribuiremos `new_from_balance` diretamente a esse valor.

Nesse caso, estamos escrevendo código que trata completamente o tipo `Option` de maneira segura e ergonômica.

# Exercícios:

1. Crie uma função de transferência segura e simples no seu Pallet de Saldos.
2. Crie um teste mostrando que tudo está funcionando conforme o esperado, incluindo o tratamento de erros.

No `balances.rs`:

```rust
impl Pallet {
    /// ... código anterior.

    /// Transfere `amount` de uma conta para outra.
    /// Esta função verifica se `caller` tem pelo menos `amount` de saldo para transferir,
    /// e se não ocorrem overflow/underflow matemáticos.
    pub fn transfer(
        &mut self,
        caller: String,
        to: String,
        amount: u128,
    ) -> Result<(), &'static str> {
        /* TODO:
            - Obter o saldo da conta `caller`.
            - Obter o saldo da conta `to`.
            - Usar matemática segura para calcular um `new_caller_balance`.
            - Usar matemática segura para calcular um `new_to_balance`.
            - Inserir o novo saldo de `caller`.
            - Inserir o novo saldo de `to`.
        */

        Ok(())
    }
}
```

Também no `balances.rs`:

```rust
mod tests {
     /// ... código anterior.

    #[test]
    fn transfer_balance() {
        /* TODO: Crie um teste que verifique o seguinte:
            - Que `alice` não pode transferir fundos que ela não possui.
            - Que `alice` pode transferir fundos para `bob` com sucesso.
            - Que o saldo de `alice` e `bob` esteja atualizado corretamente.
        */
    }
}
```

E lembre-se, estamos disponíveis para responder a qualquer pergunta no [Discord](https://discord.com/channels/898706705779687435/980904325763186788).

### 🌱 Crescendo Forte
VOCÊ ESTÁ CHEGANDO LÁ.

A Seção 1 está concluída! Você está construindo uma base sólida. Muito bem :).