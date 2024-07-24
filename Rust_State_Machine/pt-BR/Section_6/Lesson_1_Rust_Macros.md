Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/6a7506ad26d4bdcf9db1f7f358b687ff).

Sensacional! Poucos chegaram a este ponto, tornando você excepcionalmente persistente. Grandes oportunidades aguardam aqueles que completarem todas as lições!

# Macros em Rust

[Youtube](https://youtu.be/DMMp3lc3Vbk?si=lAVbcAq4AzWeEM4D)

Nesta seção, introduziremos Macros em Rust ao nosso projeto para reduzir o código boilerplate e automatizar implementações.

Você pode imaginar que continuar adicionando novos Pallets ao nosso runtime levaria a muito código semelhante ou redundante.

Até o final desta seção, você verá como as Macros em Rust podem gerar automaticamente o código que temos escrito e por que o SDK do Polkadot usa essa técnica para melhorar a experiência do desenvolvedor e a produtividade.

# Introduzindo Macros

Se você chegou até aqui, então terminou de projetar sua máquina de estados simples.

Neste ponto, nosso objetivo é ver se podemos usar o poder das macros em Rust para facilitar ainda mais o desenvolvimento futuro.

Tudo isso é uma preparação para você trabalhar com o SDK do Polkadot, que depende fortemente de macros como as que você verá aqui.

## Código Gerado Automaticamente

Como mencionado anteriormente, as macros em Rust são basicamente códigos que podem gerar mais códigos.

Como você pode ver em nossa máquina de estados simples, há muito código boilerplate que poderíamos gerar, seguindo os padrões e estruturas simples que projetamos.

Por exemplo:

- Esperamos que cada Pallet exponha algumas funções chamáveis com `Call`.
- Sabemos que cada `Call` terá todos os mesmos parâmetros da função subjacente do Pallet, exceto o `caller`.
- Sabemos que cada Pallet implementará a lógica de `Dispatch` no struct `Pallet`.
- Sabemos que o `Runtime` acumulará todos os `pallet::Call`s no enum `RuntimeCall` externo.
- Sabemos que o `Runtime` terá lógica para redistribuir chamadas em nível de runtime para o nível do pallet.
- e assim por diante...

Quanto mais abstraímos nosso Pallet e Runtime em peças consistentes e extensíveis, mais podemos automatizar e, em última análise, isso pode proporcionar uma melhor experiência de desenvolvimento.

## Navegando pelas Macros

Este tutorial não tem a intenção de ensinar você a escrever essas macros. Essas informações levariam um tutorial inteiro por si só.

Em vez disso, estamos fornecendo a você macros que devem funcionar diretamente com seu código existente e substituir muito do código que você já escreveu.

Macros, em geral, são "mágicas". Se você não escreveu a macro, pode haver pouca compreensão do que está acontecendo por baixo. Nesse contexto, as macros que estamos fornecendo substituirão diretamente o código que você já escreveu, então você deve entender completamente o que está sendo gerado e como elas funcionam.

A pasta `macros` contém um `lib.rs`, que expõe as duas macros de atributo construídas para este tutorial:

1. `#[macros::call]`
2. `#[macros::runtime]`

Você pode encontrar o código para essas duas macros em suas respectivas pastas `call` e `runtime`.

Em cada uma dessas pastas, há 3 arquivos:

1. `mod.rs` - O ponto de entrada para a macro, onde o código é analisado e gerado.
2. `parse.rs` - A lógica de análise da macro, extraindo as informações necessárias para gerar o código.
3. `expand.rs` - O código de expansão/geração, que escreverá o novo código para nós com os dados fornecidos.

Vamos passar por cada um desses arquivos mais profundamente à medida que incluímos as macros em nosso código.

## Adicionando as Macros ao Nosso Projeto

Todas as macros estão contidas dentro do próprio crate, que será uma pasta no seu projeto.

Baixe o conteúdo da pasta das macros aqui: [download](https://download-directory.github.io?url=https://github.com/w3b3d3v/rust-state-machine/tree/master/macros)

> Se esse link não funcionar, você pode extrair a pasta `macros` da maneira que for melhor para você do repositório fonte deste tutorial: https://github.com/w3b3d3v/rust-state-machine/tree/master/macros

Depois de ter o conteúdo da pasta macros:

1. Copie o conteúdo para uma pasta `macros` na raiz do seu projeto.
2. Atualize seu arquivo `cargo.toml` para incluir este crate em seu projeto:

   ```toml
   [dependencies]
   num = "0.4.1"
   macros = { path = "./macros/" }
   ```

Recompile seu projeto e você deverá ver este novo crate e suas sub-dependências sendo compilados.

Na próxima etapa, começaremos a integrar essas macros em sua máquina de estados simples.