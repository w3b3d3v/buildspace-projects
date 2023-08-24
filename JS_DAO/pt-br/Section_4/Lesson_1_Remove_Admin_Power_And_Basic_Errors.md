### 😡 Revogue as funções.

Se você se lembra, você na verdade ainda tem direitos de cunhagem no contrato ERC-20. Isso significa que você pode ir e criar mais tokens se você quiser, o que pode deixar os membros da sua DAO malucos rs. Você pode ir e cunhar tipo um bilhão de tokens para você lol.

É melhor você revogar sua função de cunhagem completamente.

Dessa maneira, apenas o contrato de votação é capaz de cunhar novos tokens. Nós podemos fazer isso adicionando o seguinte em `scripts/11-revoke-roles.js`:

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    const token = sdk.getToken("INSIRA_O_ENDEREÇO_DO_TOKEN_MODULE", "token");
    // Mostre os papeis atuais.
    const allRoles = (await token).roles;

    console.log(
      "👀 Papeis que existem agora:",
      await allRoles.getAll(allRoles.roles)
    );

    // Remova todos os superpoderes que sua carteira tinha sobre o contrato ERC-20.
    await allRoles.setAll({ admin: [], minter: [] });
    console.log(
      "🎉 Papeis depois de remover nós mesmos",
      await allRoles.getAll(allRoles.roles)
    );
    console.log("✅ Revogados nossos super-poderes sobre os tokens ERC-20");

  } catch (error) {
    console.error("Falha ao remover nossos direitos sobre o tesouro da DAO", error);
  }
})();
```

Quando eu rodo isso usando `node scripts/11-revoke-roles.js` eu recebo:

```plaintext
$ node scripts/11-revoke-roles.js 
👋 SDK inicializado pelo endereço: 0xf9aD3D930AB5df972558636A2B8749e772aC9297
👀 Papeis que existem agora: {
  admin: [ '0xf9aD3D930AB5df972558636A2B8749e772aC9297' ],
  minter: [
    '0xf9aD3D930AB5df972558636A2B8749e772aC9297',
    '0xB6f4Dcb245638F5C1b694FA8f28E4C37400A437b'
  ],
  transfer: [
    '0xf9aD3D930AB5df972558636A2B8749e772aC9297',
    '0x0000000000000000000000000000000000000000'
  ]
}
🎉 Papeis depois de remover nós mesmos {
  admin: [],
  minter: [],
  transfer: [
    '0xf9aD3D930AB5df972558636A2B8749e772aC9297',
    '0x0000000000000000000000000000000000000000'
  ]
}
✅ Revogados nossos super-poderes sobre os tokens ERC-20
```

No começo você pode ver que meu endereço `0xf9aD3D9` tinha vários privilégios sobre o ERC-20. Então, depois de rodar `token.roles.setAll({ admin: [], minter: [] })` você vai ver que a única pessoa que tem a função de cunhagem é o contrato de votação!

Agora nós estamos livre de um possível "roubo" vindo de admins 😃.

Você verá que ainda tenho a função `transfer` em conjunto com `AddressZero`, na matriz de papeis significa que todos podem transferir tokens (que é o que queremos). Não importa que nosso endereço também esteja lá.

### Execute as propostas depois da votação.

A jornada épica que trilhamos juntos está se aproximando do desfecho. Neste estágio crucial, nos deparamos com um momento de grande importância: a execução das propostas que já conquistaram seus votos.

Mas antes de dar vida às decisões tomadas, temos uma tarefa essencial: identificar aquelas propostas que já completaram seu ciclo de votação e então avançaremos com a execução.

Vá para `scripts/12-executing-proposals.js` e adicione:

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // inicializar o contrato de governança
    const vote = await sdk.getContract("0x3F631d3De33BAeAF8C33E6398f903F2041Dfe25b", "vote");

    // Ver todas as propostas
    const allProposals = await vote.getAll()
    console.log(`Total de propostas: `, allProposals);

} catch (error) {
    console.error("Falha ao listar propostas:", error);
    process.exit(1);
  }
})();
```

Aqui está a saída quando eu executo `node scripts/12-executing-proposals.js`

