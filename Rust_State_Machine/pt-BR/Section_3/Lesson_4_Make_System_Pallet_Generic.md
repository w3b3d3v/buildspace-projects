Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/742dd51b323d9a12c6d8c598c7aaf3da).

# Exercícios:

### Tornando o Pallet de Sistema Genérico

[Youtube](https://youtu.be/p9R6FEaOd7E?si=EvymG0SLUEPCwoRx)

Agora que você tem alguma prática com o Pallet de Saldos, vamos fazer a mesma tarefa para o Pallet de Sistema.

1. Neste caso, precisamos tornar o Sistema genérico sobre `AccountId`, `BlockNumber`, e `Nonce`.

2. Você também precisará descobrir as restrições de trait necessárias para que esses tipos sejam compatíveis com a lógica que você escreveu anteriormente. O compilador é seu amigo aqui para ajudá-lo a navegar em tudo.

3. Atualize seus testes.

4. Finalmente, mova suas definições de tipo para o seu arquivo `main.rs` e atualize seu `Runtime`.

Certifique-se de que tudo compile e todos os testes passem após esta etapa.

Se precisar de ajuda, recomendo olhar para o seu Pallet de Saldos em vez da solução para esta etapa. Todos os padrões são os mesmos de antes, então é melhor que você comece a conectar os pontos sozinho em vez de depender da solução.

Se você teve dificuldades aqui, é uma boa oportunidade para fazer uma pausa e revisar tipos genéricos a partir de outros exemplos no ecossistema Rust.

No `main.rs`:

```rust
mod balances;
mod system;

// Estes são os tipos concretos que usaremos em nossa máquina de estado simples.
// Módulos são configurados diretamente para esses tipos e eles satisfazem todos os nossos
// requisitos de traits.
mod types {
	pub type AccountId = String;
	pub type Balance = u128;
	/* TODO: Mova suas definições de tipo para `BlockNumber` e `Nonce` aqui. */
}

// Este é o nosso Runtime principal.
// Acumula todos os diferentes paletes que queremos utilizar.
#[derive(Debug)]
pub struct Runtime {
	/* TODO: Use suas definições de tipo para seu novo `system::Pallet` genérico */
	system: system::Pallet,
	balances: balances::Pallet<types::AccountId, types::Balance>,
}
```

No `system.rs`:

```rust
use std::collections::BTreeMap;

type AccountId = String;
type BlockNumber = u32;
type Nonce = u32;

/*
	TODO:
	Atualize a estrutura `Pallet` para ser genérica sobre os tipos `AccountId`, `BlockNumber` e `Nonce`.
  	Você não precisará das definições de tipo acima depois de terminar.
  	Os tipos agora serão definidos em `main.rs`. Veja os TODOs lá.
*/

/// Este é o Palete do Sistema.
/// Ele lida com o estado de baixo nível necessário para seu blockchain.
#[derive(Debug)]
pub struct Pallet {
	/// O número do bloco atual.
	block_number: BlockNumber,
	/// Um ​​mapa de uma conta para seu nonce.
	nonce: BTreeMap<AccountId, Nonce>,
}

/*
	TODO:
	Os tipos genéricos precisam satisfazer certas características para serem usados ​​nas funções abaixo.
  	Veja se você consegue descobri-los sozinho.

 	NOTA: Pode ser necessário ajustar algumas das funções abaixo para satisfazer o verificador de empréstimo.
*/

/// ...código anterior.

#[cfg(test)]
mod test {
	#[test]
	fn init_system() {
		/*
			TODO:
			Ao criar uma instância de `Pallet`, você deve definir explicitamente os tipos que usa.
		*/
		let mut system = super::Pallet::new();
		
        /// ...código anterior.
	}
}
```