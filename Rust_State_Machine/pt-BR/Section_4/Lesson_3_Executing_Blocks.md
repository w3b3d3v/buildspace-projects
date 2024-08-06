Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/5ec6d06dbbb5504db697fb2e2562cb9f).

# Executando Blocos

Agora começaremos o processo de substituir a simulação simples de bloco em nossa função `main` por um pipeline de execução de bloco adequado.

## Executar Bloco

[Youtube](https://www.youtube.com/watch?v=B-Q_QMK4Ins)

Introduzimos uma nova função no nosso `Runtime` chamada `fn execute_block`.

Os passos desta função são exatamente os mesmos da nossa função `main` atual, mas usando o tipo de `Block` concreto que definimos para extrair detalhes como o número do bloco esperado e os extrínsecos que queremos executar.

### Iterando Sobre um Vetor

Para construir nossa função `execute_block`, precisaremos iterar sobre todos os extrínsecos em nosso bloco e despachar essas chamadas. Em Rust, a maneira comum de acessar os elementos de um vetor é transformá-lo em um iterador.

Existem duas funções usadas para transformar um vetor em um iterador, `iter` e `into_iter`, e sua diferença está na propriedade:

- `iter`: Este método cria um iterador que empresta cada elemento do vetor, permitindo que você leia os valores sem tomar posse. É útil quando você quer iterar sobre o vetor mantendo-o intacto.

- `into_iter`: Este método consome o vetor, transferindo a propriedade de cada elemento para o iterador. É útil quando você quer mover ou transferir a propriedade dos elementos do vetor para outra parte do seu código. Após usar `into_iter`, o vetor original não pode mais ser usado, pois a propriedade foi transferida.

No nosso contexto, queremos usar `into_iter()`, então obteremos algo que se parece com:

```rust
for support::Extrinsic { caller, call } in block.extrinsics.into_iter() {
    // fazer algo com `caller` e `call`
}
```

Aqui você pode ver que também fazemos um truque para separar os campos do `Extrinsic` em uma única linha, já que, em última análise, queremos trabalhar com `caller` e `call`. Você pode, é claro, dividir esse processo em várias linhas se quiser.

### Despachando uma Chamada

Uma vez que tenhamos o `call` e o `caller`, o que devemos fazer com eles?

É aqui que a trait `Dispatch` começa a entrar em jogo. Você verá em nosso template, incluímos a estrutura de um `unimplemented()` `fn dispatch`. Escreveremos essa lógica no próximo passo, mas precisamos já usar a função `dispatch` em nossa lógica de `execute_block`.

Uma vez que tenhamos o `call` e o `caller`, queremos passá-los para a lógica de `dispatch`, que você vê que está implementada no `Runtime`.

Isso ficará algo assim:

```rust
let _res = self.dispatch(caller, call).map_err(|e| eprintln!("{}", e));
```

Note que em Rust, se você quiser acessar uma função dentro de uma trait, como fazemos aqui com `dispatch`, você precisa importar explicitamente essa trait em seu projeto.

Deixamos um `TODO` no topo de `main.rs` onde pedimos para você importar `crate::support::Dispatch`, que permitirá acessar a chamada `dispatch` no `Runtime`.

### Mensagens de Erro Melhores

Como esta é uma função mais permanente do nosso projeto, também faz sentido expandir a mensagem sendo impressa quando houver erros de extrínsecos. Por exemplo:

```rust
eprintln!(
    "Erro de Extrínseco\n\tNúmero do Bloco: {}\n\tNúmero do Extrínseco: {}\n\tErro: {}",
    block.header.block_number, i, e
)
```

Isso permite que você veja o número do bloco, o número do extrínseco e a mensagem de erro sempre que houver um erro de extrínseco. Isso pode ser muito útil quando você tem muitos blocos sendo importados, cada um com potencialmente muitos extrínsecos.

Para obter o número do extrínseco `i`, você pode encadear a função [`enumerate()`](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.enumerate) após o `into_iter()`.

## Exercícios:

### Construa Sua Função de Execução de Bloco

Agora você deve ter todas as ferramentas e informações necessárias para escrever com sucesso sua função `execute_block`.

Siga os `TODO`s fornecidos pelo template e certifique-se de incluir o `impl crate::support::Dispatch for Runtime` que fornecemos para você e que implementaremos nos próximos passos.

No `main.rs`:

```rust
mod balances;
mod support;
mod system;

/* TODO: Importe `crate::support::Dispatch` para que você possa acessar a função `dispatch`. */

/// ...código anterior.

// Estas são todas as chamadas expostas para o mundo.
// Note que é apenas uma acumulação das chamadas expostas por cada módulo.
pub enum RuntimeCall {
    // TODO: Não implementado ainda.
}

// Este é o nosso Runtime principal.
// Ele acumula todos os diferentes pallets que queremos usar.
#[derive(Debug)]
pub struct Runtime {
    system: system::Pallet<Self>,
    balances: balances::Pallet<Self>,
}

impl system::Config for Runtime {
    type AccountId = types::AccountId;
    type BlockNumber = types::BlockNumber;
    type Nonce = types::Nonce;
}

impl balances::Config for Runtime {
    type Balance = types::Balance;
}

impl Runtime {
    // Cria uma nova instância do Runtime principal, criando uma nova instância de cada pallet.
    fn new() -> Self {
        Self { system: system::Pallet::new(), balances: balances::Pallet::new() }
    }

	// Executa um bloco de extrínsecos. Incrementa o número do bloco.
	fn execute_block(&mut self, block: types::Block) -> support::DispatchResult {
		/* TODO:
			- Incrementar o número do bloco do sistema.
			- Verifique se o número do bloco de entrada corresponde ao número do bloco atual,
			ou retornar um erro.
			- Iterar sobre os extrínsecos do bloco...
			- Aumente o nonce do chamador.
			- Despache o extrínseco usando o `caller` e a `call` contida no extrínseco.
			- Lidar com erros de `despacho` da mesma forma que fizemos para chamadas individuais: imprimindo qualquer
			erro e capturar o resultado.
			- Você pode estender a mensagem de erro para incluir informações como o número do bloco e
			número extrínseco.
		*/
		Ok(())
	}
}

//também ADICIONE ESTE CÓDIGO AO SEU arquivo main.rs:
impl crate::support::Dispatch for Runtime {
    type Caller = <Runtime as system::Config>::AccountId;
    type Call = RuntimeCall;

    // Despacha uma chamada em nome de um chamador. Aumenta o nonce do chamador.
    //
    // Dispatch nos permite identificar qual chamada de módulo subjacente queremos executar.
    // Observe que extraímos o `chamador` do extrínseco e usamos essa informação
    // para determinar em nome de quem estamos executando a chamada.
    fn dispatch(
        &mut self,
        caller: Self::Caller,
        runtime_call: Self::Call,
    ) -> support::DispatchResult {
        unimplemented!();
    }
}

/// ...código anterior.
```

Executar blocos é um aspecto fundamental da funcionalidade blockchain, e agora você deu um passo significativo para dominar esse processo. Parabéns por alcançar esse marco!

Se você tiver alguma dúvida ou encontrar problemas, não hesite em pedir ajuda no canal [#🆘・section-4](https://discord.com/channels/898706705779687435/980905761783832637) no Discord. Estamos aqui para te apoiar! 🚀