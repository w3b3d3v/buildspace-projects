### 📜 Crie as primeiras duas propostas da sua DAO.

Legal. Tudo está configurado agora, nós precisamos só criar nossa primeira proposta! Vá para `10-create-vote-proposals.js` e adicione o seguinte:

```jsx
import { ethers } from "ethers";
import sdk from "./1-initialize-sdk.js";

// Nosso contrato de votação.
const voteModule = sdk.getVote(
  "INSIRA_O_ENDEREÇO_DO_VOTE_MODULE",
);

// Nosso contrato ERC-20.
const tokenModule = sdk.getToken(
  "INSIRA_O_ENDEREÇO_DO_TOKEN_MODULE",
);

(async () => {
  try {
    const amount = 420_000;
    // Crie uma proposta para cunhar 420.000 novos tokens para a tesouraria.
    await voteModule.propose(
      "A DAO deveria mintar " + amount + " tokens a mais na tesouraria?",
      [
        {
          // Nosso nativeToken é ETH. nativeTokenValue é a quantidade de ETH que nós queremos 
          // mandar nessa proposta. Nesse caso, estamos mandando 0 ETH.
          // Nós estamos apenas cunhando novos tokens para a tesouraria. Então, deixe 0.
          nativeTokenValue: 0,
          transactionData: tokenModule.encoder.encode(
            // Estamos fazendo uma cunhagem! E, estamos cunhando no voteModule, que está
            // agindo como nossa tesouraria.
            "mintTo",
            [
              voteModule.getAddress(),
              ethers.utils.parseUnits(amount.toString(), 18),
            ]
          ),
          // Nosso token module que de fato executa a cunhagem.
          toAddress: tokenModule.getAddress(),
        },
      ]
    );

    console.log("✅ Proposta criada com sucesso para mintar tokens");
  } catch (error) {
    console.error("A criação da proposta falhou", error);
    process.exit(1);
  }

  try {
    const amount = 6_900;
    // Crie uma proposta para transferir para nós mesmos 6,900 tokens por sermos irados.
    await voteModule.propose(
      " A DAO deveria transferir  " +
      amount + " tokens da tesouraria para " +
      process.env.WALLET_ADDRESS + " por ser uma pessoa incrível?",
      [
        {
          // Novamente, estamos mandando para nós mesmos 0 ETH. Apenas mandando nosso próprio token.
          nativeTokenValue: 0,
          transactionData: tokenModule.encoder.encode(
            // Nós estamos fazendo uma transferência da tesouraria para a nossa carteira.
            "transfer",
            [
              process.env.WALLET_ADDRESS,
              ethers.utils.parseUnits(amount.toString(), 18),
            ]
          ),

          toAddress: tokenModule.getAddress(),
        },
      ]
    );

    console.log(
      "✅ Proposta criada com sucesso para a tesouraria nos recompensar, vamos torcer para votem a favor!"
    );
  } catch (error) {
    console.error("A criação da proposta falhou", error);
  }
})();

```

Parece muita coisa. Vá em frente e leia passo a passo! Nós estamos criando duas novas propostas para membros votarem:

**1) Nós estamos criando uma proposta que permite o tesouro cunhar 420.000 novos tokens.** Você pode ver que fazemos um `mint` no código.

Talvez a tesouraria está na baixa e queremos mais tokens para premiar membros. Lembre-se, mais cedo nós demos para o nosso contrato de votação a habilidade de cunhar novos tokens — então isso funciona! É uma tesouraria democrática. Se os membros acharem que essa proposta é estúpida e votarem “NÃO”, ela simplesmente não vai passar!

**2) Nós estamos criando uma proposta para transferir 6.900 tokens para a nossa carteira a partir da tesouraria.** Você pode ver que nós fazemos uma `"transfer"` no código.

Talvez tenhamos feito algo legal e queremos ser recompensados por isso! No mundo real você criaria propostas para mandar tokens pra outras pessoas. Por exemplo, talvez alguém ajudou a codar um novo website para a DAO e quer ser recompensado por isso. Nós podemos mandar tokens para ele!

A propósito, eu quero fazer um comentário sobre `nativeTokenValue`. Digamos que queremos que nossa proposta faça algo como, "Nós queremos recompensar NarutoFangir127 por nos ajudar com marketing com 2500 tokens de governança e 0.1 ETH". Isso é bem legal! Quer dizer que você pode recompensar pessoas com ETH e tokens de governança — o melhor dos dois mundos. *Nota: Esses 0.1 ETH tem que estar na sua tesouraria se quisermos enviá-lo!*

Quando eu rodo `node scripts/10-create-vote-proposals.js` eu recebo:

```plaintext
web3dev-dao-starter % node scripts/10-create-vote-proposals.js
👋 Your app address is: 0xa002D595189bF9D50D5897C64b6e07BE5bdEe9b8
✅ Successfully created proposal to mint tokens
✅ Successfully created proposal to reward ourselves from the treasury, let's hope people vote for it!

```

BOOM. Aí estão nossas propostas. A última coisa que vamos fazer é de fato permitir que nosso usuários votem nas propostas no nosso DAO dashboard!

### ✍️ Permita que usuários votem nas propostas no dashboard.

Finalmente, vamos juntar tudo agora. Neste momento, nossas propostas vivem apenas no smart-contract. Mas nós queremos que nossos usuários vejam e votem nelas! Vamos fazer isto! Vá para `App.jsx`. Adicione o hook `useVote` às importações:

```jsx
import { useAddress, useMetamask, useEditionDrop, useToken, useVote } from '@thirdweb-dev/react';
```
Siga em frente e coloque isto aqui abaixo de `token`.

