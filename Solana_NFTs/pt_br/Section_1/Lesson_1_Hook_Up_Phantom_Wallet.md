### 🤖 Vamos pegar o código inicial

Comece indo para este [link do GitHub](https://github.com/w3b3d3v/Solana_NFTs), onde você encontra o código do repositório inicial. A partir daqui, você deve pressionar o botão "Fork" (bifurcação) no canto superior direito da página.

![image](https://i.imgur.com/gMLU93Y.png)

Massa! Quando você faz o fork deste repositório, na verdade você está criando uma cópia idêntica dele que fica no seu perfil do Github. Então agora você tem sua própria versão deste código que você pode editar para o conteúdo de sua escolha 😊. Isso também será útil quando estivermos prontos para implantar nosso aplicativo no Vercel 🤘.

O passo final aqui é transferir o repositório do fork que você acabou de fazer para a sua máquina local. Clique no botão "Code" (Código) e copie esse link!

![image](https://i.imgur.com/dlsiQg8.png)

Finalmente, vá para o seu terminal e execute o comando :


```plaintext
git clone SEU_LINK_DO_FORK
```

Você também opção de usar o [Replit](https://replit.com/~) se preferir!

Observação: **Você não precisa usar o replit para criar + deploy do seu site. Se você quiser trabalhar localmente no VSCode e usar Vercel/Heroku/AWS para fazer o deploy e estiver confiante em suas habilidades de desenvolvimento web - isso é totalmente legal.**

É um IDE baseado em navegador que nos permite facilmente criar aplicativos web e fazer o deploy deles a partir do navegador. E é totalmente legal. Em vez de ter que configurar um ambiente local completo e escrever comandos para fazer o deploy, tudo é dado para a gente.

Se você decidir ir com a Replit, faça uma conta lá antes de seguir em frente!

Já criei um projeto básico de Next que você pode fazer um **fork** no Replit.

[Basta clicar aqui](https://replit.com/@vitormancio/SolanaNFTsWEB3DEV) **e, à direita, você verá o botão "Fork Repl".** Certifique-se você está logado, então clique no botão.

Você clonará magicamente meu repositório e IDE completo em seu navegador para trabalhar com o código. Quando ele parar de carregar e mostrar algum código, clique em "Run" na parte superior e pronto. Pode levar de 2 a 4 minutos na primeira vez.

**Observação: à medida que avança neste projeto, você pode notar que estamos fazendo referência a arquivos `.js`. No Replit, se você estiver criando novos arquivos JavaScript, precisará usar a extensão `.jsx`! Replit tem algumas frescuras de desempenho que obrigam que você use a extensão de arquivo `.jsx` 😊.**

[Aqui está um vídeo rápido](https://www.loom.com/share/4578eb9fba1243499a6913d214b21dc3) que fiz para outro projeto, abordando alguns conceitos básicos da Replit.

Aí está 😊. Hora de codificar!

Abra o arquivo `README.md` ele contém as instruções para instalar as depências do necessárias e também rodar localmente o App. 

### 🔌 Criando um botão de conexão de carteira com a Phantom Wallet

Para este projeto, usaremos uma carteira chamada [Phantom](https://phantom.app/). Esta é uma das principais extensões de carteira para Solana.

Antes de mergulharmos em qualquer código - certifique-se de ter baixado a extensão e configurado uma carteira Solana! Atualmente, a Phantom Wallet suporta **Chrome**, **Brave**, **Firefox** e **Edge**. Mas, como nota: só testamos este código no Brave e no Chrome.

### 👻 Usando o objeto Solana

Para que nosso site converse com nosso programa Solana, precisamos de alguma forma conectar nossa carteira (que é a extensão Phantom Wallet) a ele.

Assim que conectarmos nossa carteira ao nosso site, este terá permissão para executar funções do nosso programa em nosso nome. Se nossos usuários não conectarem suas carteiras, eles não poderão se comunicar com a blockchain Solana.

**Lembre-se, é como se autenticar em um site.** Se você não estiver "conectado" ao GMail, não poderá usar o produto de e-mail deles!

Vá até seu código e acesse `index.js` em `app/pages`. É aqui que estará o principal ponto de entrada do nosso aplicativo.

Se você tiver a extensão Phantom Wallet instalada, ela injetará automaticamente um objeto especial chamado `solana` em seu objeto `window` que possui algumas funções mágicas. Isso significa que antes de fazermos qualquer coisa, precisamos verificar se isso existe. Se não existir, vamos dizer ao nosso usuário para fazer o download:

Veja aqui as mudanças que eu fiz no `index.js`, atulize no seu código substituindo ou apenas adiconando as modicações.

```jsx
import React from "react";
import dynamic from 'next/dynamic';

// Constantes
const TWITTER_HANDLE = "web3dev_";
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const Home = () => {
const WalletMultiButtonDynamic = dynamic(
    async () =>
        (await import("@solana/wallet-adapter-react-ui")).WalletMultiButton,
    { ssr: false }
    );
    // Ações
    const renderNotConnectedContainer = () => (
        <div>
            <img src="https://media.giphy.com/media/eSwGh3YK54JKU/giphy.gif" alt="emoji"/>

            <div className="button-container">
                <WalletMultiButtonDynamic className="cta-button connect-wallet-button" />
            </div>
        </div>
    );

    return (
        <div className="App">
            <div className="container">
                <div className="header-container">
                    <p className="header">🍭 Candy Drop</p>
                    <p className="sub-text">Máquina de NFTs com cunhagem justa</p>
                    {/* Renderize seu botão "Conectar à carteira" aqui */}
                    {renderNotConnectedContainer()}
                </div>

                <div className="footer-container">
                    <img alt="Twitter Logo" className="twitter-logo" src="twitter-logo.svg" />
                    <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`construido na @${TWITTER_HANDLE}`}</a>
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
            <WalletMultiButtonDynamic className="cta-button connect-wallet-button" />
        </div>
    </div>
);
```

O `WalletMultiButtonDynamic` detectará dinamicamente qualquer extensão de carteira Solana que você instalou em seu navegador, como `Phantom`, `Sollet`, `Ledger`, `Solflare` etc. Isso depende de suas configurações em `_app.js`. É assim que seu `_app.js` deve ficar.

```javascript
import { useMemo } from "react";
import { clusterApiUrl } from "@solana/web3.js";
import { WalletAdapterNetwork } from "@solana/wallet-adapter-base";
import { WalletModalProvider } from "@solana/wallet-adapter-react-ui";
import { PhantomWalletAdapter } from "@solana/wallet-adapter-wallets";
import { ConnectionProvider, WalletProvider } from "@solana/wallet-adapter-react";

import "../styles/App.css";
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


Certifique-se de ter instalado as depências e estar na pasta `app` e agora execute `npm run dev` e clique no botão `Select Wallet`. Ele deve listar algumas carteiras para você escolher, dependendo de como você configura seus adaptadores.

<img src="https://i.imgur.com/TSV1xWk.png" />

Como testamos este projeto totalmente com as Phantom Wallets, recomendamos manter isso. No entanto, nada o impede de explorar ou apoiar outras carteiras 👀.

### 🔒 Acessando a conta do usuário

Depois de fazer login com sucesso em sua carteira, seu site deve se parecer com isso.

<img src="https://i.imgur.com/jbMvgpr.png" />

_Lembrando que para obter instruções adicionais sobre como executar seu aplicativo, consulte o `README.md` na raiz do seu projeto._

**LEGAL!**

Em seguida, precisamos realmente verificar se estamos **autorizados** a acessar a carteira do usuário. Assim que tivermos acesso a isso, podemos começar a ter acesso às funções do nosso programa Solana 🤘.

Basicamente, a **Phantom Wallet não simplesmente fornece as informações da nossa carteira para todos os sites que visitamos**. Ela só as fornece a sites que autorizamos. Até agora, **não** demos acesso explícito à Phantom para compartilhar as informações de nossa carteira.

### 🚨 Relatório de progresso

_Por favor, faça isso, senão o Yan vai ficar triste 😔_

Poste uma captura de tela em `#progresso` com o seu App inicial contendo a abreviação da sua carteira. Pode parecer simples, mas, muitas pessoas não sabem como fazer essas coisas! É épico.
