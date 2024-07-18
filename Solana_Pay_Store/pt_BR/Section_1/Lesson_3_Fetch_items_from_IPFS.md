## 🌐 Buscando itens no IPFS

O próximo passo em nossa poderosa aventura é adicionar itens à nossa loja. A grande questão aqui é: onde você armazena seus arquivos? Você poderia colocá-los na AWS ou em outro fornecedor de armazenamento na nuvem, mas isso não é muito web3. Em vez disso, usaremos o [IPFS](https://pt.w3d.community/paulogio/um-guia-tecnico-para-ipfs-o-armazenamento-descentralizado-da-web3-432o), que é essencialmente um sistema de arquivo distribuído. Hoje - você pode usar algo como S3 ou GCP Storage. Mas, neste caso, podemos simplesmente confiar no IPFS, que é executado por estranhos que estão usando a rede. Dê uma rápida leitura [nisto](https://pt.w3d.community/beperello/como-usar-o-ipfs-a-espinha-dorsal-da-web3-57jm) quando puder! Abrange muitos bons conhecimentos básicos 😊 .

Realmente, tudo o que você precisa saber é que o IPFS é o padrão da indústria para armazenamento de ativos. É imutável, permanente e descentralizado.

### 🛸Carregando arquivos para o IPFS

A sua utilização é bastante simples. Tudo que você precisa fazer é carregar seus arquivos no IPFS e depois usar o hash de identificação de conteúdo único que ele lhe retorna em seu aplicativo web quando quiser baixar alguma coisa.

Primeiro, você precisará carregar seus arquivos em um serviço especializado em "[pinning](https://pt.w3d.community/paulogio/fixando-arquivos-usando-o-ipfs-1bj5)" (fixação) — o que significa que seu arquivo será essencialmente armazenado em cache para que possa ser facilmente recuperado. Eu gosto de usar o [**Pinata**](https://www.pinata.cloud) para meu serviço de pinning — eles lhe dão 1 GB de armazenamento gratuito, o que é suficiente para muitos ativos. Basta criar uma conta, carregar os arquivos de sua loja através da interface do usuário, e pronto!

![](https://i.imgur.com/CUQ3XEy.png)

Vá em frente e copie o"CID" do arquivo. Este é o endereço do conteúdo do arquivo no IPFS! O que é legal agora é que podemos criar este link para acessar o arquivo:

```javascript
https://cloudflare-ipfs.com/ipfs/ADICIONE_SEU_CID_AQUI
```


Se você estiver usando o **Navegador Brave** (que tem o IPFS embutido) você pode simplesmente digitar esta pasta no URL:


```javascript
ipfs://ADICIONE_SEU_CID_AQUI
```


E isso vai realmente iniciar um nó IPFS em sua máquina local e recuperar o arquivo! Se você tentar fazer isso em algo como o Chrome, ele apenas faz uma busca no Google 😂. Ao invés disso, você terá que usar o link `cloudflare-ipfs`.

E agora você sabe como usar o IPFS! Mas há um senão em nosso cenário - já que os itens no IPFS são públicos, **qualquer pessoa** pode acessá-los se tiver o hash de identificação de conteúdo único. Exploraremos métodos para proteger nossas lojas disso mais tarde 😉.


### 🎈Baixando Arquivos do IPFS

Tudo o que sobe, deve descer. A menos que seja um foguete para a lua. Ou os 21 balões de hélio que comprei no meu quarto aniversário sem contar aos meus pais. Pergunto-me onde eles estão hoje.

Baixar os arquivos do IPFS é quase mais fácil do que carregá-los 😂. Eu deixei um arquivo chamado `useIPFS` na pasta _hooks_(ganchos). Dê uma olhada - tudo que ele faz é adicionar o hash e o nome do arquivo a um URL de acesso ao IPFS.

Você pode encontrar outros portais públicos do IPFS [aqui.](https://luke.lol/ipfs.php)

Este é um arquivo bem pequeno para que pudéssemos mantê-lo no componente em que o usaremos, mas é uma boa prática separar os hooks. Em seguida, vamos criar um componente para usar este hook.

Adicione um arquivo chamado `IpfsDownload.js` na pasta `components` e acrescente isto a ele:

```jsx
import React  from 'react';
import useIPFS from '../hooks/useIPFS';

const IPFSDownload = ({ hash, filename }) => {

  const file = useIPFS(hash, filename);

  return (
    <div>
      {file ? (
        <div className="download-component">
          <a className="download-button" href={file} download={filename}>Download</a>
        </div>
      ) : (
        <p>Fazendo download do arquivo...</p>
      )}
    </div>
  );
};

export default IPFSDownload;
```

Sim, é realmente **MUITO** fácil! Tudo o que isso faz é retornar um componente com um link que você pode colocar em qualquer lugar em seu aplicativo. Quando alguém clica nesse link, eles vão baixar o arquivo!

### 😔 Dê as mercadorias gratuitamente

Eu menti: nós não estamos fazendo uma loja. Na verdade, estamos construindo uma instituição de caridade que acredita que o mundo precisa de melhores emojis.

Uma vez que ainda não temos transações estabelecidas, vamos apenas deixar as pessoas baixarem os itens em nossa loja gratuitamente agora mesmo. Isto nos permitirá tirar todas as pequenas coisas do caminho e concentrar nas transações.

Crie uma pasta `api` no diretório `pages` e acrescente um arquivo `products.json` nela. Este vai ser nosso "banco de dados" falso. Quero que você faça um produto que você possa usar no mundo real, então quando você estiver na trajetória para ser maior que a Gumroad, tudo o que você precisará fazer é trocar o ponto de extremidade de `/products.json` para um banco de dados real, como Supabase ou CockroachDB.

Aqui está a aparência do meu arquivo, você pode adicionar ou remover campos com base em seu produto:

```json
[
  {
    "id": 1,
    "name": "OG Emoji pack",
    "price": "0.09",
    "description": "Obtenha um pacote de emojis irados por apenas $0.09! Inclui 3 emojis legais: Forreal, Flooshed, and Sheesh!",
    "image_url": "https://i.imgur.com/rVD8bjt.png",
    "filename": "Emojis.zip",
    "hash": "QmWWH69mTL66r3H8P4wUn24t1L5pvdTJGUTKBqT11KCHS5"
  }
]
```

Os principais campos com os quais nos preocupamos são os campos de identificação, nome e preço.

A seguir, vamos criar um arquivo `Product.js` na pasta `components`. Vamos usá-lo para exibir nossos produtos. Aqui está como ele vai ficar:

```jsx
import React from "react";
import styles from "../styles/Product.module.css";
import IPFSDownload from './IpfsDownload';

export default function Product({ product }) {
  const { id, name, price, description, image_url } = product;

  return (
    <div className={styles.product_container}>
      <div >
        <img className={styles.product_image}src={image_url} alt={name} />
      </div>

      <div className={styles.product_details}>
        <div className={styles.product_text}>
          <div className={styles.product_title}>{name}</div>
          <div className={styles.product_description}>{description}</div>
        </div>

        <div className={styles.product_action}>
          <div className={styles.product_price}>{price} USDC</div>
          {/* Estou usando um código rígido. Isto por enquanto. Vamos buscar o hash da API mais tarde.*/}
          <IPFSDownload filename="emojis.zip" hash="QmWWH69mTL66r3H8P4wUn24t1L5pvdTJGUTKBqT11KCHS5" cta="Download emojis"/>
        </div>
      </div>
    </div>
  );
}
```

Antes de podermos utilizá-lo, precisaremos criar um ponto de extremidade API que possa buscar nossos produtos em nosso "banco de dados". Vá até a pasta `api` no diretório `pages` e acrescente um arquivo chamado `fetchProducts.js`:

```jsx
import products from "./products.json"

export default function handler(req, res) {
  // Se for solicitado
  if (req.method === "GET") {
    // Criar uma cópia dos produtos sem os hashes e nomes de arquivo
    const productsNoHashes = products.map((product) => {

      const { hash, filename, ...rest } = product;
      return rest;
    });

    res.status(200).json(productsNoHashes);  
  }
  else {
    res.status(405).send(`Method ${req.method} not allowed`);
  }
}
```


Você vai notar que não estamos pegando os hashes! Isto porque não queremos dar aos observadores os hashes antes que eles paguem pelos itens, já que podem simplesmente baixá-los rsrs.

Agora, para usar este ponto de extremidade e o componente Product, só precisamos atualizar nosso `index.js`:

```jsx
import React, { useEffect, useState } from 'react';
import Product from "../components/Product";

import { useWallet } from '@solana/wallet-adapter-react';
import { WalletMultiButton } from '@solana/wallet-adapter-react-ui';

// Constantes
const TWITTER_HANDLE = 'web3dev_';
const TWITTER_LINK = `https://twitter.com/${TWITTER_HANDLE}`;

const App = () => {
  const { publicKey } = useWallet();
  const [products, setProducts] = useState([]);

  useEffect(() => {
    if (publicKey) {
      fetch(`/api/fetchProducts`)
        .then(response => response.json())
        .then(data => {
          setProducts(data);
          console.log("Products", data);
        });
    }
  }, [publicKey]);

  const renderNotConnectedContainer = () => (
    <div className="button-container">
      <WalletMultiButton className="cta-button connect-wallet-button" />
    </div>
  );


  const renderItemBuyContainer = () => (
    <div className="products-container">
      {products.map((product) => (
        <Product key={product.id} product={product} />
      ))}
    </div>
  );

  return (
    <div className="App">
      <div className="container">
        <header className="header-container">
          <p className="header"> 😳 Loja de emojis 😈</p>
          <p className="sub-text">A única loja de emojis que aceita shitcoins</p>
        </header>

        <main>
          {publicKey ? renderItemBuyContainer() : renderNotConnectedContainer()}
        </main>

        <div className="footer-container">
          <img alt="Twitter Logo" className="twitter-logo" src="twitter-logo.svg" />
          <a
            className="footer-text"
            href={TWITTER_LINK}
            target="_blank"
            rel="noreferrer"
          >{`contruido na @${TWITTER_HANDLE}`}</a>
        </div>
      </div>
    </div>
  );
};

export default App;
```

Agora você deve ver um botão "Download" em seu aplicativo depois de conectar sua carteira! Uma vez que você clique em download, nosso hook será chamado e o arquivo será obtido e baixado do IPFS. Isto pode levar um pouco de tempo na primeira vez, portanto, por favor, seja paciente!

Tenha em mente que os arquivos no IPFS são armazenados em cache através de vários nós, portanto, se você _acabou_ de carregar algo, ele só existirá em alguns nós e levará um pouco de tempo para ser baixado. Quanto mais seus arquivos forem acessados, mais nós serão colocados em cache e mais rápido eles serão baixados!

Se seu arquivo não for baixado, você terá que mudar para um portal IPFS diferente. Confira [este ótimo artigo ](https://github.com/maxim-saplin/ipfs_gateway_research/blob/main/README.md) comparando as opções mais conhecidas.


### 🚨 Relatório de progresso

Por favor, faça isso, senão a Thaíssa vai ficar triste 😟

**Publique uma captura de tela de seu arquivo carregado no Pinata 🤗**