```jsx
  const vote = useVote("INSIRA_O_CONTRATO_DE_VOTAÇÃO_AQUI");
```

Nosso web app precisa acessar nosso `vote` para que usuários possam interagir com nosso contrato.

A partir daqui, vamos adicionar o código abaixo em algum lugar em baixo das nossas outras variáveis de estado:

```jsx
const [proposals, setProposals] = useState([]);
const [isVoting, setIsVoting] = useState(false);
const [hasVoted, setHasVoted] = useState(false);

// Recupere todas as propostas existentes no contrato. 
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  // A simple call to vote.getAll() to grab the proposals.
  const getAllProposals = async () => {
    try {
      const proposals = await vote.getAll();
      setProposals(proposals);
      console.log("🌈 Propostas:", proposals);
    } catch (error) {
      console.log("Falhou em recuperar as propostas", error);
    }
  };
  getAllProposals();
}, [hasClaimedNFT, vote]);

// Nós também precisamos checar se o usuário já votou.
useEffect(() => {
  if (!hasClaimedNFT) {
    return;
  }

  // Se nós não tivermos terminado de recuperar as propostas do useEffect acima
  // então ainda nao podemos checar se o usuário votou!
  if (!proposals.length) {
    return;
  }

  // Cheque se o usuário já votou na primeira proposta.
  const checkIfUserHasVoted = async () => {
    try {
      const hasVoted = await vote.hasVoted(proposals[0].proposalId, address);
      setHasVoted(hasVoted);
      if (hasVoted) {
        console.log("🥵 Usuário já votou");
      } else {
        console.log("🙂 Usuário ainda não votou");
      }
    } catch (error) {
      console.error("Não foi possível verificar se o usuário já votou", error);
    }
  };
  checkIfUserHasVoted();

}, [hasClaimedNFT, proposals, address, vote]);
```

Estamos fazendo duas coisas aqui!

No primeiro `useEffect` estamos fazendo `vote.getAll()` para pegar todas as propostas que existem no nosso contrato de governança e então fazemos `setProposals` para que possamos renderizá-las depois.

No segundo useEffect, nós estamos fazendo `vote.hasVoted(proposals[0].proposalId, address)` que checa se esse endereço já votou na primeira proposta. Se sim, então nós fazemos `setHasVoted` para que o usuário não possa votar novamente! Mesmo se nós não tivéssemos isso, nosso contrato rejeitaria a transação se um usuário tentasse votar duas vezes!

A magia do thirdweb é que não somente é fácil fazer deploy de smart contracts, também é fácil interagir com eles diretamente do nosso cliente com funções simples como `vote.getAll()`!

Vá em frente e atualize sua página, você deve ver suas propostas impressas perto do 🌈 e você pode explorar todos os dados!

![Untitled](https://i.imgur.com/tNhXvHY.png)

E se você já votou, você vai ver algo assim:

![Untitled](https://i.imgur.com/zOQ6Rim.png)

O próximo pedaço de código é massivo lol. Ele lida com de fato renderizar as propostas que nós recuperamos para que os usuários possam ter três opções de voto:

1) For

2) Against

3) Abstains

Se você tem familiaridade com React/JS, você pode facilmente dar uma olhada e entender como funciona sozinho. Se você não sabe React/JS muito bem, não se preocupe. Só copie e cole. Sem vergonha mesmo!

Vá em frente e substitua o conteúdo de `if (hasClaimedNFT) { }` com este código [REVIEW](https://github.com/buildspace/buildspace-dao-final/blob/d94cadc73703c09561fda946a338237eee7f9bee/src/App.jsx#L194).

Não se esqueça de verificar que usamos algumas variáveis novas, você precisa declará-las no começo do arquivo. 

Quando você checar seu web app, você verá algo como:

![Untitled](https://i.imgur.com/Q5bzFWb.png)

Muito legal. Agora você pode usar esses botões para votar.

Nós configuramos nosso contrato de governança para terminar a votação depois de 24 horas. Isso significa que depois de 24 horas se:
```plaintext
votos "para" a proposta > votos "contra" a proposta
```

Então qualquer membro deve ser apto a executar a proposta através do nosso contrato de governança. Propostas não podem ser executadas automaticamente. Mas, uma vez que uma proposta passa, **qualquer membro** da DAO pode acionar a proposta aceita.

Por exemplo. Digamos que nós estamos lidando com a proposta em que estamos cunhando 420.000 tokens adicionais. se `votos "para" a propsta > votos "contra" a proposta` — então qualquer pessoa pode acionar a proposta e nosso contrato irá cunhar os tokens. Bem louco, certo? Nós não precisamos confiar em ninguém além da blockchain.

Imagine estar num país corrupto, votando por algo, e então o seu governo mente para você e diz “Ei na verdade nós não conseguimos tokens suficientes rs” quando na verdade conseguiram sim. Ou imagine que eles falamm, “Okay, nós temos votos suficientes e nós vamos fazer isso nós prometemos” e nunca fazem!

Nesse caso, tudo está codificado e código não mente.

De qualquer forma, agora não é a hora de discutir como DAOs podem potencialmente melhorar nossos governos ;). Precisamos terminar nossa DAO meme, aqui e agora! Estamos tão perto.

### 🚨 Relatório de Progresso

*Por favor faça isso ou Yan vai ficar triste :(.*

Vá em frente e compartilhe uma captura de tela do seu DAO dashboard mostrando sua lista de membros + sistema de votação em `#progresso`!
