Vamos para `App.jsx`. O que nós vamos fazer agora é

1) Se detectarmos que o usuário tem um NFT de filiação, mostramos para ele nossa tela de "DAO dashboard" onde ele pode votar nas propostas e ver informações relacionadas a DAO.

2) Se detectarmos que o usuário não tem nosso NFT, vamos mostrar o botão para ele cunhar um.

Vamos fazer isso! Vamos atacar o caso #1 primeiro, precisamos detectar se o usuário tem nosso NFT.

### 🤔 Checando se o usuário tem um NFT de filiação.

Vá para `App.jsx` e atualize seus imports:

```jsx
import { useAddress, ConnectWallet, useContract, useNFTBalance } from '@thirdweb-dev/react';
import { useState, useEffect, useMemo } from 'react';
```

A partir daí, abaixo do `console.log("👋 Address:", address);`, vamos adicionar:

```jsx
  // inicializar o contrato editionDrop
  const editionDropAddress = "INSIRA_O_ENDEREÇO_DO_BUNDLE_DROP"
  const { contract: editionDrop } = useContract(editionDropAddress, "edition-drop");

  // Hook para verificar se o úsuario tem a NFT
  const { data: nftBalance } = useNFTBalance(editionDrop, address, "0")

    const hasClaimedNFT = useMemo(() => {
    return nftBalance && nftBalance.gt(0)
    }, [nftBalance])
    
  // ... inclua todo o seu outro código que já estava abaixo.
```

Primeiro nós inicializamos o contrato editionDrop.

A partir daí, nós usamos `bundleDropModule.balanceOf(address, "0")` para checar se o usuário tem o nosso NFT. Isso vai na verdade requisitar os dados ao nosso contrato que está na blockchain. Por que nós usamos `0`? Bem, se você se lembra o `0` é o tokenId do nosso NFT de filiação. Então aqui estamos perguntando ao nosso contrato, "Ei, esse usuário é dono de um token com o id 0?".

Perfeito! Nós recebemos "esse usuário NÃO tem o NFT de membro". Vamos criar um botão que permite o usuário cunhar um NFT.

### ✨ Construa um botão "Mint NFT".

Vamos fazer isso! Volte para `App.jsx`. Eu coloquei alguns comentários nas linhas que eu adicionei:

```javascript
import { useAddress, ConnectWallet, Web3Button, useContract, useNFTBalance } from '@thirdweb-dev/react';
import { useState, useEffect, useMemo } from 'react';

const App = () => {
  // Usando os hooks que o thirdweb nos dá.
  const address = useAddress();
  console.log("👋 Address:", address);
  // inicializar o contrato editionDrop
  const editionDropAddress = "INSIRA_O_ENDEREÇO_DO_BUNDLE_DROP"
  const { contract: editionDrop } = useContract(editionDropAddress, "edition-drop");
  // Hook para sabermos se o usuário tem nosso NFT.
  const { data: nftBalance } = useNFTBalance(editionDrop, address, "0")

  const hasClaimedNFT = useMemo(() => {
    return nftBalance && nftBalance.gt(0)
  }, [nftBalance])

    // Se ele não tiver uma carteira conectada vamos chamar Connect Wallet
    if (!address) {
    return (
      <div className="landing">
        <h1>Welcome to NarutoDAO</h1>
        <div className="btn-hero">
          <ConnectWallet />
        </div>
      </div>
    );
  }

  // Renderiza a tela de cunhagem do NFT.
  return (
    <div className="mint-nft">
      <h1>Cunhe seu NFT 🍪 ele mostra que você é membro desta DAO</h1>
      <div className="btn-hero">
        <Web3Button 
          contractAddress={editionDropAddress}
          action={contract => {
            contract.erc1155.claim(0, 1)
          }}
          onSuccess={() => {
            console.log(`🌊 Successfully Minted! Check it out on OpenSea: https://testnets.opensea.io/assets/${editionDrop.getAddress()}/0`);
          }}
          onError={error => {
            console.error("Failed to mint NFT", error);
          }}
        >
          Mint your NFT (FREE)
        </Web3Button>
      </div>
    </div>
  );

}

export default App;

