### 🤖 Vamos pegar o código inicial

Comece indo para este [link do GitHub](https://github.com/buildspace/nft-drop-starter-project), onde você encontra o código do repositório inicial. A partir daqui, você deve pressionar o botão "Fork" no canto superior direito da página.

![image](https://i.imgur.com/p2FTyAM.png)

Massa! Quando você faz o fork deste repositório, na verdade você está criando uma cópia idêntica dele que fica no seu perfil do Github. Então agora você tem sua própria versão deste código que você pode editar para o conteúdo de sua escolha :). Isso também será útil quando estivermos prontos para implantar nosso aplicativo no Vercel 🤘.

O passo final aqui é transferir o repositório do fork que você acabou de fazer para a sua máquina local. Clique no botão "Code" (Código) e copie esse link!

![image](https://i.imgur.com/4QtA8wO.png)

Finalmente, vá para o seu terminal, dê um `cd` para qualquer diretório em que seu projeto ficará e execute o comando:


```plaintext
git clone SEU_LINK_DO_FORK
```


Aí está :). Hora de codificar!


### 🔌 Criando um botão de conexão de carteira com a Phantom Wallet

Para este projeto, usaremos uma carteira chamada [Phantom](https://phantom.app/). Esta é uma das principais extensões de carteira para Solana.

Antes de mergulharmos em qualquer código - certifique-se de ter baixado a extensão e configurado uma carteira Solana! Atualmente, a Phantom Wallet suporta **Chrome**, **Brave**, **Firefox** e **Edge**. Mas, como nota: só testamos este código no Brave e no Chrome.


### 👻 Usando o objeto Solana

Para que nosso site converse com nosso programa Solana, precisamos de alguma forma conectar nossa carteira (que é a extensão Phantom Wallet) a ele.

Assim que conectarmos nossa carteira ao nosso site, este terá permissão para executar funções do nosso programa em nosso nome. Se nossos usuários não conectarem suas carteiras, eles não poderão se comunicar com a blockchain Solana.

**Lembre-se, é como se autenticar em um site.** Se você não estiver "conectado" ao GMail, não poderá usar o produto de e-mail deles!

Vá até seu código e acesse `App.js` em `src`. É aqui que estará o principal ponto de entrada do nosso aplicativo.

Se você tiver a extensão Phantom Wallet instalada, ela injetará automaticamente um objeto especial chamado `solana` em seu objeto `window` que possui algumas funções mágicas. Isso significa que antes de fazermos qualquer coisa, precisamos verificar se isso existe. Se não existir, vamos dizer ao nosso usuário para fazer o download:


```jsx
import React from "react";
import { WalletMultiButton } from "@solana/wallet-adapter-react-ui";

// Constantes
const TWITTER_HANDLE = "_buildspace";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const Home = () => {
    // Ações
    const renderNotConnectedContainer = () => (
        <div>
            <img src="https://media.giphy.com/media/eSwGh3YK54JKU/giphy.gif" alt="emoji" />

            <div className="button-container">
                <WalletMultiButton className="cta-button connect-wallet-button" />
            </div>
        </div>
    );

    return (
        <div className="App">
            <div className="container">
                <div className="header-container">
                    <p className="header">🍭 Candy Drop</p>
                    <p className="sub-text">Máquina de NFTs com cunhagem justa</p>
                    {/* Renderize seu botão conectar à carteira aqui */}
                    {renderNotConnectedContainer()}
                </div>

                <div className="footer-container">
                    <img alt="Twitter Logo" className="twitter-logo" src="twitter-logo.svg" />
                    <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`built on @${TWITTER_HANDLE}`}</a>
                </div>
            </div>
        </div>
    );
};

export default Home;
```

Excelente! Não é tão difícil, certo? Vamos detalhar isso um pouco mais:

```jsx
const renderNotConnectedContainer = () => (
    <div>
        <img src="https://media.giphy.com/media/eSwGh3YK54JKU/giphy.gif" alt="emoji" />
        <div className="button-container">
            <WalletMultiButton className="cta-button connect-wallet-button" />
        </div>
    </div>
);
```


O `WalletMultiButton` detectará automaticamente qualquer extensão de carteira Solana que você instalou em seu navegador, como `Phantom`, `Sollet`, `Ledger`, `Solflare` etc. Isso depende de suas configurações em `_app.js`. É assim que seu `_app.js` deve ficar.

```javascript
import { useMemo } from "react";
import { clusterApiUrl } from "@solana/web3.js";
import { WalletAdapterNetwork } from "@solana/wallet-adapter-base";
import { WalletModalProvider } from "@solana/wallet-adapter-react-ui";
import { PhantomWalletAdapter } from "@solana/wallet-adapter-wallets";
import { ConnectionProvider, WalletProvider } from "@solana/wallet-adapter-react";

import "../styles/App.css";
import "../styles/index.css";
import "../styles/globals.css";
import "../styles/CandyMachine.css";
import "@solana/wallet-adapter-react-ui/styles.css";

const App = ({ Component, pageProps }) => {
    const network = WalletAdapterNetwork.Devnet;
    const endpoint = useMemo(() => clusterApiUrl(network), [network]);
    const wallets = useMemo(() => [new PhantomWalletAdapter()], [network]);

    return (
        <ConnectionProvider endpoint={endpoint}>
            <WalletProvider wallets={wallets} autoConnect>
                <WalletModalProvider>
                    <Component {...pageProps} />
                </WalletModalProvider>
            </WalletProvider>
        </ConnectionProvider>
    );
};

export default App;
```

### Adicionando suporte para mais adaptadores de carteira (opcional)

Se você deseja adicionar suporte para mais extensões, pode fazê-lo importando mais adaptadores dessa forma:

```javascript
// ... Resto do seu código
import { PhantomWalletAdapter, SolflareWalletAdapter, TorusWalletAdapter } from "@solana/wallet-adapter-wallets";

// ... Resto do seu código

const App = ({ Component, pageProps }) => {
    // ... Resto do seu código
    const wallets = useMemo(() => [new PhantomWalletAdapter(), new SolflareWalletAdapter(), new TorusWalletAdapter()], [network]);

    return (
        <ConnectionProvider endpoint={endpoint}>
            <WalletProvider wallets={wallets} autoConnect>
                <WalletModalProvider>
                    <Component {...pageProps} />
                </WalletModalProvider>
            </WalletProvider>
        </ConnectionProvider>
    );
};

export default App;
```
E agora execute `npm run dev` e clique no botão `Select Wallet`. Ele deve listar algumas carteiras para você escolher, dependendo de como você configura seus adaptadores.

<img src="https://i.imgur.com/0BZZTsD.png" />

Como testamos este projeto totalmente com as Phantom Wallets, recomendamos manter isso. No entanto, nada o impede de explorar ou apoiar outras carteiras 👀.

### 🔒 Acessando a conta do usuário

Então, quando você executar isso, você deverá ver a linha "_Phantom wallet encontrada!_" impressa no console do site quando for inspecioná-lo.

![https://camo.githubusercontent.com/140bada2787e267afe24c054f7a8100d07c8143ddc9f2beac616ba9df8f746e9/68747470733a2f2f692e696d6775722e636f6d2f75794763534a342e706e67](https://camo.githubusercontent.com/140bada2787e267afe24c054f7a8100d07c8143ddc9f2beac616ba9df8f746e9/68747470733a2f2f692e696d6775722e636f6d2f75794763534a342e706e67)


_Para obter instruções adicionais sobre como executar seu aplicativo, consulte o `README.md` na raiz do seu projeto._

**LEGAL**.

Em seguida, precisamos realmente verificar se estamos **autorizados** a acessar a carteira do usuário. Assim que tivermos acesso a isso, podemos começar a ter acesso às funções do nosso programa Solana 🤘.

Basicamente, a **Phantom Wallet não simplesmente fornece as informações da nossa carteira para todos os sites que visitamos**. Ela só as fornece a sites que autorizamos. Até agora, **não** demos acesso explícito à Phantom para compartilhar as informações de nossa carteira.

A primeira coisa que precisamos fazer é verificar se um usuário nos deu permissão para usar sua carteira em nosso site - isso é como verificar se nosso usuário está "conectado". Tudo o que precisamos fazer é adicionar mais uma linha à nossa função `checkIfWalletIsConnected`. Confira o código abaixo:


```javascript
const checkIfWalletIsConnected = async () => {
  try {
    const { solana } = window;

    if (solana && solana.isPhantom) {
        console.log('Phantom wallet encontrada!');

        /* O objeto solana nos dá uma função que nos permitirá 
        conectar diretamente com a carteira do usuário! */

        const response = await solana.connect({ onlyIfTrusted: true });
        console.log(
          'Conectado com a Chave Pública:',
          response.publicKey.toString()
        );
    } else {
      alert('Objeto Solana não encontrado! Consiga uma Phantom Wallet 👻');
    }
  } catch (error) {
    console.error(error);
  }
};
```


É tão simples quanto chamar `connect`, que informa à Phantom Wallet que nosso site NFT está autorizado a acessar informações sobre essa carteira! Alguns de vocês podem estar se perguntando o que é essa propriedade `onlyIfTrusted`.

Se um usuário já conectou a carteira ao seu aplicativo, essa propriedade puxará imediatamente seus dados sem avisá-lo com outro pop-up de conexão! Bem bacana, né? Curioso para saber mais - [dê uma olhada neste documento](https://docs.phantom.app/integrating/establishing-a-connection#eagerly-connecting) da Phantom!

E é isso!

_Neste ponto, você ainda deve estar vendo apenas o log "Phantom Wallet encontrada!"_ em seu console!

Não se preocupe se você estiver vendo o erro "User Rejected Request" (solicitação do usuário rejeitada) no console. É totalmente esperado neste ponto do projeto ;), Está lá porque adicionamos esse parâmetro `onlyIfTrusted: true` dentro do método `connect`. Isso fará com que a carteira Phantom rejeite a solicitação de conexão do usuário por enquanto (como o nome do erro sugere 😁).

Por que isso? Bem, o método `connect` com o parâmetro `onlyIfTrusted` definido como `true` só será executado se o usuário já tiver autorizado uma conexão entre sua carteira e o aplicativo da web. **O que nunca fizeram até agora.** Vamos fazer isso em seguida :).


### 🚨 Relatório de progresso

Por favor faça isso, senão o Farza vai ficar triste :(

Poste uma captura de tela em `#progress` mostrando a mensagem "Phantom wallet encontrada!" no seu console. Pode parecer simples, mas, muitas pessoas não sabem como fazer essas coisas! É épico.
