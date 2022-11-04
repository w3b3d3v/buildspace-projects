### ☎️ Se comunique com a Candy Machine do seu aplicativo web

Nós finalmente conseguimos. Vamos fazer uma pequena recapitulação do que fizemos até agora. Nós: 

1. Configuramos nosso aplicativo da web.
2. Construímos um recurso de conexão à carteira
3. Configuramos nossa Candy Machine, fizemos o upload de nossos NFTs e implementamos tudo na devnet.

Tire um momento para se aplaudir 👏! Você agora faz parte de uma equipe de elite de indivíduos que sabem como fazer isso. Existem menos de 20.000 desenvolvedores em Solana neste momento. Use seu poder com sabedoria, jovem gafanhoto.

Então o que vem depois? Bem, agora é hora de configurar nosso aplicativo da web para permitir que os usuários realmente interajam com nossa Candy Machine. Precisaremos de alguma forma chamar nossa Candy Machine diretamente de nosso aplicativo web. Isso será muito parecido com chamar uma API que vive em um servidor, mas, na verdade, estaremos chamando nossa Candy Machine que vive na blockchain.

Antes de começarmos, você notará em `components/CandyMachine/index.js` que existem vários padrões de código que se repetem. Isso faz parte da biblioteca de front-end do Metaplex.

**Não** entrarei em detalhes sobre _todo_ o conteúdo do `CandyMachine/index.js`. Eu quero que você explore isso sozinho.

Algumas coisas neste arquivo são bastante avançadas, mas explore-as e mexa com elas da forma que quiser. A melhor maneira de aprender essas coisas é apenas lendo o código e brincando com ele.

Mas, não se preocupe muito em explorar ainda. Vamos fazer as coisas funcionarem primeiro (risos)!

### 🌲 Configurar as propriedades `.env`

Lembra daquelas chaves públicas que eu pedi para você manter à mão? Bem, finalmente chegou a hora de usá-las! Antes de começarmos aqui - **se você está submetendo seu código-fonte para algum repositório como o Github, certifique-se de NÃO FAZER um commit do seu arquivo `.env`**. Essa é uma prática comum para qualquer aplicativo da Web que você cria. Esses arquivos geralmente contêm informações confidenciais, portanto, **tenha cuidado**.

Ok, para começar, basta criar um arquivo `.env.local` na raiz da pasta `app` do seu aplicativo da web (ou seja, `nft-drop-starter-project/app/.env.local`). É aqui que iremos armazenar nossas chaves. Adicione o seguinte ao seu `.env.local` para iniciar:


```plaintext
NEXT_PUBLIC_CANDY_MACHINE_ID=ID_DA_SUA_CANDY_MACHINE
NEXT_PUBLIC_SOLANA_NETWORK=devnet
NEXT_PUBLIC_SOLANA_RPC_HOST=https://metaplex.devnet.rpcpool.com/
```


Vamos um por um (nota: as aspas **não** são necessárias aqui).

