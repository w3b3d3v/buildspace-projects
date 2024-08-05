Antes de prosseguir, verifique se sua resposta corresponde à [solução da etapa anterior aqui](https://gist.github.com/nomadbitcoin/2b3beb37b376732e6c000053e04f07ff).

Incrível! Você dominou outra etapa importante e já está na metade do caminho. Você está prestes a adicionar uma conquista notável ao seu portfólio!

# Executando Blocos e Despachando Chamadas

Nesta próxima seção, você construirá o pipeline central usado para interagir com sua máquina de estados.

Vamos criar a estrutura do bloco que contém as transações para sua função de transição de estado e, em seguida, o pipeline de despacho de função para direcionar essas transações para as chamadas de função apropriadas.

O objetivo desta seção é fazer com que sua máquina de estados existente se pareça com uma blockchain que pode ser estendida e atualizada.

# Adicionando Nosso Módulo de Suporte

[Youtube](https://youtu.be/F9uPCvAX9_k?si=9uGfJAVAEBbq2ewv)

Neste passo, introduziremos um módulo de `support` para ajudar a trazer vários tipos e traits que usaremos para aprimorar nossa máquina de estados simples.

## Construindo um Bloco

O primeiro conjunto de primitivas fornecidas pelo módulo `support` são um conjunto de structs que precisamos para construir um `Block` simples.

#### O Bloco

Um bloco é basicamente dividido em duas partes: o cabeçalho e um vetor de extrínsecos.

Você pode ver que mantemos o `Block` completamente genérico em relação aos tipos `Header` e `Extrinsic`. O conteúdo e as definições exatas desses subtipos podem mudar, mas o struct genérico `Block` pode sempre ser usado.

#### O Cabeçalho

O cabeçalho do bloco contém metadados sobre o bloco, que são usados para verificar se o bloco é válido. Em nossa máquina de estados simples, armazenamos apenas o número do bloco no cabeçalho, mas blockchains reais, como o Polkadot, possuem:

- Hash do bloco pai
- Número do bloco
- Raiz do estado
- Raiz dos extrínsecos
- Digests / Logs de consenso

#### O Extrínseco

Em nossa máquina de estados simples, um extrínseco é sinônimo de transações de usuário.

Assim, nosso tipo extrínseco é composto por uma `Call` (a função que executaremos) e um `Caller` (a conta que deseja executar essa função).

O SDK do Polkadot suporta outros tipos de extrínsecos além de transações de usuário, razão pela qual ele é chamado de `Extrinsic`, mas isso está além do escopo deste tutorial.

### Despachando Chamadas

A próxima mudança importante que faremos em nossa máquina de estados simples é lidar com o despacho de funções. Basicamente, você pode imaginar que poderia haver vários pallets diferentes em seu sistema, cada um com chamadas diferentes que desejam expor.

Seu runtime, atuando como um único ponto de entrada para toda a sua função de transição de estado, precisa ser capaz de direcionar as chamadas recebidas para as funções apropriadas. Para isso, precisamos da trait `Dispatchable`.

### Resultado de Despacho

Uma última coisa que adicionamos ao módulo de suporte foi uma definição simples do tipo `Result` que queremos que todas as chamadas despacháveis retornem. Este é exatamente o tipo que já usamos para a função `fn transfer` e permite que retornemos `Ok(())` se tudo correu bem ou `Err("alguma mensagem de erro")` se algo deu errado.

## Exercícios:

### Crie o Módulo de Suporte

Agora que você entende o que está no módulo de suporte, adicione-o ao seu projeto.

1. Crie o arquivo `support.rs`:

    ```bash
    touch src/support.rs
    ```

2. Copie e cole o conteúdo fornecido no seu arquivo.
3. Importe o módulo de suporte no topo do seu arquivo `main.rs`.
4. Finalmente, substitua `Result<(), &'static str>` por `crate::support::DispatchResult` na função `fn transfer` no seu Pallet de Saldos.

Crie o arquivo `src/support.rs`:

```rust
/// A representação mais primitiva de um bloco de blockchain.
pub struct Block<Header, Extrinsic> {
    /// O cabeçalho do bloco contém metadados sobre o bloco.
    pub header: Header,
    /// Os extrínsecos representam as transições de estado a serem executadas neste bloco.
    pub extrinsics: Vec<Extrinsic>,
}

/// Estamos usando um cabeçalho extremamente simplificado que contém apenas o número atual do bloco.
/// Em uma blockchain real, você esperaria encontrar também:
/// - hash do bloco pai
/// - raiz do estado
/// - raiz dos extrínsecos
/// - etc...
pub struct Header<BlockNumber> {
    pub block_number: BlockNumber,
}

/// Este é um "extrínseco": literalmente uma mensagem externa de fora da blockchain.
/// Esta versão simplificada de um extrínseco nos diz quem está fazendo a chamada e qual chamada eles estão fazendo.
pub struct Extrinsic<Caller, Call> {
    pub caller: Caller,
    pub call: Call,
}

/// O tipo Result para nosso runtime. Quando tudo é concluído com sucesso, retornamos `Ok(())`,
/// caso contrário, retornamos uma mensagem de erro estática.
pub type DispatchResult = Result<(), &'static str>;

/// Uma trait que nos permite despachar um extrínseco recebido para a chamada de função de transição de estado apropriada.
pub trait Dispatch {
    /// O tipo usado para identificar o chamador da função.
    type Caller;
    /// A chamada de função de transição de estado que o chamador está tentando acessar.
    type Call;

    /// Esta função recebe um `caller` e a `call` que eles querem fazer, e retorna um `Result`
    /// com base no resultado dessa chamada de função.
    fn dispatch(&mut self, caller: Self::Caller, call: Self::Call) -> DispatchResult;
}
```

No `main.rs`:

```rust
mod balances;
/* TODO: Adicione o módulo de suporte aqui. */
mod system;
```