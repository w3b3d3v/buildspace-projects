Antes de prosseguir, verifique se sua resposta corresponde à [solução da etapa anterior aqui](https://gist.github.com/nomadbitcoin/1883ed94b8c8439df4264024af3e31c2)

Parabéns! A sua determinação em avançar e enfrentar os desafios de programação é verdadeiramente louvável!

# Pallet de Prova de Existência

[Youtube](https://www.youtube.com/watch?v=7F7qn7n7_i0)

Nesta seção, vamos criar um Pallet de Prova de Existência.

Vamos aproveitar toda a refatoração que fizemos até agora para tornar muito simples a integração deste novo Pallet em nosso Runtime existente.

Não há novos conceitos para aprender nesta seção; no entanto, isso testará se você entende e pode reproduzir todas as etapas e conceitos aprendidos nas seções anteriores.

## Pallet de Prova de Existência

Percorremos um longo caminho desde que construímos nosso primeiro Pallet de Saldos. A estrutura do nosso Runtime e dos Pallets evoluiu bastante desde então:

- Tipos Genéricos
- Trait Config
- Dispatch Aninhado
- e mais...

Este será o último pallet que construiremos para este tutorial, mas vamos construí-lo conhecendo todas as dicas e truques que aprendemos até agora. O objetivo aqui é garantir que todas as complexidades do desenvolvimento de Pallets sejam bem compreendidas e que você seja capaz de navegar por todo o código Rust.

### O que é Prova de Existência?

O Pallet de Prova de Existência usa a blockchain para fornecer um ledger seguro e imutável que pode ser usado para verificar a existência de um documento, arquivo ou dado específico em um determinado momento.

Como a blockchain atua como um ledger imutável cuja história não pode ser alterada, quando alguns dados são colocados na blockchain, eles podem ser referenciados no futuro para mostrar que esses dados já existiam no passado.

Por exemplo, imagine que você soubesse o resultado da próxima eleição presidencial, mas antes de revelá-lo, você quer garantir que pode provar que o descobriu em um determinado momento. Você poderia colocar algum tipo de dado na blockchain que represente a descoberta e, depois, quando sua pesquisa for revisada e publicada, você pode provar que tinha essa informação antes.

Normalmente, você não colocaria o conteúdo bruto da sua reivindicação na blockchain, mas um [hash](https://en.wikipedia.org/wiki/Cryptographic_hash_function) dos dados, que é menor e ofusca os dados da sua reivindicação antes que você esteja pronto para revelá-los.

No entanto, para os propósitos deste tutorial, não introduziremos funções de hash ainda.

## Estrutura do Pallet

O `BTreeMap` é novamente a melhor ferramenta para usar para armazenar dados neste Pallet. No entanto, você notará que a construção do armazenamento é um pouco diferente do que antes. Em vez de ter um mapa de contas para alguns dados, mapearemos o conteúdo que queremos reivindicar para o usuário que o possui.

Essa construção de `conteúdo -> conta` permite que uma conta seja a proprietária de várias reivindicações diferentes, mas cada reivindicação só pode ser de um único usuário.

## Exercícios:

### Crie Seu Pallet

Vamos começar a criar este pallet:

1. Crie um novo arquivo para o seu Pallet de Prova de Existência.

	```bash
	touch src/proof_of_existence.rs
	```

2. Copie o conteúdo do template para o seu novo arquivo.

```rust
use core::fmt::Debug;
use std::collections::BTreeMap;

pub trait Config: crate::system::Config {
	/// O tipo que representa o conteúdo que pode ser reivindicado usando este pallet.
	/// Pode ser o conteúdo diretamente como bytes, ou melhor ainda, o hash desse conteúdo.
	/// Deixamos essa decisão para o desenvolvedor do runtime.
	type Content: Debug + Ord;
}

/// Este é o Módulo de Prova de Existência.
/// É um módulo simples que permite que contas reivindiquem a existência de alguns dados.
#[derive(Debug)]
pub struct Pallet<T: Config> {
	/// Um simples mapa de armazenamento de conteúdo para o proprietário desse conteúdo.
	/// As contas podem fazer várias reivindicações diferentes, mas cada reivindicação só pode ter um proprietário.
	/* TODO: Adicione um campo `claims` que é um `BTreeMap` de `T::Content` para `T::AccountId`. */
}

impl<T: Config> Pallet<T> {
	/// Cria uma nova instância do Módulo de Prova de Existência.
	pub fn new() -> Self {
		/* TODO: Retorne uma nova instância da estrutura `Pallet`. */
	}
}
```

3. Complete os `TODO`s para adicionar um armazenamento ao seu novo pallet e permitir que ele seja inicializado.

4. No seu arquivo `main.rs`, importe o módulo `proof_of_existence`.

Certifique-se de que tudo compile após você completar estas etapas.

No `main.rs`:

```rust
mod balances;
/* TODO: Importe o módulo `proof_of_existence`. */
mod support;
mod system;
```