Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/d73d5173a7f5d40909bad87604b76270).

# Funções de Prova de Existência

[Youtube](https://www.youtube.com/watch?v=0uE1hQSl5_0)

O Pallet de Prova de Existência é bastante simples, então vamos construir a lógica necessária.

## Obter Reivindicação

Nosso Pallet tem um mapa de armazenamento simples de algum conteúdo de reivindicação para o proprietário dessa reivindicação.

A função `get_claim` deve atuar como uma função de leitura simples, retornando o `T::AccountId` do proprietário, se houver. No caso de consultarmos uma reivindicação que não possui proprietário, devemos retornar `None`.

Esta não é uma função que um usuário chamaria a partir de um extrínseco, mas é útil para outras partes da sua máquina de estado acessarem os dados neste Pallet.

## Criar Reivindicação

Qualquer usuário pode adicionar uma nova reivindicação ao Pallet de Prova de Existência.

A única coisa importante é verificar se a reivindicação ainda não foi feita por outro usuário.

Cada reivindicação deve ter apenas um proprietário, e quem fizer a reivindicação primeiro tem prioridade.

Você pode verificar se alguma reivindicação já está no armazenamento `claims` usando a API `contains_key`:

```rust
if self.claims.contains_key(&claim) {
	return Err(&"este conteúdo já foi reivindicado");
}
```

## Revogar Reivindicação

Dados na blockchain não são gratuitos e, de fato, são muito caros de manter. Dar aos usuários a capacidade de limpar seus dados não é apenas bom, mas incentivado. Se um usuário não precisar mais armazenar sua reivindicação na cadeia, ele deve limpá-la.

Além disso, a história da blockchain é imutável. Mesmo que os dados sobre uma reivindicação não existam no "estado atual", pode-se mostrar que existiam no passado.

Manter coisas no estado atual apenas facilita a consulta de informações.

Para revogar uma reivindicação, precisamos verificar duas coisas:

1. Que a reivindicação existe.
2. Que a pessoa que deseja revogar a reivindicação é o proprietário dessa reivindicação.

Você deve ser capaz de lidar com toda essa lógica chamando a função `get_claim` e usando `ok_or` para retornar um erro quando a reivindicação não existir. Se a reivindicação existir, você deve ser capaz de extrair diretamente o proprietário da consulta de estado.

## Exercícios:

### Construa Suas Funções

Complete os `TODO`s descritos no template.

Depois, crie um teste `basic_proof_of_existence` para verificar se todas as suas funções estão funcionando conforme esperado.

Isso inclui tanto as condições de sucesso quanto os possíveis erros do seu Pallet.

No `proof_of_existence.rs`:

```rust
use crate::support::DispatchResult;
use core::fmt::Debug;
use std::collections::BTreeMap;

pub trait Config: crate::system::Config {
	/// O tipo que representa o conteúdo que pode ser reivindicado usando este pallet.
	/// Pode ser o conteúdo diretamente como bytes, ou melhor ainda, o hash desse conteúdo.
	/// Deixamos essa decisão para o desenvolvedor do runtime.
	type Content: Debug + Ord;
}

/// Este é o Módulo de Prova de Existência.
/// É um módulo simples que permite que contas reivindiquem a existência de alguns dados.
#[derive(Debug)]
pub struct Pallet<T: Config> {
	/// Um simples mapa de armazenamento de conteúdo para o proprietário desse conteúdo.
	/// As contas podem fazer várias reivindicações diferentes, mas cada reivindicação só pode ter um proprietário.
	claims: BTreeMap<T::Content, T::AccountId>,
}

impl<T: Config> Pallet<T> {
	/// Cria uma nova instância do Módulo de Prova de Existência.
	pub fn new() -> Self {
		Self { claims: BTreeMap::new() }
	}

	/// Obtém o proprietário (se houver) de uma reivindicação.
	pub fn get_claim(&self, claim: &T::Content) -> Option<&T::AccountId> {
		/* TODO: `get` o `claim` */
		unimplemented!()
	}

	/// Cria uma nova reivindicação em nome do `caller`.
	/// Esta função retornará um erro se alguém já tiver reivindicado esse conteúdo.
	pub fn create_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		/* TODO: Verifique se uma `claim` não existe. Se existir, retorne um erro. */
		/* TODO: `insert` a reivindicação em nome do `caller`. */
		Ok(())
	}

	/// Revoga uma reivindicação existente em algum conteúdo.
	/// Esta função só deve ter sucesso se o chamador for o proprietário de uma reivindicação existente.
	/// Retornará um erro se a reivindicação não existir ou se o chamador não for o proprietário.
	pub fn revoke_claim(&mut self, caller: T::AccountId, claim: T::Content) -> DispatchResult {
		/* TODO: Obtenha o proprietário da `claim` a ser revogada. */
		/* TODO: Verifique se o `owner` corresponde ao `caller`. */
		/* TODO: Se todas as verificações passarem, então `remove` a `claim`. */
		Ok(())
	}
}

#[cfg(test)]
mod test {
	struct TestConfig;

	impl super::Config for TestConfig {
		type Content = &'static str;
	}

	impl crate::system::Config for TestConfig {
		type AccountId = &'static str;
		type BlockNumber = u32;
		type Nonce = u32;
	}

	#[test]
	fn basic_proof_of_existence() {
		/*
			TODO:
			Crie um teste de ponta a ponta verificando a funcionalidade básica deste pallet.
				- Verifique se o estado inicial é como você espera.
				- Verifique se todas as funções funcionam com sucesso.
				- Verifique se todas as condições de erro retornam erro como esperado.
		*/
	}
}
```

Implementar as funções de Prova de Existência é um passo crítico na construção de um módulo funcional de blockchain. Ótimo trabalho em completar esta lição!

Se você ficar preso ou sentir-se perdido ao criar, obter ou revogar reivindicações, não hesite em pedir ajuda no canal [#🆘・section-5](https://discord.com/channels/898706705779687435/1261079360341934161) no Discord. Até super-heróis precisam de uma mãozinha às vezes! 🦸‍♂️🦸‍♀️