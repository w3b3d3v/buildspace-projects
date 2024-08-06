# Ferramentas do Rust

[Youtube](https://www.youtube.com/watch?v=wvwNxmah3-c)

Nesta etapa, vamos inicializar um projeto Rust básico, onde podemos começar a construir nossa máquina de estados simples em Rust.

## Configurando o rustfmt

Para manter o seu código limpo e fácil de ler, usamos uma ferramenta chamada [`rustfmt`](https://github.com/rust-lang/rustfmt).

1. Crie um novo arquivo no diretório raiz do seu projeto chamado `rustfmt.toml`.

```bash
touch rustfmt.toml
```

2. Use o arquivo `rustfmt.toml` fornecido para configurar suas preferências de formatação.

3. Execute o formatador de código utilizando o seguinte comando:

```bash
cargo fmt
```

Você não deve ver nenhuma mudança desta vez, mas à medida que você escrever mais código, será capaz de ver o `cargo fmt` deixar tudo bonito, consistente e fácil de ler.

> 🚨 **Alerta:** Recomendamos que você execute `cargo fmt` após cada etapa!

## Rust Analyzer

Outra ferramenta popular na comunidade Rust é o [Rust Analyzer](https://rust-analyzer.github.io/).

Ela oferece muitos recursos, como autocompletar código e ir para a definição, para editores de código como o VS Code.

No entanto, para fornecer toda a funcionalidade que oferece, o Rust Analyzer precisa compilar seu código. Para um projeto pequeno como este, isso não é um problema, mas trabalhar com um projeto grande como o Substrate/Polkadot-SDK é.

Minha recomendação pessoal é que o Rust Analyzer não é necessário neste projeto e, em geral, você não deve usá-lo para o desenvolvimento do Substrate. No entanto, esta seção pode ser atualizada no futuro para incluir configurações especiais do Rust Analyzer que funcionarão bem com o Polkadot SDK.

No entanto, se você quiser usá-lo de qualquer maneira, agora é o momento certo para configurá-lo.

### 🎉 Primeiro passo

VOCÊ ESTÁ NO CAMINHO.

Você completou com sucesso a seção 0! Ótimo começo :).