```

Okay, um monte de coisas acontecendo! Nós chamamos `bundleDropModule.claim("0", 1)` para de fato cunhar o NFT na carteira do usuário quando ele clicar no botão. Nesse caso o tokenId do nosso NFT de filiação é `0` então nós passamos 0. Depois, passamos `1` porque só queremos cunhar um NFT de filiação para a carteira do usuário!

Quando tudo está pronto, nós fazemos `setIsClaiming(false)` para parar o estado de carregamento. E depois fazemos `setHasClaimedNFT(true)` para que o nosso app react possa saber que esse usuário reivindicou seu NFT com sucesso.

Quando você de fato vai cunhar o NFT, a Metamask vai mostrar um pop-up para que você pague a taxa de transação. Uma vez que a cunhagem foi feita, você deve ver `Cunhado com sucesso!` no seu console junto com o link para o Opensea Testnet. Em [`testnets.opensea.io`](http://testnets.opensea.io/) nós podemos de fato ver os NFTs cunhados na testnet, o que é bem legal! Quando você for para o seu link, você verá algo tipo assim:

![Untitled](https://i.imgur.com/9sASxQT.png)

Legal! Aqui você consegue ver que meu NFT tem 2 donos. Você também verá que diz "You own 1" o que é o que você verá do seu lado desde que você tenha cunhado seu NFT!

![Untitled](https://i.imgur.com/j0lOE7P.png)

Isso é por que eu pedi para um amigo meu cunhar esse NFT para mim como um teste. Novamente, porque é um ERC-1155 **todo mundo é dono do mesmo NFT**. Isso é bem legal e é também mais eficiente em termos de taxas. Cunhar um ERC721 custa 96,073 gas. Cunhar um ERC1155 custa 51,935 gas. Por que? Porque todo mundo está compartilhando os mesmos dados do NFT. Nós não precisamos copiar novos dados para cada usuários.

### 🛑 Mostre o seu Dashboard apenas se o usuário tiver o NFT.

Okay, se você se lembra nós temos que lidar com dois casos:

1) Se detectarmos que o usuário tem um NFT de filiação, mostramos para ele nossa tela de "DAO dashboard" onde ele pode votar nas propostas e ver informações relacionadas a DAO.

2) Se detectarmos que o usuário não tem nosso NFT, vamos mostrar o botão para ele cunhar um.

Isso é bem fácil. Tudo que precisamos fazer é adicionar o código abaixo em `App.jsx`

```jsx
if (!address) {
 return (
   <div className="landing">
     <h1>Bem-vind@s à MTBDAO a DAO dos pedaleiros de montanha</h1>
     <button onClick={connectWithMetamask} className="btn-hero">
       Conecte sua carteira
     </button>
   </div>
 )
}

// Adicione esse pedacinho!
if (hasClaimedNFT) {
 return (
   <div className="member-page">
     <h1>🚴 Página dos membros da DAO</h1>
     <p>Parabéns por fazer parte desse clube de bikers!</p>
   </div>
 )
};
```

É isto! Agora, quando você atualizar a página você vai ver que você está na página de membros da DAO. Sim!!! Se você desconectar sua carteira do web app, você será redirecionado para a página de "Connect Wallet".

Finalmente, se você conectar sua carteira e **não** tiver seu NFT de filiação, vai ser mostrado o botão para você cunhar um. Eu recomendo que você teste esse caso:

1) **desconecte** sua carteira do web app

2) [crie uma nova conta](https://metamask.zendesk.com/hc/en-us/articles/360015289452-How-to-create-an-additional-account-in-your-MetaMask-wallet) 

O que vai te dar um novo endereço público para que você tenha outro endereço para receber o NFT. A Metamask permite que você tenha quantas contas você quiser.

Certifique-se de testar todos os casos!

1) Carteira desconectada:

![Untitled](https://i.imgur.com/T5M76f8.png)

2) Carteira conectada, mas o usuário não tem o NFT de filiação:

![Untitled](https://i.imgur.com/d7xq0pc.png)

3) Usuário tem o NFT de filiação, então mostre a ela a página que apenas membros da DAO podem ver:

![Untitled](https://i.imgur.com/p7oUlSS.png)

### 🚨 Relatório de Progresso

*Por favor faça isso ou Yan vai ficar triste :(.*

Vá em frente e compartilhe uma screenshot do seu NFT de filiação no OpenSea em `#progresso`.