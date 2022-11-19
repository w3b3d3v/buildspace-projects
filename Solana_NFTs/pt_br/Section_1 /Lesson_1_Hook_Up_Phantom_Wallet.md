### 🤖 Vamos pegar o código inicial

Comece indo para este [link do GitHub](https://github.com/w3b3d3v/Solana_NFTs), onde você encontra o código do repositório inicial. A partir daqui, você deve pressionar o botão "Fork" (bifurcação) no canto superior direito da página.

![image](https://i.imgur.com/gMLU93Y.png)

Massa! Quando você faz o fork deste repositório, na verdade você está criando uma cópia idêntica dele que fica no seu perfil do Github. Então agora você tem sua própria versão deste código que você pode editar para o conteúdo de sua escolha 😊. Isso também será útil quando estivermos prontos para implantar nosso aplicativo no Vercel 🤘.

O passo final aqui é transferir o repositório do fork que você acabou de fazer para a sua máquina local. Clique no botão "Code" (Código) e copie esse link!

![image](https://i.imgur.com/dlsiQg8.png)

Finalmente, vá para o seu terminal, dê um `cd` para qualquer diretório em que seu projeto ficará e execute o comando:


```
git clone SEU_LINK_DO_FORK
```

Aí está 😊. Hora de codificar!

Leia o arquivo `README.md` com as instruções para instalar as depências do necessárias e também rodar localmente o App. 


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

  /* Declare sua função */
  const checkIfWalletIsConnected = async () => {
    try {
      const { solana } = window;

      if (solana && solana.isPhantom) {
          console.log('Phantom wallet encontrada!');
      } else {
        alert('Objeto Solana não encontrado! Consiga uma Phantom Wallet 👻');
      }
    } catch (error) {
      console.error(error);
    }
  };

  /* Quando nosso componente for montado pela primeira vez, 
  vamos verificar se temos uma Phantom Wallet  */

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
      </div>
    </div>
  );
};

export default App;
```


Excelente! Não é tão difícil, certo? Vamos detalhar isso um pouco mais:


```javascript
const checkIfWalletIsConnected = async () => {
  try {
    const { solana } = window;

    if (solana && solana.isPhantom) {
        console.log('Phantom wallet encontrada!');
    } else {
      alert('Objeto Solana não encontrado! Consiga uma Phantom Wallet 👻');
    }
  } catch (error) {
    console.error(error);
  }
};
```


Nossa função aqui está verificando o objeto `window` em nosso DOM para ver se a extensão Phantom Wallet injetou o objeto `solana`. Se tivermos mesmo um objeto `solana`, também podemos verificar se é uma Phantom Wallet.

Como testamos este projeto inteiramente com as Phantom Wallets, recomendamos manter essa configuração. No entanto, nada o impede de explorar ou apoiar outras carteiras 👀.


```javascript
useEffect(() => {
  const onLoad = async () => {
    await checkIfWalletIsConnected();
  };
  window.addEventListener('load', onLoad);
  return () => window.removeEventListener('load', onLoad);
}, []);
```


Certifique-se de ter instalado as depências e estar na pasta `app` e agora execute `npm run dev` e clique no botão `Select Wallet`. Ele deve listar algumas carteiras para você escolher, dependendo de como você configura seus adaptadores.

<img src="https://i.imgur.com/TSV1xWk.png" />

Finalmente, só precisamos executar isso aqui!

No React, o hook `useEffect` é chamado uma vez na montagem do componente quando esse segundo parâmetro (o `[]`) está vazio! Então, isso é perfeito para nós. Assim que alguém acessa nosso aplicativo, podemos verificar se ele possui a Phantom Wallet instalada ou não. Isso será **muito importante** em breve.

Atualmente, a equipe da Phantom Wallet sugere esperar que a janela termine completamente o carregamento antes de verificar o objeto `solana`. Uma vez que este evento é chamado, podemos garantir que este objeto esteja disponível se o usuário tiver a extensão Phantom Wallet instalada.


### 🔒 Acessando a conta do usuário

Depois de fazer login com sucesso em sua carteira, seu site deve se parecer com isso: 

<img src="https://i.imgur.com/jbMvgpr.png" />

_Lembrando que para obter instruções adicionais sobre como executar seu aplicativo, consulte o `README.md` na raiz do seu projeto._

**LEGAL**.

Em seguida, precisamos realmente verificar se estamos **autorizados** a acessar a carteira do usuário. Assim que tivermos acesso a isso, podemos começar a ter acesso às funções do nosso programa Solana 🤘.

Basicamente, a **Phantom Wallet não simplesmente fornece as informações da nossa carteira para todos os sites que visitamos**. Ela só as fornece a sites que autorizamos. Até agora, **não** demos acesso explícito à Phantom para compartilhar as informações de nossa carteira.

### 🚨 Relatório de progresso

_Por favor, faça isso, senão o Yan vai ficar triste 😔_

Poste uma captura de tela em `#progresso` com o seu App inicial contendo a abreviação da sua carteira. Pode parecer simples, mas, muitas pessoas não sabem como fazer essas coisas! É épico.
