# Construa uma Blockchain com Rust

Você conseguiu - incrível! Bem-vindo ao tutorial de máquina de estados em Rust :)
Aqui é Dani (@danicuki) e Yan (@nomadbitcoin) e seremos seus instrutores.
Este projeto é para desenvolvedores que desejam se aprofundar mais na Stack Polkadot baseada em Substrate e na linguagem de programação Rust.

Este tutorial é destinado a ensinar os conceitos básicos de Rust, Blockchain e, eventualmente, o funcionamento interno do [Polkadot SDK](https://github.com/paritytech/polkadot-sdk).

Pela minha experiência, a parte mais difícil de construir seu primeiro blockchain usando o Polkadot SDK é navegar pelos recursos avançados do Rust usados pelo [Substrate](https://github.com/paritytech/polkadot-sdk/tree/master/substrate) e entender a magia por trás de vários macros que geram código para você.

Este tutorial não assume que você tenha um conhecimento profundo sobre Rust, Blockchain ou Polkadot SDK. No entanto, este build não substitui uma introdução básica de nenhum desses tópicos.

> 🚨 **Alerta:** Se você deseja aprofundar seu conhecimento em algum desses temas, nossos [grupos de estudos](https://www.w3d.community/grupos-de-estudos) são uma ótima oportunidade para você. Eles acontecem semanalmente dentro da comunidade e são uma excelente forma de adquirir mais conhecimento.

Recomendamos a leitura dos 11 primeiros capítulos do [Rust Book](https://doc.rust-lang.org/book/) antes de iniciar este build para o melhor aproveitamento. Existe uma versão do [Rust Book PT-BR](https://rust-br.github.io/rust-book-pt-br/) mas não está atualizada com a versão mais recente do Rust.

> Não é necessário ser um especialista em tudo o que lê, mas será útil ter exposição a todos os vários tópicos como: Ownership, Tipos de dados, Structs, Enums, Crates, Tratamentos de erros, Traits, Tipos genéricos e Testes.

O tutorial é dividido em lições que cobrem objetivos de aprendizado específicos e podem funcionar como bons pontos de pausa, se necessário.

Todo o conteúdo deste build é de código aberto, de acesso gratuito e pode ser encontrado [aqui](https://github.com/w3b3d3v/rust-state-machine).

Se você tiver sugestões para melhorar o tutorial, comentários ou problemas, [envie um PR](https://github.com/w3b3d3v/buildspace-projects/pulls) com a solução para a branch `web3dev-version`.

Sem mais delongas, aproveite e espero que você aprenda muito! 😎

## Criando seu projeto em Rust

Nesta etapa, vamos inicializar um projeto Rust básico, onde podemos começar a construir nossa máquina de estados simples.

[Youtube](https://youtu.be/oBfgvUBksg4?si=xUqPPvEGjzY_O5UZ)

### Iniciando um novo projeto

1. Crie um diretório chamado `rust-state-machine` onde você quer que fique seu projeto no seu computador e acesse essa pasta pelo seu terminal.

```bash
mkdir rust-state-machine
cd rust-state-machine
```

2. Nessa pasta, inicialize seu projeto Rust usando `cargo init`:

```bash
cargo init
```

> Isso criará um executável Rust básico que podemos usar para começar.

3. Você pode verificar se seu projeto está funcionando corretamente executando:

```bash
cargo run
```

Você deverá ver "Hello, World!" no final da compilação:

```bash
➜  rust-state-machine git:(master) ✗ cargo run
Compiling rust-state-machine v0.1.0 (/Users/shawntabrizi/Documents/GitHub/rust-state-machine)
	Finished dev [unoptimized + debuginfo] target(s) in 2.19s
	Running `target/debug/rust-state-machine`
Hello, world!
```

Se olharmos o que foi gerado, nessa pasta, você verá o seguinte:

- `src/main.rs` - Este é o ponto de entrada do seu programa. Construiremos tudo para este projeto na pasta `src`.
- `Cargo.toml` - Este é um arquivo de configuração para seu projeto Rust. Muito semelhante a um `package.json` que você veria em um projeto Node.JS. Vamos modificar isso no futuro quando importarmos crates para usar em nosso projeto, mas podemos deixar isso de lado por enquanto.
- `Cargo.lock` - Este é um arquivo de bloqueio gerado automaticamente com base no seu `cargo.toml` e na compilação. Isso geralmente define as versões muito específicas de cada crate importado e não deve ser editado manualmente.
- `target/*` - Você também pode ver uma pasta target se você fez `cargo run`. Esta é uma pasta onde todos os artefatos de construção são colocados durante a compilação. Não vamos adicionar esta pasta ao nosso histórico do git.

Tudo isso deve ser bastante familiar para você se já tiver alguma experiência mínima com Rust. Se algo disso for novo, sugiro que primeiro leia o [Rust Book](https://doc.rust-lang.org/book/) e o [Rust by Example](https://doc.rust-lang.org/rust-by-example/), pois isso já é uma indicação de que este guia pode não estar direcionado ao seu nível de conhecimento.

## 🤘 Quer melhorar alguma coisa?

A cultura da Polkadot é baseada no espírito proativo e código aberto, e nós também. Se você encontrar um problema, erro de digitação, etc., você pode corrigi-lo facilmente [enviando um PR](https://github.com/w3b3d3v/buildspace-projects/pulls) com a solução para a branch `web3dev-version`!

**[Todo este conteúdo pode ser encontrado neste repositório](https://github.com/w3b3d3v/buildspace-projects/tree/web3dev-version)**! Se precisar de ajuda para fazer isso, mande uma mensagem para `@nomadbitcoin` no Discord e ele poderá te ajudar 😃.

Vamos garantir a você uma reputação open-source!!

E por último, mas não menos importante, dê uma ⭐ no repositório para nos sentirmos importantes!