**Nota**: `cache.json` pode ser encontrado na raiz da sua pasta após a execução do comando Metaplex nas etapas anteriores. Para saber mais sobre o uso de variáveis de ambiente no Next.js, você pode ler [aqui](https://nextjs.org/docs/basic-features/environment-variables)

`NEXT_PUBLIC_CANDY_MACHINE_ID` - Este é o endereço que pedi para que você mantivesse à mão. Se você o perdeu, ele pode ser encontrado no arquivo JSON `cache.json`. Procure o valor associado à chave `candymachine`.

`NEXT_PUBLIC_SOLANA_NETWORK` - Defina isso como `devnet`, pois esta é apenas a rede em que estamos acessando nossa Candy Machine.

`NEXT_PUBLIC_SOLANA_RPC_HOST` - Isso é praticamente o mesmo que acima. Como estamos acessando a Candy Machine na devnet, precisamos apontar o RPC para o link devnet que é `https://explorer-api.devnet.solana.com`.

Legal, tudo isso parece estar configurado. Nosso aplicativo da web precisa de todas essas variáveis para saber coisas como: com qual Candy Machine conversar, em qual rede cunhar, etc.

Quando você alterar o arquivo `.env.local`, precisa também finalizar o processo React no Terminal e executar `npm run start` novamente. Portanto, certifique-se de fazer isso sempre que alterar o arquivo `.env.local`.

Mais uma coisa aleatória para fazer antes de seguir em frente. Na Phantom Wallet, vá para Configurações → Alterar rede → e escolha "Devnet". Nossa Candy Machine vive na devnet, então precisamos ter certeza de que nossa carteira também está na devnet!

![Untitled](https://i.imgur.com/wzxriNg.png)

### 🤬 Uma nota sobre como alterar seus NFTs

Digamos que você não gostou da coleção NFT que usou para testar e teve uma ideia melhor. Você precisa ter cuidado aqui! Sempre que você alterar sua coleção, precisará seguir as mesmas etapas anteriores para implantar os NFTs.

1. Exclua o arquivo `cache.json` que foi gerado pelos comandos da Candy Machine na CLI do Metaplex.
2. Altere seus arquivos NFT para o que você quiser!
3. Execute o comando `upload` do Metaplex via CLI para fazer o upload dos NFTs e criar uma nova Candy Machine.
4. Execute o comando `verify` do Metaplex via CLI para certificar-se de que os NFTs foram carregados e a Candy Machine foi configurada corretamente.
5. Atualize seu arquivo `.env.local` com o novo endereço.

Se você errar, mesmo que seja em um pequeno detalhe, tudo vai dar errado. Então tenha cuidado.


### 📞 Comunicando com a Candy Machine

A primeira coisa que vamos fazer é pegar os metadados da nossa Candy Machine. Esses metadados nos fornecem algumas informações interessantes, como a data do drop, quantos itens foram cunhados e quantos itens estão disponíveis para cunhar.

Também é bom começar com esse processo, porque se pudermos pegar os metadados, isso significa que configuramos nossa Candy Machine corretamente :).

Vá para `app/components/CandyMachine/index.js`.

Comece importando e configurando um `useEffect`, que chama uma função chamada `getCandyMachineState`, a qual iremos configurar.

```jsx
import React, { useEffect } from 'react';

...

const CandyMachine = ({ walletAddress }) => {

  ...
  
  useEffect(() => {
    getCandyMachineState();
  }, []);	
}
```

Antes de entrarmos na lógica da função `getCandyMachineState`, precisamos configurar outra função chamada `getProvider` .

Essencialmente, isso cria um novo objeto `AnchorProvider`. O **provedor** é o caminho para nosso aplicativo web se comunicar com a blockchain Solana - ele dá ao nosso cliente uma conexão com a Solana + nossas credenciais de carteira, para podermos conversar com programas na Solana.

Vá em frente e adicione o `getProvider` abaixo de onde você colocar o seu `useEffect`.


```jsx
const getProvider = () => {
  const rpcHost = process.env.NEXT_APP_SOLANA_RPC_HOST;
  // Crie um novo objeto de conexão
  const connection = new Connection(rpcHost);
  
  // Crie um novo objeto de provedor Solana
  const provider = new AnchorProvider(
    connection,
    window.solana,
    opts.preflightCommitment
  );

  return provider;
};
```


Ok, de volta ao `getCandyMachineState`. Coloque-o em algum lugar abaixo de `getProvider`. Veja como fica:


```jsx
// Declare getCandyMachineState como um método assíncrono
const getCandyMachineState = async () => {
  const provider = getProvider();
  
  // Obtenha os metadados do programa implantado da sua Candy Machine
  const idl = await Program.fetchIdl(candyMachineProgram, provider);

  // Crie um programa que você possa chamar
  const program = new Program(idl, candyMachineProgram, provider);

  // Busque os metadados da sua Candy Machine com o comando fetch
  const candyMachine = await program.account.candyMachine.fetch(
    process.env.NEXT_APP_CANDY_MACHINE_ID
  );
  
  // Analise todos os nossos metadados e crie um log
  const itemsAvailable = candyMachine.data.itemsAvailable.toNumber();
  const itemsRedeemed = candyMachine.itemsRedeemed.toNumber();
  const itemsRemaining = itemsAvailable - itemsRedeemed;
  const goLiveData = candyMachine.data.goLiveDate.toNumber();
  const presale =
    candyMachine.data.whitelistMintSettings &&
    candyMachine.data.whitelistMintSettings.presale &&
    (!candyMachine.data.goLiveDate ||
      candyMachine.data.goLiveDate.toNumber() > new Date().getTime() / 1000);
  
  // Usaremos isso mais tarde em nossa interface do usuário, então vamos gerar isso agora
  const goLiveDateTimeString = `${new Date(
    goLiveData * 1000
  ).toGMTString()}`

  console.log({
    itemsAvailable,
    itemsRedeemed,
    itemsRemaining,
    goLiveData,
    goLiveDateTimeString,
    presale,
  });
};

```
OK - muita coisa está acontecendo aqui. Vamos conferir.

```jsx
  // Obtenha os metadados do programa implantado da sua Candy Machine
  const idl = await Program.fetchIdl(candyMachineProgram, provider);

  // Crie um programa que você possa chamar
  const program = new Program(idl, candyMachineProgram, provider);
```

Para podermos conversar com nossa Candy Machine, precisaremos de duas coisas - **a `IDL`** (Interface Definition Language, ou Linguagem de Definição de Interface) **e um objeto `Program`**. A `IDL` tem informações que nosso aplicativo da web precisa, sobre como interagir com a Candy Machine. O `Program` é um objeto que podemos usar para **interagir diretamente** com a Candy Machine.

Você sabe como criar uma conexão com uma `Database` na web2? Bem - aqui estamos fazendo algo semelhante (risos). Mas estamos criando uma conexão com a Solana.

No final das contas, nossa Candy Machine é apenas um programa da Solana que mora no Metaplex! Isso significa que podemos interagir com ela exatamente como faríamos com qualquer programa que resida na Solana.

Uma vez que criamos nosso objeto `Program`, buscamos seus metadados com base no ID da nossa Candy Machine.

Essa linha chama o método fetch no programa da nossa Candy Machine e retorna `itemsAvailable` , `itemsRedeemed` , `itemsRemaining` e `goLiveDate`.

```jsx
// Busque os metadados da sua Candy Machine com o comando fetch
  const candyMachine = await program.account.candyMachine.fetch(
    process.env.NEXT_APP_CANDY_MACHINE_ID
  );
  
  // Analise todos os nossos metadados e crie um log
  const itemsAvailable = candyMachine.data.itemsAvailable.toNumber();
  const itemsRedeemed = candyMachine.itemsRedeemed.toNumber();
  const itemsRemaining = itemsAvailable - itemsRedeemed;
  const goLiveData = candyMachine.data.goLiveDate.toNumber();
  const presale =
    candyMachine.data.whitelistMintSettings &&
    candyMachine.data.whitelistMintSettings.presale &&
    (!candyMachine.data.goLiveDate ||
      candyMachine.data.goLiveDate.toNumber() > new Date().getTime() / 1000);
```

Quando executamos `fetch` aqui, **na verdade estamos acessando a devnet da Solana** para buscar esses dados. Parece muito que estamos atingindo uma API, mas na verdade estamos atingindo a blockchain!

### 🧠 Renderize o componente CandyMachine.

Vamos então renderizar nosso componente `CandyMachine`. Se você rolar até o final do componente `CandyMachine`, verá que renderizamos um monte de coisas abaixo de `return`. Vamos conferir!

Vá para `app/pages/index.js` e importe `CandyMachine`.

```jsx
import React from "react";
import CandyMachine from "../components/CandyMachine";
import { useWallet } from "@solana/wallet-adapter-react";
import { WalletMultiButton } from "@solana/wallet-adapter-react-ui";
```

A partir daí, queremos apenas renderizar `CandyMachine` se tivermos o endereço de carteira de um usuário no estado.


```jsx
return (
    <div className="App">
        <div className="container">
            <div className="header-container">
                <p className="header">🍭 Candy Drop</p>
                <p className="sub-text">Máquina de NFTs com cunhagem justa</p>
                {/* Renderize o botão de conexão com a carteira bem aqui */}
                {wallet.publicKey ? <CandyMachine walletAddress={wallet} /> : renderNotConnectedContainer()}
            </div>
            <div className="footer-container">
                <img alt="Twitter Logo" className="twitter-logo" src="twitter-logo.svg" />
                <a className="footer-text" href={TWITTER_LINK} target="_blank" rel="noreferrer">{`built on @${TWITTER_HANDLE}`}</a>
            </div>
        </div>
    </div>
);
```

Observe como declaramos `wallet` para `CandyMachine` :).

