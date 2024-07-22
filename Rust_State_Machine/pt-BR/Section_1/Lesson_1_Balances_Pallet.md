Já vi muitos tutoriais complicados, então vamos tentar manter este simples e organizado. 😃 Espero que você goste!

# O Pallet de Saldos

[Youtube](https://youtu.be/49NApUfg-w8?si=L1n02_Fg-NT45cVn)

Nesta seção, vamos construir a primeira lógica para nossa máquina de estados: um Pallet de Saldos.

Este Pallet gerenciará os saldos dos usuários e permitirá que eles transfiram tokens entre si.

Ao longo do caminho, você aprenderá sobre matemática segura, opções, tratamento de erros e muito mais.

Ao final desta seção, você terá projetado a lógica de uma criptomoeda simples.

# Criando um Pallet de Saldos

Como mencionado anteriormente, no coração de uma blockchain está uma máquina de estados.

Podemos criar uma máquina de estados muito ingênua usando abstrações simples de Rust, e através disso aprender sobre Rust no contexto de blockchains.

Queremos manter nosso código organizado, então não vamos realmente começar a construir no arquivo `main.rs`, mas sim em módulos Rust separados. Podemos pensar no arquivo `main.rs` como a cola que une tudo, e veremos isso ao longo deste projeto.

"Pallet" é um termo específico do Polkadot SDK, que se refere a módulos Rust que contêm lógica específica para o runtime da sua blockchain. Vamos começar a usar esse termo aqui porque o que construímos aqui se assemelhará muito ao que você verá com o Polkadot SDK.

## Saldos

Praticamente toda blockchain tem lógica que lida com os saldos dos usuários nessa blockchain.

Este Pallet dirá: quanto saldo cada usuário tem, fornecerá funções que permitem aos usuários transferir esses saldos e até mesmo algumas funções de baixo nível para permitir que seu sistema de blockchain manipule esses saldos, se necessário. Pense, por exemplo, se você quiser cunhar novos tokens que não existem ainda.

Este é um ótimo ponto de partida, e o primeiro Pallet que vamos construir.

## Criando uma Struct

1. Crie um novo arquivo na pasta `src` chamado `balances.rs`

	```bash
	touch src/balances.rs
	```

2. Neste arquivo, crie uma `struct`, que atuará como estado e ponto de entrada para este módulo:

	```rust
	pub struct Pallet {}
	```

3. Agora volte para `src/main.rs` e importe este novo módulo, que incluirá toda a lógica dentro dele:

	```rust
	mod balances;
	```

4. Se executarmos seu programa agora, você verá que ele ainda é compilado e executado, mas poderá mostrar alguns avisos como:

	```rust
	warning: struct `Pallet` is never constructed
	--> src/balances.rs:1:12
	|
	1 | pub struct Pallet {    }
	|              ^^^^^^
	|
	= note: `#[warn(dead_code)]` on by default

	warning: `pr` (bin "pr") generated 1 warning
	```

Tudo bem! Ainda não começamos a usar nosso Pallet, mas você pode ver que o compilador Rust está detectando nosso novo código e trazendo essa lógica para nosso programa principal. Este é o início da construção do nosso primeiro módulo de máquina de estados.

## Exercícios:

Em `balances.rs`:

```rust
/* TODO: crie uma nova struct pública chamada `Pallet`. */
```

Em `main.rs`:

```rust
/* TODO: use seu novo módulo `balances` */

fn main() {
    println!("Hello, world!");
}
```

Se você tiver uma pergunta sobre a qual está curioso, sinta-se à vontade para perguntar em [🆘・seção-1](https://discord.com/channels/898706705779687435/980904325763186788) no Discord.

Normalmente, começo a entender realmente as coisas quando começo a programar. Então, vamos começar a construir algum código. :)