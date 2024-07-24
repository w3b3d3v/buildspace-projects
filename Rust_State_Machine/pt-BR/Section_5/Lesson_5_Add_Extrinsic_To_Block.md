Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/81c56b7ae67dd2f4524d999e4879c05f).

# Adicionar Extrinsics de PoE aos Blocos

O Pallet de Prova de Existência está totalmente integrado ao seu runtime neste ponto, mas ainda não o estamos utilizando.

Crie alguns novos `Block`s em sua função `fn main()` para testar a funcionalidade do Pallet de Prova de Existência.

Seja criativo e sinta-se à vontade para introduzir alguns extrinsics que desencadearão erros com base na lógica dos seus pallets.

Não se esqueça de incrementar o número do bloco e chamar `execute_block` para cada um desses blocos.

Veja o resultado final e verifique se o estado da sua máquina faz sentido!

No `main.rs`:

```rust
/// ... previous code.

fn main() {
	// Crie uma nova instância do Runtime.
	// Ela instanciará todos os módulos que utiliza.
	let mut runtime = Runtime::new();
	let alice = "alice".to_string();
	let bob = "bob".to_string();
	let charlie = "charlie".to_string();

	// Inicialize o sistema com algum saldo inicial.
	runtime.balances.set_balance(&alice, 100);

	// Aqui estão os extrinsics no nosso bloco.
	// Você pode adicionar ou remover esses extrinsics com base nos módulos e chamadas que configurou.
	let block_1 = types::Block {
		header: support::Header { block_number: 1 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::Balances(balances::Call::Transfer { to: bob, amount: 20 }),
			},
			support::Extrinsic {
				caller: alice,
				call: RuntimeCall::Balances(balances::Call::Transfer { to: charlie, amount: 20 }),
			},
		],
	};

	/*
		TODO:
		Crie novos bloco(s) que executem extrinsics para o novo pallet `ProofOfExistence`.
			- Certifique-se de definir o número do bloco corretamente.
			- Sinta-se à vontade para permitir que alguns extrinsics falhem e veja os erros aparecerem.
	*/

	//Executa os extrínsecos que compõem nosso bloco.
  // Se houver algum erro, nosso sistema entra em pânico, pois não devemos executar blocos inválidos.
	runtime.execute_block(block_1).expect("invalid block");
	/* TODO: Execute seu(s) novo(s) bloco(s). */

  	// Simplesmente imprima o formato de depuração do nosso estado de tempo de execução.
	println!("{:#?}", runtime);
}
```

### 🔥 On Fire
VOCÊ FEZ DE NOVO.

A Seção 5 está completa! Seu trabalho duro está valendo a pena. Continue assim :).