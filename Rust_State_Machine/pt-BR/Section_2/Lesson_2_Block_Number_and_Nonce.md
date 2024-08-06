Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/1a3548177e89c4d691f78b691f1a38a1).

# Tornando Seu Sistema Funcional

[Youtube](https://www.youtube.com/watch?v=_qkR8NAzk1A)

Nós estabelecemos novamente a base de um novo Pallet.

Vamos adicionar funções que o tornem útil.

## Número do Bloco

O número do bloco da sua blockchain é armazenado no Pallet de Sistema, e o Pallet de Sistema precisa expor funções que nos permitam acessar e modificar o número do bloco.

Para isso, precisamos de duas funções simples:

- `fn block_number` - uma função que retorna o número do bloco atualmente armazenado.
- `fn inc_block_number` - uma função que incrementa o número do bloco atual em um.

Isso deve ser tudo o que uma blockchain básica precisa para funcionar.

## Nonce

O `nonce` representa "um número usado uma vez".

Nesse contexto, cada usuário na sua blockchain tem um `nonce` que dá um valor único a cada transação que o usuário submete à blockchain.

Lembre-se que blockchains são sistemas descentralizados e distribuídos, e transações não têm, por natureza, uma ordem determinística. Para um usuário, podemos atribuir uma ordem a diferentes transações usando esse nonce para acompanhar quantas transações o usuário executou na blockchain.

Para isso, usamos novamente um `BTreeMap` para dar a cada usuário seu próprio contador de `nonce`.

Nossa blockchain simples não usará esse valor, mas para o exemplo, manteremos o controle dele criando uma função `inc_nonce`. Se você estivesse criando uma blockchain mais complexa, o `nonce` do usuário se tornaria uma parte importante do seu sistema.

## Matemática Segura?

Acabamos de explicar a importância de usar matemática segura ao escrever o Pallet de Saldos.

Nesse contexto, é fácil ver como um usuário poderia fornecer entradas maliciosas e causar `underflows` ou `overflows` simples se nosso sistema não verificasse a matemática.

No entanto, você verá nos templates fornecidos que essas novas funções no Pallet de Sistema não retornam um resultado e, portanto, não fornecem tratamento de erros.

Isso está ok?

Como você notará, os itens de armazenamento `blocknumber` e `nonce` só fornecem APIs para incrementar em um. No nosso Sistema, ambos esses números são representados por `u32`, o que significa que mais de 4,2 bilhões de chamadas a essas funções precisam ocorrer antes que um overflow aconteça.

Assumindo que um usuário faça uma transação a cada bloco e que um novo bloco seja gerado a cada 6 segundos, levaria mais de 800 anos para que ocorresse um overflow. Então, nesta situação, estamos preferindo uma API que não requer tratamento de erros em vez de uma que o faça.

No final do dia, esta é uma decisão de design e uma preferência deixada ao desenvolvedor. Este tutorial escolhe essa API porque é exatamente a API exposta pelo Substrate e pelo Polkadot SDK. Não há nada de errado em fazer essas funções tratarem erros, então sinta-se à vontade para fazer isso, se preferir.

## Construa Seu Pallet de Sistema

Siga as instruções no template para completar:

1. `fn block_number`
2. `fn inc_block_number`
3. `fn inc_nonce`

Em seguida, escreva testes que verifiquem que essas funções funcionam conforme o esperado e que seu estado está corretamente atualizado.

## Exercícios

No `system.rs`:

```rust
use std::collections::BTreeMap;

/// Este é o Pallet de Sistema.
/// Ele lida com o estado de baixo nível necessário para sua blockchain.
pub struct Pallet {
    /// O número atual do bloco.
    block_number: u32,
    /// Um mapa de uma conta para seu nonce.
    nonce: BTreeMap<String, u32>,
}

impl Pallet {
    /// Cria uma nova instância do Pallet de Sistema.
    pub fn new() -> Self {
        Self { block_number: 0, nonce: BTreeMap::new() }
    }

    /// Obtém o número atual do bloco.
    pub fn block_number(&self) -> u32 {
        /* TODO: Retorne o número do bloco atual. */
        unimplemented!()
    }

    /// Esta função pode ser usada para incrementar o número do bloco.
    /// Aumenta o número do bloco em um.
    pub fn inc_block_number(&mut self) {
        /* TODO: Aumenta o número do bloco atual em um. */
        unimplemented!()
    }

    /// Incrementa o nonce de uma conta. Isso nos ajuda a acompanhar quantas transações cada conta fez.
    pub fn inc_nonce(&mut self, who: &String) {
        /* TODO: Obtenha o nonce atual de `who` e aumente-o em um. */
        unimplemented!()
    }
}

#[cfg(test)]
mod test {
    #[test]
    fn init_system() {
        /* TODO: Crie um teste que verifique o seguinte:
            - Aumenta o número do bloco atual.
            - Aumente o nonce de `alice`.
            - Verifique se o número do bloco é o que esperamos.
            - Verifique se o nonce de `alice` é o que esperamos.
        */
    }
}
```

## 🚨 Relatório de Progresso

Você conseguiu criar um código bacana? Não se esqueça de compartilhar uma captura de tela no canal [#progress](https://discord.com/channels/898706705779687435/980906289968345128) no Discord!