### 🍪 Renderize os dados recuperados

Ok! Agora, não devemos ter mais erros e nosso `useEffect` na `CandyMachine` deve ser acionado assim que atualizarmos nossa página.

Vá em frente, atualize sua página e você deverá ver algo assim em seu console:

![Untitled](https://i.imgur.com/Z6ogT3P.png)

Você literalmente acabou de buscar dados na devnet da Solana. Se você não comemorou, nossa! Por favor, faça isso agora! Isso é GRANDIOSO! Você está no caminho certo para se tornar uma lenda da web3 🤘.

_Nota: O seu `goLiveDateTimeString` pode parecer um pouco diferente. Se você quiser renderizar os dados no fuso horário local de uma pessoa, basta alterar `goLiveDateTimeString` em `getCandyMachineState` para:_

```jsx
const goLiveDateTimeString = `${new Date(
  goLiveData * 1000
).toLocaleDateString()} @ ${new Date(
  goLiveData * 1000
).toLocaleTimeString()}`;
```

_Você escolhe se quer fazer isso ou não._

Se você acessar seu site, verá que algumas coisas já estão renderizadas, mas não estamos renderizando nenhum dos dados reais. Vamos fazer isso então. _A propósito, o design não está muito legal agora, mas você terá a oportunidade de consertar isso :)_.

