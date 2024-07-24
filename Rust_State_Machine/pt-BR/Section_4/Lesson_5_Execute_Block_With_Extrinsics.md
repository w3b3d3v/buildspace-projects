Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/e818e17f1833fc1a2fe96c224c0bf2d4).

# Usando Execute Block

[Youtube](https://youtu.be/7G8jdZk0Cyg?si=ChTQgWl7ywjat3Xw)

Agora implementamos com sucesso a lógica de `execute_block` e `dispatch` necessária para construir e executar `Blocks` reais.

Vamos trazer essa lógica para nossa função `main`.

## Criando um Bloco

Você pode criar um novo `Block` preenchendo todos os campos da struct e atribuindo a uma variável.

Por exemplo:

```rust
let block_1 = types::Block {
	header: support::Header { block_number: 1 },
	extrinsics: vec![
		support::Extrinsic {
			caller: "alice".to_string(),
			call: RuntimeCall::BalancesTransfer { to: "bob".to_string(), amount: 69 },
		},
	],
};
```

É importante que você defina o número do bloco corretamente, pois verificamos isso em nossa função `execute_block`. O primeiro bloco em nossa máquina de estados terá o número `1`.

Além disso, lembre-se de que você pode adicionar vários extrínsecos em um único bloco, estendendo o vetor.

## Executando um Bloco

Depois de construir seu `Block`, você pode passá-lo para a função `execute_block` implementada no seu `runtime`.

```rust
runtime.execute_block(block_1).expect("invalid block");
```

Observe como panicamos com a mensagem `"invalid block"` se a função `execute_block` retornar um erro. Isso só deve acontecer quando algo está seriamente errado com seu bloco, por exemplo, o número do bloco está incorreto para o que esperamos.

Este pânico NÃO será acionado se houver um erro em um extrínseco, pois "engolimos" esses erros na função `execute_block`. Este é o comportamento que queremos.

## Exercícios:

### Atualize Sua Função Principal

Vá em frente e use o tipo `Block` e a função `execute_block` para atualizar a lógica da sua função `main`.

Siga os `TODO`s fornecidos no template para concluir esta etapa.

No `main.rs`:

```rust
fn main() {
	// Crie uma nova instância do Runtime.
	// Ele irá instanciar com todos os módulos que usa.
	let mut runtime = Runtime::new();
	let alice = "alice".to_string();
	let bob = "bob".to_string();
	let charlie = "charlie".to_string();

	// Inicialize o sistema com algum saldo inicial.
	runtime.balances.set_balance(&alice, 100);

	// Inicie a emulação de um bloco
	runtime.system.inc_block_number();
	assert_eq!(runtime.system.block_number(), 1);

	// primeira transação
	runtime.system.inc_nonce(&alice);
	let _res = runtime
		.balances
		.transfer(alice.clone(), bob, 30)
		.map_err(|e| eprintln!("{}", e));

	// segunda transação
	runtime.system.inc_nonce(&alice);
	let _res = runtime.balances.transfer(alice, charlie, 20).map_err(|e| eprintln!("{}", e));

	/*
		TODO: Substitua a lógica acima por um novo `Block`.
			- Defina o número do bloco como 1 no `Header`.
			- Mova suas transações existentes para o formato extrínseco, usando `Extrinsic` e `RuntimeCall`.
	*/

	/*
		TODO:
		Use seu `runtime` para chamar a função `execute_block` com seu novo bloco.
  		Se a função `execute_block` retornar um erro, você deve entrar em pânico!
  		Nós `esperamos` que todos os blocos sendo executados sejam válidos.
	*/

	// Simplesmente imprima o formato de depuração do estado do nosso runtime.
	println!("{:#?}", runtime);
}
```