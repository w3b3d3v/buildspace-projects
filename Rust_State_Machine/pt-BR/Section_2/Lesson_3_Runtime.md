Você pode encontrar a [solução para a etapa anterior aqui](https://gist.github.com/nomadbitcoin/4d41571e1155cb262c116e88b185bcd3).

# Criando Nosso Runtime

[Youtube](https://youtu.be/uXzBcZgrH50?si=igqKuiLIKXmIMkf3)

Agora estabelecemos dois Pallets diferentes para nossa blockchain: o Pallet de Sistema e o Pallet de Saldos.

Como esses pallets funcionam juntos para criar um sistema de blockchain unificado?

Para isso, precisaremos criar um `Runtime`.

## O que é o Runtime?

Lembre-se de que há uma separação entre o cliente da blockchain e a função de transição de estado da nossa blockchain.

Você pode pensar no Runtime como a acumulação de toda a lógica que compõe sua função de transição de estado. Ele combinará todos os seus pallets em um único objeto e, em seguida, exporá esse objeto único como o ponto de entrada para os usuários interagirem.

Certamente isso parece bastante abstrato, mas fará mais sentido à medida que completarmos este tutorial.

## Criar o Runtime

Assim como nossos Pallets, nosso Runtime será representado por uma `struct`simples, no entanto, neste caso, os campos da nossa  `struct` serão nossos Pallets!

Complete as instruções para criar um novo runtime que inclua nossos pallets de Sistema e Saldos. Para isso, você precisará aproveitar as funções `new()` que expusemos para cada um dos Pallets.

## Exercícios

No `main.rs`:

```rust
mod balances;
mod system;

// Este é o nosso Runtime principal.
// Ele acumula todos os diferentes pallets que queremos usar.
pub struct Runtime {
	/* TODO: cria um campo `system` que é do tipo `system::Pallet`. */
	/* TODO: cria um campo `balances` que é do tipo `balances::Pallet`. */
}

impl Runtime {
    // Cria uma nova instância do Runtime principal, criando uma nova instância de cada pallet.
	fn new() -> Self {
		/* TODO: Crie um novo `Runtime` criando novas instâncias de `system` e `balances`. */
		unimplemented!()
	}
}

fn main() {
	println!("Hello, world!");
}
```

É isso :). Vamos seguir em frente! Estou impressionado com seu progresso!

Se precisar de assistência ou tiver dúvidas sobre como integrar seus pallets no Runtime, sinta-se à vontade para pedir ajuda no canal [#🆘・section-2](https://discord.com/channels/898706705779687435/980905562566967427) no Discord. Estamos aqui para apoiar você em sua jornada!