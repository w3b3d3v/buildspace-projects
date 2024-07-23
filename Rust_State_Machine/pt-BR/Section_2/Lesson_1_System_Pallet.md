Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/afd212bee56108f0cd87c712ad5d3fdf).

Parabéns por chegar à seção 2! ;)

# O Sistema e o Runtime

Nesta seção, você criará o Pallet de Sistema, um Pallet de baixo nível para gerenciar o estado básico da blockchain.

Em seguida, você integrará tanto o Pallet de Saldos quanto o Pallet de Sistema na sua função de transição de estado, chamada Runtime.

# Introduzindo o Pallet de Sistema

Basicamente, completamos a criação de um Pallet de Saldos básico. Este é o pallet com o qual a maioria dos usuários irá interagir.

No entanto, sua blockchain geralmente precisa acompanhar muitas outras peças de dados para funcionar corretamente.

Para isso, criaremos um novo pallet chamado Pallet de Sistema.

## O que é o Pallet de Sistema?

O Pallet de Sistema é um "meta"-pallet que armazena todos os metadados necessários para a sua blockchain funcionar. Por exemplo, o número atual do bloco ou o nonce dos usuários na sua blockchain.

Este pallet não precisa expor nenhuma função para os usuários finais, mas ainda pode desempenhar um papel importante na nossa função geral de transição de estado.

Veremos a importância do Pallet de Sistema evoluir à medida que avançamos nos passos de sua construção.

## Criando o Pallet de Sistema

1. Crie um novo arquivo `src/system.rs` em seu projeto.
2. Copie o template inicial fornecido e complete os passos descritos pelo código do template.
3. Importe o módulo `system` no seu arquivo `main.rs`.

Você notará que as instruções aqui são bastante breves. Você já fez todos esses passos antes, então já deve estar familiarizado com tudo que precisa para completar esta etapa.


## Exercícios:

Crie o arquivo `src/system.rs`:
```rust
/* TODO: Talvez seja necessário atualizar suas importações. */

/// Este é o Palete do Sistema.
/// Ele lida com o estado de baixo nível necessário para seu blockchain.
pub struct Pallet {
	/// O número do bloco atual.
	/* TODO: Cria um campo `block_number` que armazena um `u32`. */
	/// Um ​​mapa de uma conta para seu nonce.
	/* TODO: Crie um campo `nonce` que seja um `BTreeMap` de `String` para `u32`. */
}

impl Pallet {
	/// Crie uma nova instância do System Pallet.
	pub fn new() -> Self {
		/* TODO: Retorne uma nova instância da struct `Pallet`. */
	}
}
```
On `main.rs`:
```rust
mod balances;
/* TODO: Importe seu novo módulo `system`. */

fn main() {
	println!("Hello, world!");
}
```

Vai ser épico. :) Vamos fazer isso!