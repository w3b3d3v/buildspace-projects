# Tornando o Pallet de Saldos Genérico

[Youtube](https://youtu.be/Yq_WZJrDVhk?si=hdJbSEHav_zbJiTg)

Nosso objetivo nas próximas etapas será tornar nosso runtime mais genérico e configurável em relação aos tipos que usamos em nossos Pallets.

## Por que Genérico?

A flexibilidade de um runtime genérico significa que podemos escrever código que funcione para várias configurações e tipos diferentes.

Por exemplo, até agora, usamos `&'static str` para representar as contas dos usuários. Isso obviamente não é a coisa certa a fazer, mas é fácil de implementar para um tutorial básico de blockchain como este.

O que você precisaria mudar para usar chaves públicas criptográficas mais tradicionais?

Bem, atualmente há definições do tipo de conta tanto no Pallet de Saldos quanto no Pallet de Sistema. Imagine se você tivesse muitos mais Pallets também! Esse tipo de refatoração poderia ser muito difícil, mas totalmente evitado se usássemos tipos genéricos desde o início.

Na verdade, a vantagem dos tipos genéricos não será super evidente neste tutorial, mas ao construir um SDK de blockchain como o Substrate, esse tipo de flexibilidade permitirá que os desenvolvedores do ecossistema alcancem seu potencial máximo.

Por exemplo, equipes usaram o Substrate para construir blockchains totalmente compatíveis com Ethereum, enquanto outras equipes experimentaram primitivos criptográficos de ponta. Esse framework genérico permite que ambas as equipes sejam bem-sucedidas.

## Tipos Genéricos

Você já foi ligeiramente exposto a tipos genéricos com o tipo `Result`. Lembre-se de que esse tipo é flexível para permitir que você configure qual tipo é retornado quando há `Ok` ou `Err`.

Se quisermos tornar nosso `Pallet` genérico, ele pareceria algo assim:

```rust
pub struct Pallet<AccountId, Balance> {
 	balances: BTreeMap<AccountId, Balance>,
}
```

E implementar funções em `Pallet` pareceria assim:

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance> {
	// funções que usam esses tipos
}
```

Nesse caso, não definimos o que o tipo `AccountId` e `Balance` são concretamente, apenas que armazenaremos um `BTreeMap` onde o tipo `AccountId` é uma chave e o tipo `Balance` é um valor.

### Restrições de Traits

O tipo genérico `Result` é extremamente flexível porque não há restrições sobre o que o tipo `Ok` ou `Err` deve ser. Qualquer tipo funcionará para essa situação.

No entanto, nossos Pallets não são tão flexíveis. O tipo `Balance` não pode ser literalmente qualquer tipo. Porque temos funções como `fn transfer`, devemos exigir que o tipo `Balance` pelo menos tenha acesso à função `checked_sub`, `checked_add` e tenha alguma representação de `zero`.

É aqui que o crate `num` será útil. Do crate `num`, você pode importar traits que definem tipos que expõem essas funções:

```rust
use num::traits::{CheckedAdd, CheckedSub, Zero};
```

Então, onde aplicável, você precisa restringir seus tipos genéricos para ter essas traits.

Isso parecerá assim:

```rust
impl<AccountId, Balance> Pallet<AccountId, Balance>
where
	AccountId: Ord,
	Balance: Zero + CheckedSub + CheckedAdd + Copy,
{
	// functions which use these types and have access to the traits specified
}
```

Você notará outros tipos como `Copy` e `Ord` que foram adicionados. Essas restrições vêm do uso de estruturas como o `BTreeMap`, que requer que o tipo de chave seja "ordenável".

Você pode realmente tentar compilar seu código sem essas restrições de tipo, e o compilador lhe dirá quais traits você está faltando e o que precisa incluir.

### Instanciando um Tipo Genérico

A peça final do quebra-cabeça é instanciar nossos tipos genéricos.

Anteriormente, poderíamos simplesmente escrever:

```rust
let mut balances = super::Pallet::new();
```

Mas agora que `Pallet` é genérico, precisamos definir concretamente esses tipos ao instanciá-lo.

Essa sintaxe parece assim:

```rust
let mut balances = super::Pallet::<&'static str, u128>::new();
```

Você notará que agora os tipos são definidos onde quer que a `struct Pallet` genérica esteja sendo instanciada. Isso significa que você pode extrair os tipos de seus Pallets e movê-los para o Runtime.

# Exercícios:

### Torne-se Genérico!

É hora de tornar seu pallet de saldos genérico.

1. Siga os `TODO`s no arquivo `balances.rs` para tornar `Pallet` genérico.
2. Mova as definições de tipo para `AccountId` e `Balance` para o seu `main.rs`.
3. Atualize sua `struct Runtime` para usar esses tipos ao definir o `balances::Pallet`.

Para ser honesto, este é um dos lugares onde os desenvolvedores mais frequentemente têm problemas ao aprender Rust, por isso há tanta ênfase em ensiná-lo e fazer você aprender fazendo esses passos sozinho.

Não tenha medo nesta etapa de espiar a solução se ficar preso, mas tente aprender os padrões de uso de tipos genéricos e o que toda a sintaxe significa em termos do que o compilador está tentando garantir sobre a segurança dos tipos.

No `balances.rs`:

```rust
/* TODO: Você pode precisar importar algumas coisas para este passo. */
use std::collections::BTreeMap;

type AccountId = String;
type Balance = u128;

/*
	TODO: 
	Atualize a struct `Pallet` para ser genérica em relação aos tipos `AccountId` e `Balance`.

	Você não precisará das definições de tipo abaixo depois de concluir.
	Os tipos agora serão definidos em `main.rs`. Veja os TODOs lá.
*/

/// Este é o Módulo de Saldos.
/// É um módulo simples que mantém o controle de quanto saldo cada conta possui nesta máquina de estados.
#[derive(Debug)]
pub struct Pallet {
    // Um armazenamento simples mapeando contas para seus saldos.
	balances: BTreeMap<AccountId, Balance>,
}

/*
  TODO:
  Os tipos genéricos precisam satisfazer certas características para serem usados ​​nas funções abaixo.
  - ID da conta: pedido
  - Saldo: Zero + CheckedSub + CheckedAdd + Copiar

  Você pode descobrir essas características deixando o compilador dizer o que está faltando.

  NOTA: Pode ser necessário ajustar algumas das funções abaixo para satisfazer o verificador de empréstimo.
*/

/// ...código anterior.

#[cfg(test)]
mod tests {
	#[test]
	fn init_balances() {
		/*
			TODO:
			Ao criar uma instância de `Pallet`, você deve definir explicitamente os tipos que usa.
		*/
		let mut balances = super::Pallet::new();

		/// ...código anterior.
	}

	#[test]
	fn transfer_balance() {
		/*
			TODO:
			Ao criar uma instância de `Pallet`, você deve definir explicitamente os tipos que usa.
		*/
		let mut balances = super::Pallet::new();

		/// ...código anterior.
	}
}
```

No `main.rs`:
```rust
mod balances;
mod system;

// Estes são os tipos concretos que usaremos em nossa máquina de estados simples.
// Os módulos são configurados diretamente para esses tipos e satisfazem todos os nossos
// requisitos de característica.
mod types {
	/*
		TODO: Mova suas definições de tipo para `AccountId` e `Balance` aqui.
	*/
}

// Este é o nosso Runtime principal.
// Acumula todos os diferentes paletes que queremos utilizar.
#[derive(Debug)]
pub struct Runtime {
	system: system::Pallet,
	/* TODO: Use suas definições de tipo para seus novos `balances::Pallet` genéricos. */
	balances: balances::Pallet,
}
```

Tornar o Balances Pallet genérico é uma habilidade crucial para a criação de soluções blockchain flexíveis e escaláveis. Ótimo trabalho ao concluir esta lição! 🌟
Poste uma captura de tela em [#progress](https://discord.com/channels/898706705779687435/980906289968345128) mostrando seu tempo de execução com o novo Balances Pallet genérico em ação. É melhor ainda não estar usando &'static str!