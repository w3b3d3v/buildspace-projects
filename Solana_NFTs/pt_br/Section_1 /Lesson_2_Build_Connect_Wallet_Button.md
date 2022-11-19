### 🛍 Renderizando o botão de conexão à carteira

Tudo bem. Já estamos verificando se um usuário já está conectado ao nosso aplicativo ou não. E se ele não estiver conectado? Não temos como, em nosso aplicativo, solicitar que a Phantom Wallet se conecte ao nosso aplicativo!

Precisamos criar um botão `connectWallet`. No mundo da web3, conectar sua carteira é literalmente como se tivéssemos um botão "Cadastre-se/Faça o Login" embutido em outro botão para uma melhor experiência do usuário.

Você está pronto para a experiência de cadastro mais fácil da sua vida :)? Confira o código abaixo. Deixei comentários nas linhas que modifiquei.


```jsx
import React from "react";
import { useWallet } from "@solana/wallet-adapter-react";
import dynamic from 'next/dynamic';

// Constantes
const TWITTER_HANDLE = "web3dev_";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  const checkIfWalletIsConnected = async () => {
    try {
      const { solana } = window;

      if (solana) {
        if (solana.isPhantom) {
          console.log('Phantom wallet encontrada!');
          const response = await solana.connect({ onlyIfTrusted: true });
          console.log(
            'Conectado com chave pública:',
            response.publicKey.toString()
          );
        }
      } else {
        alert('Objeto Solana não encontrado. Consiga uma Phantom Wallet 👻');
      }
    } catch (error) {
      console.error(error);
    }
  };

  /* Vamos definir esse método para que nosso código não quebre. 
  Vamos escrever a lógica para isso a seguir! */

  const connectWallet = async () => {};

  /* Queremos renderizar essa interface do usuário quando o usuário 
  ainda não tiver conectado sua carteira ao nosso aplicativo. */

  const renderNotConnectedContainer = () => (
    <button
      className="cta-button connect-wallet-button"
      onClick={connectWallet}
    >
      Conectar à Carteira
    </button>
  );

  useEffect(() => {
    const onLoad = async () => {
      await checkIfWalletIsConnected();
    };
    window.addEventListener('load', onLoad);
    return () => window.removeEventListener('load', onLoad);
  }, []);

  return (
    <div className="App">
      <div className="container">
        <div className="header-container">
          <p className="header">🍭 Candy Drop</p>
          <p className="sub-text">Máquina de NFTs com cunhagem justa</p>
          {/* Renderize seu botão para conectar à carteira aqui */}
          {renderNotConnectedContainer()}
        </div>
    );

    return (
        <div className="App">
            <div className="container">
                <div className="header-container">
                    <p className="header">🍭 Candy Drop</p>
                    <p className="sub-text">Máquina de NFTs com cunhagem justa</p>
                    {/* Renderize seu botão conectar à carteira aqui */}
                    {wallet.publicKey ? "Hello World" : renderNotConnectedContainer()}
                </div>

                <div className="footer-container">
                    <img alt="Twitter Logo" className="twitter-logo" src="twitter-logo.svg" />
                    <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`Construído na @${TWITTER_HANDLE}`}</a>
                </div>
            </div>
        </div>
      </div>
    </div>
  );
};

export default App;
```


Excelente! Agora você deve ter renderizado em sua página um botão com gradiente e uma aparência bem legal, que diz "Conectar à carteira".

![https://camo.githubusercontent.com/cbefae447d15aa767b115a5bd652b11a9b078c9cd5f0b04d625757997c270857/68747470733a2f2f692e696d6775722e636f6d2f3559326c5559502e706e67](https://camo.githubusercontent.com/cbefae447d15aa767b115a5bd652b11a9b078c9cd5f0b04d625757997c270857/68747470733a2f2f692e696d6775722e636f6d2f3559326c5559502e706e67)

**Queremos que nosso botão "Conectar à carteira" seja renderizado apenas quando nosso usuário não tiver realmente conectado sua carteira ao aplicativo.**

Então, por que não armazenamos esses dados da carteira em um estado do React? **Assim** poderíamos também usar isso como um sinal para determinar se devemos mostrar ou ocultar nosso botão.

Primeiro você precisará importar `useState` para seu componente dessa forma:


```jsx
import React, { useEffect, useState } from 'react';
```

Acho que isso é bem autoexplicativo. `useWallet` é um gancho personalizado fornecido por `@solana/wallet-adapter-react`. Acabamos de conectar nossa Phantom Wallet e agora recebemos os dados da carteira do usuário. Agora que temos isso, podemos usar um operador ternário para fazer renderização condicional. Você pode aprender mais sobre o operador ternário [aqui](https://developer.mozilla.org/pt-BR/docs/Web/JavaScript/Reference/Operators/Conditional_Operator).

```jsx
// Estado
const [walletAddress, setWalletAddress] = useState(null);
```


![Untitled](https://i.imgur.com/4kBSvuk.png)


```jsx
import React, { useEffect, useState } from 'react';
import './App.css';
import twitterLogo from './assets/twitter-logo.svg';

// Constantes
const TWITTER_HANDLE = 'web3dev_';
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

Em seguida, vamos configurar as funções que precisamos para chamar nosso programa Solana + obter alguns dados. Nosso app para a web por enquanto está meio chato/vazio! Vamos mudar isso. 😊


### 🚨 Relatório de progresso

Por favor, faça isso, senão o vitordev vai ficar triste 😔.

Publique em `#progresso` uma captura de tela do seu App com o texto Hello World, isso mostra que sua carteira está conectada ao site.🔥