Então, para mostrar os dados, vamos manter as estatísticas da nossa Candy Machine em uma variável de estado. Siga adiante e importe `useState` em seu componente `CandyMachine` em `app/components/CandyMachine/index.js`, então vá em frente e adicione o seguinte código:


```jsx
// Importe useState
import React, { useEffect, useState } from 'react';

...

const CandyMachine({walletAddress}) => {
  // Adicione a propriedade de estado dentro do seu componente assim
  const [candyMachine, setCandyMachine] = useState(null);

  ...

  const getCandyMachineState = async () => { 
    const provider = getProvider();
    const idl = await Program.fetchIdl(candyMachineProgram, provider);
    const program = new Program(idl, candyMachineProgram, provider);
    const candyMachine = await program.account.candyMachine.fetch(
      process.env.NEXT_APP_CANDY_MACHINE_ID
    );
    
    const itemsAvailable = candyMachine.data.itemsAvailable.toNumber();
    const itemsRedeemed = candyMachine.itemsRedeemed.toNumber();
    const itemsRemaining = itemsAvailable - itemsRedeemed;
    const goLiveData = candyMachine.data.goLiveDate.toNumber();
    const presale =
      candyMachine.data.whitelistMintSettings &&
      candyMachine.data.whitelistMintSettings.presale &&
      (!candyMachine.data.goLiveDate ||
        candyMachine.data.goLiveDate.toNumber() > new Date().getTime() / 1000);
  
    const goLiveDateTimeString = `${new Date(
      goLiveData * 1000
    ).toGMTString()}`
  
    // Adicione esses dados ao seu estado para renderizar
    setCandyMachine({
      id: process.env.NEXT_APP_CANDY_MACHINE_ID,
      program,
      state: {
        itemsAvailable,
        itemsRedeemed,
        itemsRemaining,
        goLiveData,
        goLiveDateTimeString,
        isSoldOut: itemsRemaining === 0,
        isActive:
          (presale ||
            candyMachine.data.goLiveDate.toNumber() < new Date().getTime() / 1000) &&
          (candyMachine.endSettings
            ? candyMachine.endSettings.endSettingType.date
              ? candyMachine.endSettings.number.toNumber() > new Date().getTime() / 1000
              : itemsRedeemed < candyMachine.endSettings.number.toNumber()
            : true),
        isPresale: presale,
        goLiveDate: candyMachine.data.goLiveDate,
        treasury: candyMachine.wallet,
        tokenMint: candyMachine.tokenMint,
        gatekeeper: candyMachine.data.gatekeeper,
        endSettings: candyMachine.data.endSettings,
        whitelistMintSettings: candyMachine.data.whitelistMintSettings,
        hiddenSettings: candyMachine.data.hiddenSettings,
        price: candyMachine.data.price,
      },
    });
  
    console.log({
      itemsAvailable,
      itemsRedeemed,
      itemsRemaining,
      goLiveData,
      goLiveDateTimeString,
    });
  };
}
```


Tudo o que fizemos foi criar uma variável de estado e depois fazer uma chamada para `setCandyMachine` para definir os dados.

Com isso, podemos facilmente renderizar alguns dados interessantes aqui. Vá em frente e adicione este código de interface do usuário à sua função de renderização:

```jsx
return (
  // Mostrar isso apenas se machineStats estiver disponível
  candyMachine && (
    <div className="machine-container">
      <p>{`Data do Drop: ${candyMachine.state.goLiveDateTimeString}`}</p>
      <p>{`Itens Cunhados: ${candyMachine.state.itemsRedeemed} / ${candyMachine.state.itemsAvailable}`}</p>
      <button className="cta-button mint-button" onClick={null}>
          Cunhar NFT
      </button>
    </div>
  )
);
```


É simples assim! Você deve ver todos os dados bem renderizados em seu aplicativo da web agora.

Forneci um arquivo `CandyMachine.css` que inclui alguns estilos básicos para você. Quando estiver pronto para fazer algumas alterações, vá até lá e adicione o CSS que quiser para torná-lo seu. Mesmo se você for preguiçoso, basta mudar algumas cores por ali. Crie um estilo só seu. Não faça igual ao meu (risos).

Você notará que tem um botão "Cunhar NFT" bem bacana, mas quando você clica nele, nada acontece 😔.

Não se preocupe! Na próxima seção vamos construir a lógica para este botão e configurá-lo para cunhar nosso primeiro NFT.

### 🚨 Relatório de progresso

_Por favor, faça isso, senão o Farza vai ficar triste :(_

Em `#progress`, deixe uma captura de tela do seu aplicativo web mostrando como ele renderiza os dados que recupera de sua Candy Machine!
