Antes de prosseguir, verifique se sua resposta corresponde à [solução da etapa anterior aqui](https://gist.github.com/nomadbitcoin/fe73ad648ad4cd64c782fdc85ceb5791).

Fantástico! Agora você entende a importância do Estado de Gênesis e está pronto para dar os primeiros passos em seu blockchain!

# Tipos Genéricos e Configuráveis

[Youtube](https://youtu.be/uXEoRoeoTg8?si=NnhqrqECI2N947pz)

Nesta seção, vamos aproveitar todo o poder do Rust para criar um Runtime genérico e configurável.

Não haverá mudanças lógicas reais nos próximos passos.

Em vez disso, vamos gradualmente abstrair os tipos concretos definidos em nossos Pallets e, em vez disso, estruturar nosso código para lidar puramente com tipos genéricos.

No final da seção, você terá um projeto cuja estrutura espelha exatamente o que é encontrado no Polkadot SDK e entenderá como tudo funciona.

# Usando Tipos Nomeados

Até agora, estivemos codificando tipos brutos diretamente em nossas structs e definições de funções.

Já existem exemplos onde isso pode ser confuso, por exemplo, se você vê uma função aceitar um parâmetro `u32`, é um `blocknumber` ou um `nonce`?

Para tornar nosso código mais claro, vamos extrair todos os nossos tipos brutos e definir tipos nomeados personalizados para nossas structs e funções.

Nos Pallets de Saldos e Sistema, precisamos definir os seguintes tipos:

1. `type AccountId = String;`
2. `type Balance = u128;`
3. `type Nonce = u32;`
4. `type BlockNumber = u32;`

Observe que extrair esses tipos para definições de tipos comuns também nos permite atualizar os tipos mais facilmente, se assim escolhermos.

À medida que avançamos neste tutorial, mostraremos como podemos tornar essas definições de tipos ainda mais flexíveis e personalizáveis no contexto da construção de um SDK de blockchain para desenvolvedores como você.

# Exercício:

### Criar Tipos Personalizados

Siga os `TODO`s no template para adicionar essas definições de tipo a cada um dos seus Pallets e atualize todas as suas structs e funções para usar esses tipos.

No `balances.rs`:
```rust
use std::collections::BTreeMap;

/*
  TODO: Defina os tipos comuns usados ​​nesta palete:
  - `ID da conta`
  - `Equilíbrio`

  Em seguida, atualize esta paleta para usar esses tipos comuns.
*/

/// Este é o Módulo de Saldos.
/// É um módulo simples que monitora quanto saldo cada conta tem neste máquina de estado.
#[derive(Debug)]
pub struct Pallet {
	// Um ​​mapeamento simples de armazenamento de contas (`String`) para seus saldos (`u128`).
	balances: BTreeMap<String, u128>,
}
```
No `system.rs`:
```rust
use std::collections::BTreeMap;

/*
	TODO: Defina os tipos comuns usados ​​nesta palete:
		- `AccountID`
		- `BlockNumber`
		- `Nonce`

	Em seguida, atualize esta paleta para usar esses tipos comuns.
*/

/// Este é o Palete do Sistema.
/// Ele lida com o estado de baixo nível necessário para seu blockchain.
#[derive(Debug)]
pub struct Pallet {
	/// O número do bloco atual.
	block_number: u32,
	/// Um ​​mapa de uma conta para seu nonce.
	nonce: BTreeMap<String, u32>,
}
```