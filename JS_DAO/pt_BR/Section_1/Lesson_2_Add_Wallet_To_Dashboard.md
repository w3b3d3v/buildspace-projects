### ⛓ Especificando sua rede e tipo de carteira.

[Youtube](https://www.youtube.com/watch?v=LLfG5qnaJZU)

Para que nosso site possa se comunicar com a blockchain, nós precisamos de alguma forma conectar nossa carteira com ele. Uma vez que nossa carteira esteja conectada, nosso website terá permissão para chamar smart contracts em nosso favor. **Lembre-se, é como se autenticar em um website.**

Talvez você já tenha criado botões de "Connectar Wallet" antes! Desta vez, nós usaremos o SDK front-end do thirdweb que faz isso ser muito mais fácil.

Vá para `index.js` no seu React App e adicione o código abaixo:

```jsx
import React from 'react';
import { createRoot } from 'react-dom/client';
import './index.css';
import App from './App';

// Importe o ThirdWeb
import { ThirdwebProvider } from '@thirdweb-dev/react';
import { ChainId } from '@thirdweb-dev/sdk';

// Inclua que redes você quer dar suporte.
// 5 = Mumbai.
const activeChainId = ChainId.Mumbai;

// Por último, envolva o App com o thirdweb provider.
const container = document.getElementById('root');
const root = createRoot(container);
root.render(
  <React.StrictMode>
    <ThirdwebProvider activeChain={activeChainId}>
      <App />
    </ThirdwebProvider>
  </React.StrictMode>,
);
```

Bem simples. Nós estamos importando o thirdweb e especificando o `chainId` da rede que estamos trabalhando, que nesse caso é a Mumbai! Sinta-se a vontade para checar todos os ids [aqui](https://besu.hyperledger.org/en/stable/Concepts/NetworkID-And-ChainID/). 

Finalmente, estamos envolvendo tudo com `<ThirdwebProvider>`, esse provedor mantém os dados da carteira do usuário conectado (se ele tiver se conectado ao site antes) e passa para `App`.

*Nota: Se você trabalhou com dapps antes, certifique-se de desconectar sua carteira de [https://localhost:3000](https://localhost:3000) se estiver conectado.*

### 🌟 Adicionando conexão com a carteira.

Se você for para o seu webapp, você verá uma página roxa em branco. Vamos adicionar um texto básico + um botão que permite usuários conectarem suas carteiras.

Vá para `App.jsx`. Adicione o código abaixo.

```jsx
import { useAddress, ConnectWallet } from '@thirdweb-dev/react';

const App = () => {
  // Use o hook connectWallet que o thirdweb nos dá.
  const address = useAddress();
  console.log("👋 Address:", address);

  // Esse é o caso em que o usuário ainda não conectou sua carteira
  // ao nosso webapp. Deixe ele chamar connectWallet.
  if (!address) {
    return (
      <div className="landing">
        <h1>Bem-vind@s à MTBDAO - a DAO dos pedaleiros de montanha</h1>
        <div className="btn-hero">
          <ConnectWallet />
        </div>
      </div>
    );
  }
  
  // Esse é o caso em que temos o endereço do usuário
  // o que significa que ele conectou sua carteira ao nosso site!
  return (
    <div className="landing">
      <h1>👀 carteira conectada, e agora?!</h1>
    </div>);
};

export default App;
```

Bem fácil! A propósito -- nesse ponto certifique-se de que seu web app está rodando usando `npm start` se você estiver trabalhando localmente.

Agora, quando você for para o web app e clicar em "Connect your wallet" você vai ver um pop-up da Metamask! Depois de autorizar a sua carteira, você vai ver essa tela:

![Untitled](https://i.imgur.com/qyxndEk.png)

Boom. Agora se você for para o console, vai ver que ele exibe seu endereço público. Se você atualizar a página aqui, verá que a conexão com a carteira se mantém.

Se você construiu uma conexão com uma carteira no passado, vai perceber como isso foi muito mais fácil com o SDK do thirdweb, por que ele lida com os casos extremos para você (ex. manter o estado da carteria do usuário em uma variável).

A propósito - Aqui eu faço `<h1>Bem-vind@s à MTBDAO - a DAO dos pedaleiros de montanha</h1>`, por favor faça isso ser seu. Não me copie! Essa é a sua DAO!

*Nota: sinta-se a vontade para [desconectar seu website](https://metamask.zendesk.com/hc/en-us/articles/360059535551-Disconnect-wallet-from-Dapp) da Metamask se você quiser testar o caso em que o usuário ainda não conectou sua carteira.*

### 🚨 Relatório de Progresso

*Por favor faça isso ou Yan vai ficar triste :(*

Poste uma captura de tela em `#progresso` mostrando a página de boas vindas da sua DAO com o botão de conectar na carteira. É melhor que não esteja escito MTBDAO!