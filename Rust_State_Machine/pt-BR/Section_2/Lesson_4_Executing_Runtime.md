Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/e5f04fe5925df501f7760f18b45b78cb).

# Usando Nosso Runtime

[Youtube](https://youtu.be/d4Bp3avZXx0?si=v8WnHztHiYDsSKpQ)

Até agora, apenas estruturamos partes da nossa blockchain. Os testes garantiram que o código que escrevemos até agora faz sentido, mas ainda não USAMOS nenhuma das lógicas que escrevemos para nosso programa `main`.

Vamos mudar isso usando nosso Runtime e realmente executando a lógica em nossa blockchain.

## Simulando um Bloco

A entrada para qualquer função de transição de estado de blockchain é um bloco de transações.

Mais tarde no tutorial, dedicaremos mais tempo para construir blocos adequados e executá-los, mas por enquanto, podemos "simular" todos os fundamentos do que um bloco faria chamando individualmente as funções que nossos Pallets expõem.

### Estado Genesis

O estado da sua blockchain se propagará de bloco em bloco. Isso significa que se Alice receber 100 tokens no bloco 4, ela pode transferir pelo menos 100 tokens no bloco 5, e assim por diante.

Mas como os usuários obtêm algum saldo para começar?

A resposta a esta pergunta pode ser diferente para diferentes blockchains, mas em geral, a maioria das blockchains modernas começa com um Estado Genesis. Este é o estado inicial da sua blockchain no "bloco 0".

Isso significa que qualquer coisa definida no estado genesis pode ser usada no bloco 1 e pode iniciar sua blockchain para ser funcional.

Em nossa situação, você pode simplesmente chamar funções de baixo nível como `set_balance` antes de simular nosso primeiro bloco para estabelecer nosso estado genesis.

### Etapas de um Bloco Básico

Vamos rapidamente dividir as etapas de execução de um bloco básico:

1. Primeiro, incrementamos o número do bloco, já que cada novo bloco terá um novo número de bloco.
2. Em seguida, passamos e executamos cada transação nesse bloco:
	1. Cada transação para nossa blockchain virá de um usuário, portanto, incrementaremos o nonce dos usuários à medida que processamos suas transações.
	2. Então tentaremos executar a função que eles desejam chamar, por exemplo `transfer`.
	3. Repetimos este processo para cada transação.

### Tratamento de Erros

A função `main()` em Rust não pode propagar ou tratar erros por si só. Ou tudo dentro dela é tratado, ou você terá que acionar um `panic`.

Como você já aprendeu, acionar um `panic` geralmente não é bom, mas pode ser a única coisa que você pode fazer se algo estiver seriamente errado. Para nossa blockchain, a única coisa que pode realmente causar um pânico é importar um bloco que não corresponda ao número de bloco esperado. Não há nada que possamos fazer para "tratar" esse erro. Se alguém está nos dizendo para executar o bloco errado, então temos um problema maior com nosso sistema geral que precisa ser corrigido.

No entanto, os usuários também podem enviar transações que resultam em um erro. Por exemplo, Alice tentando enviar mais fundos do que ela tem em sua conta.

Devemos entrar em pânico?

Absolutamente não! Este é o tipo de erro que nosso Runtime deve ser capaz de tratar, pois é esperado que tais erros ocorram. **Um bloco pode ser válido, mesmo que transações no bloco sejam inválidas!**

Quando uma transação retorna um erro, devemos mostrar esse erro ao usuário e então "engolir" o resultado.
Por exemplo:

```rust
let _res = i_can_return_error().map_err(|e| eprintln!("{}", e));
```

Neste caso, você pode ver que qualquer erro que `i_can_return_error` retornaria é impresso no console, mas, caso contrário, o `Result` dessa função é colocado em uma variável não utilizada `_res`.

Você deve ter **MUITO CUIDADO** ao fazer isso. Engolir um erro é exatamente o oposto do tratamento adequado de erros que Rust oferece aos desenvolvedores. No entanto, realmente não temos escolha aqui em nossa função `main`, e entendemos completamente o que estamos fazendo aqui.

Em sistemas de blockchain reais, os usuários ainda são cobrados por uma taxa de transação, mesmo quando sua transação resulta em um `Err`. Isso garante que os usuários ainda estejam pagando um custo por acionar a lógica na blockchain, mesmo quando a função falha. Isso é uma parte importante para manter nossa blockchain resiliente a ataques de DDOS e sybil.


## Simule Seu Primeiro Bloco

Você acha que entende tudo o que é necessário para simular seu primeiro bloco?

Siga as instruções fornecidas pelo template para transformar sua função `main` de "Hello, World!" para realmente executar o Runtime da sua blockchain.

## Exercícios

No `main.rs`:

```rust
mod balances;
mod system;

// Este é o nosso Runtime principal.
// Ele acumula todos os diferentes pallets que queremos usar.
pub struct Runtime {
	system: system::Pallet,
	balances: balances::Pallet,
}

impl Runtime {
	// Cria uma nova instância do Runtime principal, criando uma nova instância de cada pallet.
	fn new() -> Self {
		Self { system: system::Pallet::new(), balances: balances::Pallet::new() }
	}
}

fn main() {
	/* TODO: Cria uma variável mutável `runtime`, que é uma nova instância de `Runtime`. */
	/* TODO: Define o saldo de `alice` para 100, permitindo-nos executar outras transações. */

  	//começa a emular um bloco
	/* TODO: Aumenta o número do bloco no sistema. */
  	/* TODO: Afirmar que o número do bloco é o que esperamos. */

	//primeira transação
	/* TODO: Aumenta o nonce de `alice`. */
	/* TODO: Executa uma transferência de `alice` para `bob` por 30 tokens.
		- A transferência _poderia_ retornar um erro. Deveríamos usar `map_err` para imprimir
		o erro, se houver.
		- Devemos capturar o resultado da transferência em uma variável não utilizada como `_res`.
	*/

	//segunda transação
	/* TODO: Aumenta o nonce de `alice` novamente. */
	/* TODO: Executa outra transferência de saldo, desta vez de `alice` para `charlie` por 20. */
}
```