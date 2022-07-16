### ⛓ Especificando sua rede e tipo de carteira.

Para que nosso site possa se comunicar com a blockchain, nós precisamos de alguma forma conectar nossa carteira com ela. Uma vez que nossa carteira esteja conectada, nosso website terá permissão para chamar smart contracts em nosso favor. **Lembre-se, é como se autenticar em um website.**

Talvez você já tenha criado botões de "Connect Wallet" antes! Desta vez, nós usaremos o SDK front-end do thirdweb que faz isso ser muito mais fácil.

Vá para `index.js` no seu React App e adicione o código abaixo:

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

// Importe o ThirdWeb
import { ThirdwebWeb3Provider } from '@3rdweb/hooks';

// Inclua que redes você quer dar suporte.
// 4 = Rinkeby.
const supportedChainIds = [4];

// Inclua quais carteiras você quer dar suporte.
// Nesse caso, nós suportamos a MetaMask, que é uma "injected wallet".
const connectors = {
  injected: {},
};

// Por último, envolva o App com o ThirdwebWeb3Provider.
ReactDOM.render(
  <React.StrictMode>
    <ThirdwebWeb3Provider
      connectors={connectors}
      supportedChainIds={supportedChainIds}
    >
      <App />
    </ThirdwebWeb3Provider>
  </React.StrictMode>,
  document.getElementById('root')
);
```

Bem simples. Nós estamos importando o thirdweb e especificando o `chainId` da rede que estamos trabalhando, que nesse caso é a Rinkeby! Sinta-se a vontade para checar todos os ids [aqui](https://besu.hyperledger.org/en/stable/Concepts/NetworkID-And-ChainID/). Então, dentro de `connectors`, nós estamos especificando que tipo de carteira damos suporte. Existem vários tipos de carteiras. Carteiras mobile, carteiras injetadas, carteiras em hardware, etc. O thirdweb faz com que seja fácil dar suporte a todas elas em algumas linhas. Nesse caso, estamos dando suporte apenas para carteiras `injected` o que nos permite usar carteiras baseadas em extensões de navegador, como a MetaMask.

Finalmente, estamos envolvendo tudo com `<ThirdwebWeb3Provider>`, esse provedor mantém os dados da carteira do usuário conectado (se ele tiver se conectado ao site antes) e passa para `App`.

*Nota: Se você trabalhou com dapps antes, certifique-se de desconectar sua carteira de [https://localhost:3000](https://localhost:3000) se estiver conectado.*

### 🌟 Adicionando conexão com a carteira.

Se você for para o seu web app, você verá uma página roxa em branco. Vamos adicionar um texto básico + um botão que permite usuários conectarem suas carteiras.

Vá para `App.jsx`. Adicione o código abaixo.

```jsx
import { useEffect, useMemo, useState } from "react";

// importe o thirdweb
import { useWeb3 } from "@3rdweb/hooks";

const App = () => {
  // Use o hook connectWallet que o thirdweb nos dá.
  const { connectWallet, address, error, provider } = useWeb3();
  console.log("👋 Address:", address)

  // Esse é o caso em que o usuário ainda não conectou sua carteira
  // ao nosso web app. Deixe ele chamar connectWallet.
  if (!address) {
    return (
      <div className="landing">
        <h1>Welcome to NarutoDAO</h1>
        <button onClick={() => connectWallet("injected")} className="btn-hero">
          Connect your wallet
        </button>
      </div>
    );
  }
  
  // Esse é o caso em que temos o endereço do usuário
  // o que significa que ele conectou sua carteira ao nosso site!
  return (
    <div className="landing">
      <h1>👀 wallet connected, now what!</h1>
    </div>);
};

export default App;
```

Bem fácil! A propósito — nesse ponto certifique-se de que seu web app está rodando usando `npm start` se você estiver trabalhando localmente.

Agora, quando você for para o web app e clicar em "Connect your wallet" você vai ver um pop-up da MetaMask! Depois de autorizar a sua carteira, você vai ver essa tela:

![Untitled](https://i.imgur.com/oDG9uiz.png)

Boom. Agora se você for para o console, vai ver que ele exibe seu endereço público. Se você atualizar a página aqui, verá que a conexão com a carteira se mantém.

Se você construiu uma conexão com uma carteira no passado, vai perceber como isso foi muito mais fácil com o SDK do thirdweb, pois ele lida com os casos extremos para você (por exemplo, manter o estado da carteira do usuário em uma variável).

A propósito — aqui eu faço `<h1>Welcome to NarutoDAO</h1>`, por favor faça isso ser seu. Não me copie! Essa é a sua DAO!

*Nota: sinta-se à vontade para [desconectar seu website](https://metamask.zendesk.com/hc/en-us/articles/360059535551-Disconnect-wallet-from-Dapp) da MetaMask se você quiser testar o caso em que o usuário ainda não conectou sua carteira.*

### 🚨 Relatório de Progresso

*Por favor, faça isso ou danicuki vai ficar triste :(*

Poste uma captura de tela em `#progresso` mostrando a página de boas vindas da sua DAO com o botão de conectar na carteira. É melhor que não esteja escrito NarutoDAO!