Agora nossos membros tem um NFT para comprovar que são membros da nossa DAO. Perfeito. Vamos dar mais um passo. Vamos criar um fornecimento de tokens de governança para fazer um airdrop para os nosso membros.

Talvez você se lembre do airdrop do token de governança da ENS DAO [aqui](https://decrypt.co/85894/ethereum-name-service-market-cap-hits-1-billion-just-days-after-ens-airdrop). O que isso tudo significa? Por que um token de governança tem uma capitalização de mercado tem quase um bilhão de dólares [nesse momento](https://coinmarketcap.com/currencies/ethereum-name-service/)?

Basicamente, um token de goverança permite que usuários votem nas propostas. Por exemplo, uma proposta poderia dizer algo tipo "Eu quero que a MTBDAO envie 100,000 $BIKES para o endereço `0xf9aD3D930AB5df972558636A2B8749e772aC9297` por ser um membro extraordinário". Então os membros poderiam votar.

Usuários com mais tokens de governança são mais poderosos. Geralmente, tokens são dados para membros da comunidade que mais trouxeram valor.

Por exemplo, para o airdrop do ENS, as pessoas que mais receberam tokens foram as do time central de desenvolvimento e os usuários ativos no Discord deles. Mas você também poderia receber tokens da ENS DAO baseado em quanto tempo você teve seu domínio ENS (ex. `danicuki.eth`). A propósito, se você não sabe, um nome ENS é um NFT.

Então, quanto mais tempo você tivesse com o NFT, mais tokens você recebia.

Por que? Porque o time do ENS queria que os primeiros adeptos da rede fossem recompensados. Essa foi a fórmula deles:

![Untitled](https://i.imgur.com/syh3F01.png)

Eu quero ser claro, essa é uma fórmula personalizada! Sua DAO também pode ter uma fórmula personalizada. Talvez você também queira recompensar pessoas na sua DAO baseado em quanto tempo eles tiveram seu NFT de filiação. Tudo depende de você.

### 🥵 Faça Deploy do Seu Token.

Vamos criar nosso smart contract para o nosso token! Vá para `scripts/5-deploy-token.js` e adicione:

```jsx
import { AddressZero } from "@ethersproject/constants";
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // Faça o Deploy de um contracto ERC-20 padrão.
    const tokenAddress = await sdk.deployer.deployToken({
      // Qual o nome do seu token? Ex. "Ethereum"
      name: "Token de Governança da MTBDAO",
      // Qual o símbolo do seu token? Ex. "ETH"
      symbol: "BIKES",
      // Isso é para o caso de querermos vender o token,
      // nesse caso não queremos, por isso AddressZero de novo.
      primary_sale_recipient: AddressZero,
    });
    console.log(
      "✅ Módulo de token implantado com sucesso. Endereço:",
      tokenAddress,
    );
  } catch (error) {
    console.error("falha ao implantar módulo do token", error);
  }
})();
```

Nós chamamos o `sdk.deployer.deployToken` que vai fazer o deploy de um contrato [ERC-20](https://docs.openzeppelin.com/contracts/2.x/api/token/erc20) padrão para você, que é o padrão que todas as grandes moedas na Ethereum adotam. Tudo o que você precisa dar é o `name` e `symbol` do seu token! Se divirta com essa parte, não me copie é claro. Eu espero que você esteja construindo algo que **você** ache legal!

Aqui eu dou o símbolo BIKES para o meu token. A propósito — você pode ver o contrato exato que o thirdweb usa [aqui](https://github.com/nftlabs/nftlabs-protocols/blob/main/contracts/Coin.sol).

Aqui está o que eu recebo quando eu rodo:

```plaintext
$ node scripts/5-deploy-token.js 
👋 SDK inicializado pelo endereço: 0xf9aD3D930AB5df972558636A2B8749e772aC9297
✅ Módulo de token implantado com sucesso. Endereço: 0x551ab187397F8A2a7a599557C13cb2279071d4D5
```

Boom! O deploy do contrato do token foi feito. Se você for para [`https://goerli.etherscan.io/`](https://goerli.etherscan.io/) e pesquisar o endereço do módulo do token, você vai ver o contrato que acabou de subir. Novamente, você vai ver que o deploy foi feito da **sua carteira**, então **você é o dono**.

![Untitled](https://i.imgur.com/qyKEwoI.png)

Você pode até adicionar o seu token à Metamask como um token personalizado.

Cliquem em "Import Token":

![Untitled](https://i.imgur.com/WrcMWep.png)

Então cole o endereço do seu contrato ERC-20 e você vai ver que a Metamask magicamente pega o símbolo do seu token:

![Untitled](https://i.imgur.com/dMgdqao.png)

Então, volte para a sua carteira, role para baixo e boom!

![Untitled](https://i.imgur.com/f2ffsjm.png)

Você oficialmente tem o seu próprio token 😃.

### 💸 Crie o Fornecimento do Seu Token.

No momento, **existem zero tokens disponíveis para as pessoas reivindicarem.** Nosso contrato ERC-20 não sabe magicamente quantos tokens estão disponíveis. Nós temos que dizer!

Vá para `6-print-money.js` e adicione:

```jsx
import sdk from "./1-initialize-sdk.js";

// Esse é o endereço do nosso contrato ERC-20 impresso no passo anterior.
const token = sdk.getContract("INSERT_TOKEN_ADDRESS", "token");

(async () => {
  try {
    // Qual o fornecimento máximo que você quer? 1,000,000 é um número legal!
    const amount = 1_000_000;
    // Interaja com o seu contrato ERC-20 e cunhe os tokens!
    await token.mint(amount);
    const totalSupply = await token.totalSupply();
    
    // Mostre quantos dos seus tokens existem agora!
    console.log("✅ Agora temos", totalSupply.displayValue, "$BIKES em circulação");
  } catch (error) {
    console.error("Falha ao imprimir o dinheiro", error);
  }
})();
```
Lembre-se de que o endereço para inserir aqui é o endereço do seu **Token Module**. Se você colocar o endereço errado, talvez você receba um erro como esse: 'UNPREDICTABLE_GAS_LIMIT'.

Novamente, você pode ir no [dashboard do thirdweb](https://thirdweb.com/dashboard?utm_source=buildspace?utm_source=buildspace.so&utm_medium=buildspace_project) e procurar o endereço se você tiver perdido! Você deve ver que agora o módulo do token apareceu!

![image](https://i.imgur.com/3rmmKj5.png)


Então, aqui nós estamos de fato cunhando o fornecimento do token e configurando o `amount` que queremos cunhar e configuramos o fornecimento máximo do token. 

Aqui está o que eu recebo quando rodo o script:

```plaintext
$ node scripts/6-print-money.js 
👋 SDK inicializado pelo endereço: 0xf9aD3D930AB5df972558636A2B8749e772aC9297
✅ Agora temos 1000000.0 $BIKES em circulação
```

Agora para a parte épica. Volte para o seu contrato ERC-20 na Etherscan. Você vai ver que você tem seu próprio rastreador de token!

![Untitled](https://i.imgur.com/6GrjsSW.png)

Vá em frente e clique no rastreador e você vai ver toda a informação de fornecimento juntamente com coisas tipo: quem tem o seu token, quem está transferindo tokens, quantos tokens estão sendo transferidos. Você também verá que temos um "Total máximo de fornecimento".

Muito legal. Nós fizemos tudo isso usando apenas algumas linhas de javascript. Isso é insano. Você pode literalmente fazer a próxima moeda meme nesse ponto se você quiser kkkkkkkk.

![Untitled](https://i.imgur.com/OwbXkLU.png)

### ✈️ Faça o Airdrop.

Está na hora do airdrop. No momento você é provavelmente o único membro da sua DAO e está tudo bem!

Abra `7-airdrop-token.js` e adicione o código abaixo:

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // Esse é o endereço do nosso contrato ERC-1155 do NFT de filiação.
    const editionDrop = sdk.getEditionDrop("INSERT_EDITION_DROP_ADDRESS");
    // Esse é o endereço do nosso contrato ERC-20 do nosso token.
    const token = sdk.getToken("INSERT_TOKEN_ADDRESS","token");
    // Pegue o endereço de todas as pessoas que possuem o nosso NFT de filiação, que tem
    // o tokenId 0.
    const walletAddresses = await editionDrop.history.getAllClaimerAddresses(0);
  
    if (walletAddresses.length === 0) {
      console.log(
        "Ninguém mintou o NFT ainda, peça para alguns amigos fazerem isso e ganhar um NFT de graça!",
      );
      process.exit(0);
    }
    
    // faça um loop no array de endereços.
    const airdropTargets = walletAddresses.map((address) => {
      // Escolha um # aleatório entre 1000 e 10000.
      const randomAmount = Math.floor(Math.random() * (10000 - 1000 + 1) + 1000);
      console.log("✅ Vai enviar", randomAmount, "tokens para ", address);
      
      // Configure o alvo.
      const airdropTarget = {
        toAddress: address,
        amount: randomAmount,
      };
  
      return airdropTarget;
    });
    
    // Chame transferBatch em todos os alvos do airdrop.
    console.log("🌈 Começando o airdrop...")
    await token.transferBatch(airdropTargets);
    console.log("✅ Feito o airdrop de tokens para todos os donos de NFT!");
  } catch (err) {
    console.error("O airdrop de tokens falhou", err);
  }
})();
```

Isso é muita coisa. Mas você é um pro do thirdweb agora então GG!

Primeiro, você vai ver que precisamos tanto do `editionDrop` como do `token` porque vamos estar interagindo com ambos os contratos.

Primeiro precisamos pegar os titulares do nosso NFT do `editionDrop` e então cunhar para eles os seus tokens usando as funções do `token`.

Nós usamos `getAllClaimerAddresses` para pegar todos os `walletAddresses` das pessoas que tem nosso NFT de filiação com o token id `"0"`.

A partir daí, fazemos um loop através de todos os `walletAddresses` e escolhemos um `randomAmount` de tokens para dar para cada usuário e salvamos esses dados num dicionário `airdropTarget`.

Finalmente, rodamos `transferBatch` em todos os `airdropTargets`. E é isto! `transferBatch` vai automaticamente fazer um loop por todos os alvos e lhes mandar os tokens!

quando eu rodo o script eu recebo:

```plaintext
$ node scripts/7-airdrop-token.js 
👋 SDK inicializado pelo endereço: 0xf9aD3D930AB5df972558636A2B8749e772aC9297
✅ Vai enviar 7245 tokens para  0xf9aD3D930AB5df972558636A2B8749e772aC9297
✅ Vai enviar 6432 tokens para  0x28D65edF8C9C42a06Ed375D96e2BeF12D60B5705
✅ Vai enviar 7531 tokens para  0x65eF410254262fE97AF1501282F4e1c41C74AedB
🌈 Começando o airdrop...
✅ Feito o airdrop de tokens para todos os donos de NFT!
```

YOOOO. Você acabou de fazer um aidrop, isso aí!! No meu caso, você pode ver que eu tenho 3 membros únicos na minha DAO e todos eles receberam o airdrop. No seu caso, provavelmente vai ser só você por enquanto! Sinta-se à vontade para rodar esse script novamente quando novos membros se juntarem.

**No mundo real**, um airdrop geralmente acontece apenas uma vez. Mas nós estamos apenas aprendendo então está tudo bem. Além do mais, não existem regras de verdade nesse mundo kkkkkkkk. Se você quiser fazer 4 airdrops no dia faça!

Você poderia criar a sua própria fórmula como o ENS fez por exemplo:

![Untitled](https://i.imgur.com/IqboZsX.png)

Você pode pensar — “As pessoas que estão recebendo o token vão ter mais poder sobre a DAO. Isso é bom? Os maiores detentores de tokens vão fazer o que é certo pra DAO?”. Isso entra num tópico chamado tokenomics o qual você pode ler sobre [aqui](https://www.web3dev.com.br/yanluiz/tokenomics-101-daos-2fii), [aqui](https://www.web3dev.com.br/arnaldocampos/introducao-a-tokenomics-3bn3) ou [aqui](https://www.web3dev.com.br/aiengineer13/entendendo-tokenomics-o-valor-real-de-uma-cripto-token-47l7). Também temos um canal exclusivo sobre #tokenomics no Discord da WEB3DEV. Vai lá! Têm várias discussões interessantes!

Okay, então agora se eu for ver meu contrato ERC-20 na Etherscan, eu posso ver todos os meus novos detentores de tokens e quantos `$BIKES` eles tem.

É ISSO AÍ.

![Untitled](https://i.imgur.com/iVx48XT.png)

### 🚨 Relatório de Progresso

*Por favor faça isso ou danicuki vai ficar triste :(.*

Vá em frente e compartilhe uma captura de tela em `#progresso` do contrato do seu token na Etherscan que mostre o nome do token, fornecimento, etc!

**A propósito, se você chegou até aqui e está se divertindo -- talvez você queira tweetar que está construindo sua própria DAO e marcar [@Web3dev_](https://twitter.com/Web3dev_) 😃?**