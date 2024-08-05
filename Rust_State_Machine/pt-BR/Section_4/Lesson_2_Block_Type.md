Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/a6964532490ce5fbda97a606c07e6f56).

# Criar Seu Tipo de Bloco

[Youtube](https://youtu.be/0ohriBEmJks?si=U8AjrqPhtOosNuwM)

O módulo de suporte forneceu um monte de tipos genéricos que podem ser personalizados para nossa máquina de estados simples. Para realmente começar a usá-los, precisamos definir versões concretas desses tipos usando nossos outros tipos concretos.

## Chamada do Runtime

Você verá que o template fornece um `enum RuntimeCall` vazio que expandiremos mais tarde. Este é um objeto que supostamente representa todas as várias chamadas expostas pela sua blockchain para os usuários e o mundo exterior. Precisamos simular este enum nesta etapa para que ele possa ser usado para construir um tipo `Extrinsic` concreto.

Por enquanto, há apenas a função `transfer` exposta pelo Pallet de Saldos, mas adicionaremos mais antes que este tutorial seja concluído e encontraremos maneiras de automatizar a criação do nosso `RuntimeCall`.

Você pode acessar este tipo dentro de `mod types` com `crate::RuntimeCall`.

## Exercícios:

### Construindo o Tipo de Bloco

É hora de definir o tipo `Block` concreto que usaremos para aprimorar nossa máquina de estados simples.

1. Usando o enum `RuntimeCall` e o tipo `AccountId`, você pode definir um tipo `Extrinsic` concreto.
2. Usando o tipo `BlockNumber`, você pode definir um tipo `Header` concreto.
3. Usando os tipos concretos `Header` e `Extrinsic`, você pode definir um tipo `Block` concreto.

Como você pode ver, o `Block` é composto por camadas de tipos genéricos, permitindo que toda a estrutura seja flexível e personalizável às nossas necessidades.

Preste atenção às definições de tipos genéricos para garantir que você use todos os parâmetros genéricos corretos em todos os lugares certos.

No `main.rs`:

```rust
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	pub type BlockNumber = u32;
	pub type Nonce = u32;
	/* TODO: Defina um tipo `Extrinsic` concreto usando `AccountId` e `RuntimeCall`. */
  	/* TODO: Defina um tipo concreto de `Header` usando `BlockNumber`. */
  	/* TODO: Defina um tipo concreto de `Block` usando `Header` e `Extrinsic`. */
}

// Estas são todas as chamadas que estão expostas ao mundo.
// Observe que é apenas um acúmulo das chamadas expostas por cada módulo.
pub enum RuntimeCall {
    // TODO: Ainda não implementado.
}
```