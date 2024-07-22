Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/d116a728d944026dd9fd4f3f689b75cf).

# Adicionando Interação com Saldos

[Youtube](https://youtu.be/coBI_avKIMw?si=qb-E3eBsOI_aFiOa)

Agora que estabelecemos os conceitos básicos do nosso módulo de saldos, vamos adicionar maneiras de interagir com ele.

Para fazer isso, continuaremos a criar mais funções implementadas em `Pallet` que concedem acesso para ler, escrever e atualizar o  `balances: BTreeMap` que criamos.

Finalmente, veremos como é realmente começar a interagir com nosso pallet de saldos a partir do arquivo `main.rs`.

## Conhecimento Pré-requisito de Rust

Antes de continuarmos, vamos dedicar um momento para revisar alguns conceitos de Rust que usaremos nesta próxima seção.

### Option e Tratamento de Option

Um dos principais princípios de Rust é remover comportamento indefinido do seu código.

Uma maneira de ocorrer comportamento indefinido é permitindo que estados como `null` existam. Rust previne isso fazendo com que o usuário trate explicitamente todos os casos, e é aqui que entra a criação do tipo `Option` Dedique um momento para revisar [a seção sobre `Option`](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html?highlight=option#the-option-enum-and-its-advantages-over-null-values) do livro de Rust, se necessário.

A API do `BTreeMap` usa um `Option` ao ler valores do mapa, já que pode ser que você peça para ler o valor de alguma chave que você não definiu. Por exemplo:

```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert("alice", 100);
assert_eq!(map.get(&"alice"), Some(&100));
assert_eq!(map.get(&"bob"), None);
```

Uma vez que temos um tipo `Option`, existem muitas maneiras diferentes de interagir com ele usando Rust.

A maneira mais verbosa é usando uma declaração de `match`:

```rust
let maybe_value = map.get(&"alice");
match maybe_value {
	Some(value) => {
		// fazer algo com o `value`
	},
	None => {
		// talvez retornar um erro já que não havia valor lá
	}
}
```


> 🚨 **Alerta:** O que você **NÃO DEVE** fazer é usar `unwrap()` cegamente em opções. Isso resultará em um  `panic` no seu código, o que é exatamente o tipo de coisa que Rust foi projetado para prevenir! Em vez disso, você deve sempre tratar explicitamente todos os seus diferentes casos lógicos, e se deixar que Rust faça seu trabalho, seu código será super seguro.

No contexto do que estamos projetando para o módulo de saldos, temos um mapa que possui um número arbitrário de chaves de usuário e seus valores de saldo.

O que devemos fazer quando lemos o saldo de um usuário que não existe no nosso mapa?

Bem, o truque aqui é que no contexto das blockchains, um usuário ter None saldo e um usuário ter  `0` saldo é a mesma coisa. Claro, há alguns detalhes mais finos a serem expressos entre um usuário que existe em nosso estado com valor 0 e um usuário que não existe de todo, mas para os propósitos de nossas APIs, podemos tratá-los da mesma forma.

Como isso se parece?

Bem, podemos usar `unwrap_or(...)` para tratar essa condição com segurança e tornar nossas futuras APIs mais ergonômicas de usar. Por exemplo:

```rust
use std::collections::BTreeMap;

let mut map = BTreeMap::new();
map.insert("alice", 100);
assert_eq!(*map.get(&"alice").unwrap_or(&0), 100);
assert_eq!(*map.get(&"bob").unwrap_or(&0), 0);
```

Como você pode ver, ao usar `unwrap_or(&0)` após ler do nosso mapa, somos capazes de transformar nosso `Option` em um inteiro básico, onde usuários com algum valor têm seu valor exposto e usuários com `None` são transformados em `0`.

Vamos ver como isso pode ser usado a seguir.

## Definindo e Lendo Saldos de Usuários

Como você pode ver, nossa máquina de estados inicial começa com todos sem saldo.

Para tornar nosso módulo útil, precisamos ter pelo menos algumas funções que nos permitam criar novos saldos para usuários e ler esses saldos.

1. Crie uma nova função dentro de `impl Pallet` chamada `fn set_balance`:

	```rust
	impl Pallet {
		pub fn set_balance(&mut self, who: &String, amount: u128) {
			self.balances.insert(who, amount);
		}

		// -- snip --
	}
	```

	Como você pode ver, esta função simplesmente recebe informações sobre qual usuário queremos definir o saldo e qual saldo queremos definir. Isso então empurra essa informação para nosso `BTreeMap`, e isso é tudo.

2. Crie uma nova função dentro de `impl Pallet` chamada `fn balance`:

	```rust
	pub fn balance(&self, who: &String) -> u128 {
		*self.balances.get(&who).unwrap_or(&0)
	}
	```

	Como você pode ver, esta função nos permite ler o saldo dos usuários em nosso mapa. A função permite que você insira algum usuário e nós retornaremos o saldo dele.

	> 🚨 **Alerta:** Note que fazemos nosso pequeno truque aqui! Em vez de expor uma API que força o usuário a lidar com um  `Option` somos capazes de fazer nossa API sempre retornar um `u128` convertendo qualquer usuário com valor  `None` em `0`.

## Exercício:

No `balances.rs`:
```rust
impl Pallet {
	/// Cria uma nova instância do módulo de saldos.
	pub fn new() -> Self {
		Self { balances: BTreeMap::new() }
	}

	/// Define o saldo de uma conta `who` para algum `amount`.
	pub fn set_balance(&mut self, who: &String, amount: u128) {
		/* Insira `amount` no BTreeMap sob `who`. */
		self.balances.insert(who.clone(), amount);
	}

	/// Obtém o saldo de uma conta `who`.
	/// Se a conta não tiver saldo armazenado, retornamos zero.
	pub fn balance(&self, who: &String) -> u128 {
		/* Retorna o saldo de `who`, retornando zero se `None`. */
		*self.balances.get(who).unwrap_or(&0)
	}
}
```

A seguir, escreveremos nosso primeiro teste e realmente interagiremos com nosso módulo de saldos. Animado para a próxima etapa? Nós estamos! 
