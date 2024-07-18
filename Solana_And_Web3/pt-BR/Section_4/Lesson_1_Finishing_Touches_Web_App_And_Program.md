<h1>🧹Toques finais</h1>
### ✈️ Fazendo atualizações no seu programa

Digamos que você queira adicionar alguma nova funcionalidade ao seu programa Solana.

Eu disse anteriormente para não tocar em `lib.rs` porque trabalhar localmente e reimplantar fica estranho.

Então, basicamente com Solana eu quase *nunca* trabalho em `localnet` normalmente. É realmente irritante alternar entre `localnet` e `devnet` constantemente.

Em vez disso, atualizo meu programa e, em seguida, para testá-lo, basta executar meu script via `anchor test` em `tests/myepicproject.js` para ter certeza de que as coisas estão funcionando e o Anchor realmente executará o teste em `devnet` diretamente, o que é muito legal.

Então, quando eu estiver pronto para testar as atualizações do meu programa no meu aplicativo da web — eu apenas faço um `anchor deploy`. A partir daí, você precisa ter certeza de pegar o arquivo IDL atualizado para seu aplicativo da web.

**Sempre que você reimplantar, você precisa atualizar o arquivo IDL em seu aplicativo da web**

Assim como antes, você precisa copiar o conteúdo do IDL atualizado do seu projeto Solana em `target/idl/myepicproject.json` e depois colar o conteúdo dele em `app/src/idl.json` em seu _web app_.

Agora, quero falar sobre alguns recursos **opcionais** que você pode adicionar e que acho que seriam divertidos. Novamente, estes são opcionais. Eu também não vou orientá-lo sobre como construí-los. Vou deixar para você descobrir.

### 🏠 Mostre o endereço público de um usuário no _web app_

No momento, não estamos usando o endereço público do usuário para nada em:

```rust
pub struct ItemStruct {
    pub gif_link: String,
    pub user_address: Pubkey,
}
```

Pode ser legal mostrar o endereço público do usuário no GIF que eles enviaram!! Fazer algo como `item.userAddress.toString()` em seu _web app_ deve funcionar. Eu vou deixar você descobrir como implementá-lo, se você quiser.

### 🙉 Permita que os usuários votem em GIFs

Seria legal se cada GIF começasse com 0 votos e as pessoas pudessem dar um "upvote" em seus GIFs favoritos em seu aplicativo da web.

Não vou dizer-lhe como fazê-lo. 😉. Descubra se quiser! Dica: você precisará criar uma função `update_item` em seu programa Solana. Lá, você precisa descobrir como entrar em `gif_list`, encontrar o GIF que está sendo votado, e então fazer algo como `votes += 1` nele.

Veja se você consegue resolver!!

### 💰 Envie uma "gorjeta" de Solana para os remetentes dos melhores GIFs

Uma coisa que não abordamos aqui é como enviar dinheiro para outros usuários!

Seria super legal se você amasse tanto um determinado GIF enviado por outro usuário que pudesse enviar uma gorjeta a esse usuário. Talvez como 50 centavos ou como um dólar de SOL. Talvez você clique em "gorjeta", insira quanto SOL você deseja dar de gorjeta e clique em enviar para enviá-lo diretamente para a carteira desse usuário!

**A taxa de gás super baixa da Solana significa que enviar pequenas quantias de dinheiro como essa realmente faz sentido.** Se você fizer isso, poderá até criar uma versão do Patreon ou BuyMeACoffee em Solana. Não é tão louco. Você tem todas as habilidades básicas agora.

Quem precisa do Stripe e do PayPal quando você tem uma blockchain de taxa super baixa que permite fazer pagamentos instantâneos?!?

Esta é outra coisa que eu quero que você descubra se quiser ir no [Anchor Discord](https://discord.gg/wgM4KATaex) ou perguntar aos seus colegas da WEB3DEV. 

Não se esqueça que temos um canal no discord dedicado a Solana, poste updates, registre suas modifiações no seu webapp, tire suas dúvidas, compartilhe seus conhecimento, fique a vontade e explore as vantagens de nossa comunidade web3.

 **Por que não estou lhe contando as respostas?** Haha, porque eu quero que você seja ativo na comunidade Solana, descubra e aprenda lutando um pouco.

Uma dica: ser legal vai longe.

### 👍 Um monte de programas de exemplo

Existem vários programas de exemplo que você pode encontrar no repositório Anchor [aqui](https://github.com/project-serum/anchor/tree/master/tests). Você pode examinar diferentes exemplos para descobrir como implementá-lo você mesmo.