![Untitled](https://i.imgur.com/FJGYAlS.png)

Repare que nossa proposta está como o `state=3` isso aconteceu porquê durante meu testes eu votei só com uma carteira e as outras se absteram então não conseguiremos excecutar essa proposta. Esse contrato de governança se baseia nos contratos do Compound e aqui estão seus valores:

0.  Pending: Aguardando aprovação ou início.
1.  Active: Em andamento ou em processo de votação.
2.  Canceled: Cancelado e não será mais considerado.
3.  Defeated: A proposta foi rejeitada pela maioria dos votantes.
4.  Succeeded: A proposta foi aprovada pela maioria dos votantes
5.  Queued: Na fila para ser processado ou executado.
6.  Expired: O prazo para a ação ou votação terminou.
7.  Executed: A ação ou proposta foi realizada com sucesso.

Eu criei uma nova proposta e votei com outras carteiras para que pudesse ser uma proposta vencedora e aqui está o resultado após eu ter executado essa proposta:
![Untitled](https://i.imgur.com/tynVVik.png)

Note que ela está com o `state=7` de executed e você deve estar se perguntando como eu fiz isso, foi bem simples basta atualizar seu scripto com o seguinte código e então executar `node scripts/12-executing-proposals.js`

```jsx
import sdk from "./1-initialize-sdk.js";

(async () => {
  try {
    // inicializar o contrato de governança
    const vote = await sdk.getContract("0x3F631d3De33BAeAF8C33E6398f903F2041Dfe25b", "vote");

    // Ver todas as propostas
    const allProposals = await vote.getAll()
    console.log(`Total de propostas: `, allProposals);

    // Primeiro você deve pegar o id da proposta para depois executa-la
    // Nese caso estou selecionando a proposta [2] que refere-se à terceira proposta que eu criei
    const proposalId = allProposals[2].proposalId
    console.log("Proposal Executed: ", await vote.execute(proposalId))

} catch (error) {
    console.error("Falha ao listar e executar propostas:", error);
    process.exit(1);
  }
})();
```

### 👍 Lide com erro de network não suportada.

Primeiramente, vamos importar um último hook `useNetwork` no topo de `App.jsx` para poder reconhecer uma conexão de fora da rede Mumbai. Também importamos `ChainId` do thirdweb SDK:

```jsx
import {
  useAddress,
  useNetwork,
  useContract,
  ConnectWallet,
  Web3Button,
  useNFTBalance,
} from '@thirdweb-dev/react';
import { ChainId } from '@thirdweb-dev/sdk';
```

Então definimos nosso hook `useNetwork` abaixo do `useAddress`:

```jsx
const network = useNetwork();
``` 

Depois, adicione o trecho a seguir no seu arquivo `App.jsx` logo abaixo da função `mintNft`.

```jsx
if (address && (network?.[0].data.chain.id !== ChainId.Mumbai)) {
  return (
    <div className="unsupported-network">
      <h2>Por favor, conecte-se à rede Mumbai</h2>
      <p>
        Essa dapp só funciona com a rede Mumbai, por favor 
        troque de rede na sua carteira.
      </p>
    </div>
  );
}
```

Bem simples! Mas bem útil. Uma mensagem vai ser mostrada se o usuário não estiver na rede Mumbai!

### 🤑 Veja o seu token na Uniswap.

Você pode estar se perguntando como tokens como [ENS DAO](https://coinmarketcap.com/currencies/ethereum-name-service/) ou o mais recente [Constitution DAO](https://coinmarketcap.com/currencies/constitutiondao/) tem tokens de governança que valem dinheiro de verdade. Bem, Basicamente, é porque outras pessoas podem de fato comprar seus tokens de governança diretamente em exchanges decentralizadas como Uniswap.

Por exemplo — talvez uma pessoa aleatória acorde e diga, “Ei, eu te dou $100 por 100 $BIKES por que eu quero me juntar à MTBDAO e ter algum poder de governança”. Bem, isso significa que $BIKES tem valor real agora. Isso significa que 1 $BIKES = 1 Us Dollar. E uma vez que existem 1.000.000 $BIKES, isso significa que o valor de mercado totalmente diluído do meu token valeria $1.000.000.

Bem louco, certo 😃?

Pessoas geralmente fazem trocas como essas na Uniswap.

Acredite ou não, seu token agora vai aparecer na Uniswap dentro da Mumbai.

Aqui está um vídeo rápido para você fazer você mesmo: 

[Loom](https://www.loom.com/share/994d0e73f7f14af8a732d40e3d84c45e)

Você pode ler mais sobre liquidity pools [aqui](https://www.web3dev.com.br/fatimalima/a-uniswap-v3-explicada-2b8g). Você vai notar no vídeo que não existia uma para $BIKES. Mas, tecnicamente qualquer pessoa poderia vir e criar uma pool que permite pessoas trocarem $ETH por $BIKES. Essa pool poderia ter $100. Ou, poderia ter $1.000.000.000. Depende de quão popular meu token é!

### 🎨 Personalize um pouco seu web app!

Tire algum tempo para personalizar seu web app um pouco. Mude algumas cores. Mude os textos. Adicione alguns emojis legais. Vá para `public/index.html` e mude coisas como o título e a descrição!

Ideia aleatória: quando as pessoas estão votando, toque o hino do país ou algo do tipo lolol.

Tire algum tempo aqui antes de seguir em frente para fazer essas páginas suas. Mesmo que tudo que você faça seja mudar a cor do background está tudo bem. Se divirta com isso.

### 🚨 Relatório de Progresso

*Por favor faça isso ou Yan vai ficar triste :(.*

Vá em frente e poste uma captura de tela em `#progresso` do seu DAO dashboard depois de algumas customizações!
