Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/98709af2614319e0208b4c394b503166).

# Adicionando a Macro Call ao PoE

[Youtube](https://youtu.be/CzwZJCwidPs?si=wZ7v6ffeIX9i9xVh)

Já vimos a macro `#[macros::call]` ajudar a limpar o Pallet de Balances.

Vamos também adicioná-la ao Pallet de Proof of Existence, onde há ainda mais código que pode ser eliminado.

## Adicione Sua Macro Call

Basicamente, precisamos repetir os passos que fizemos para o Pallet de Balances aqui:

1. Mova suas funções `create_claim` e `revoke_claim` para seu próprio `impl<T: Config> Pallet<T>`.
2. Adicione o atributo `#[macros::call]` sobre esse novo `impl<T: Config> Pallet<T>`.
3. Exclua seu `enum Call` existente.
4. Exclua sua implementação existente de `Dispatch for Pallet`.
5. Em seu arquivo `main.rs`, altere as instâncias de:
   - `proof_of_existence::Call::CreateClaim` para `proof_of_existence::Call::create_claim` usando `snake_case`.
   - `proof_of_existence::Call::RevokeClaim` para `proof_of_existence::Call::revoke_claim` usando `snake_case`.

Verifique se tudo está compilando e rodando como antes.

No `proof_of_existence.rs`:

```rust
/// ...código anterior.

/* TODO: Adicione o atributo `#[macros::call]` aqui também. Faça as mudanças necessárias neste pallet. */
impl<T: Config> Pallet<T> {
	/// Crie uma nova instância do Módulo de Proof of Existence.
	pub fn new() -> Self {
		Self { claims: BTreeMap::new() }
	}

	/// Obtenha o proprietário (se houver) de uma claim.
	pub fn get_claim(&self, claim: &T::Content) -> Option<&T::AccountId> {
		self.claims.get(&claim)
	}

	/// Crie uma nova claim em nome do `caller`.
	/// Esta função retornará um erro se alguém já tiver reivindicado esse conteúdo.
	pub fn create_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		if self.claims.contains_key(&claim) {
			return Err(&"this content is already claimed");
		}
		self.claims.insert(claim, caller);
		Ok(())
	}

	/// Revogue uma claim existente sobre algum conteúdo.
	/// Esta função só deve ter sucesso se o chamador for o proprietário de uma claim existente.
	/// Ela retornará um erro se a claim não existir ou se o chamador não for o proprietário.
	pub fn revoke_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		let owner = self.get_claim(&claim).ok_or("claim does not exist")?;
		if caller != *owner {
			return Err(&"this content is owned by someone else");
		}
		self.claims.remove(&claim);
		Ok(())
	}
}

/// ...código anterior.
```

On `main`:

```rust
/// ...código anterior.

fn main() {
	/// ...código anterior.

	/* TODO: Atualize as extrinsics abaixo para o formato atualizado após as macros. */
	let block_2 = types::Block {
		header: support::Header { block_number: 2 },
		extrinsics: vec![
			support::Extrinsic {
				caller: alice.clone(),
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::CreateClaim {
					claim: &"Hello, world!",
				}),
			},
			support::Extrinsic {
				caller: bob.clone(),
				call: RuntimeCall::ProofOfExistence(proof_of_existence::Call::CreateClaim {
					claim: &"Hello, world!",
				}),
			},
		],
	};

	/// ...código anterior.
}
```

## Expanda seu Código Rust

Vamos aproveitar a oportunidade para mostrar como você pode dar uma olhada mais profunda no que as macros estão fazendo.

Rust fornece o comando `cargo expand`, que permite gerar o código Rust após todas as macros terem sido aplicadas ao seu projeto.

Execute o seguinte comando:

```bash
cargo expand > out.rs
```

Isso gerará o código do seu projeto em um arquivo `out.rs`.

Em seguida, dê uma olhada nesse arquivo.

Aqui estão algumas coisas que você deve notar:

- Todos os seus diferentes arquivos `mod` foram combinados em um único arquivo com seu `main.rs`.
- Você verá que o código final do Pallet tem toda a lógica de `Call` e `Dispatch` gerada!
- Você pode notar que a primeira macro `#[derive(Debug)]` gerou o código:

	```rust
    #[automatically_derived]
    impl<T: ::core::fmt::Debug + Config> ::core::fmt::Debug for Pallet<T>
    where
        T::Content: ::core::fmt::Debug,
        T::AccountId: ::core::fmt::Debug,
    {
        fn fmt(&self, f: &mut ::core::fmt::Formatter) -> ::core::fmt::Result {
            ::core::fmt::Formatter::debug_struct_field1_finish(f, "Pallet", "claims", &&self.claims)
        }
    }
	```
- Você pode até notar que outras macros menores, como `vec![]`, mudaram:

	```rust
	extrinsics: <[_]>::into_vec(
		#[rustc_box]
		::alloc::boxed::Box::new([
			// coisas
		])
	)
	```
- E `println!()`:

	```rust
	{
		::std::io::_print(format_args!("{0:#?}\n", runtime));
	};
	```
- etc...

Há duas principais conclusões para você:

1. As macros, em última análise, seguem todas as mesmas regras do código Rust regular, porque geram código Rust regular. Elas parecem mágicas, mas realmente não há nada de mágico nelas.
2. As macros são uma parte importante do ecossistema Rust e são amplamente usadas para melhorar a experiência do desenvolvedor e a qualidade do código.

Se você usar macros desenvolvidas externamente e quiser dar uma olhada mais de perto no que está acontecendo, `cargo expand` pode ser uma ferramenta útil para você entender melhor alguns dos detalhes arquitetônicos ocultos de um projeto. Ao entrar no SDK do Polkadot, recomendo que continue usando essa ferramenta para aprimorar seu aprendizado e entendimento.