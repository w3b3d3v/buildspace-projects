# Lesson_3_Mint_A_NFT_Locally

📝 Escrever nosso contrato inicial.

---

_Nota: Se você já sabe como fazer muitas coisas dessa sessão pelo projeto "WavePortal" que fizemos no passado, incrível! Você vai passar por isso rápido :). Muito disso é repetido. _

Vamos fazer uma pequena faxina.

Nós queremos deletar todo o código inicial que foi gerado para nós. Vamos começar a escrever coisas nós mesmos! Vá em frente e delete o arquivo  `sample-test.js` dentro de `test`.  Também delete `sample-script.js` dentro de  `scripts`. Então, delete `Greeter.sol` dentro de  `contracts`.
**Don't delete the actual folders!**

Agora, abra o projeto no VSCode e vamos começar a escrever nosso contrato NFT. Se você nunca escreveu um contrato inteligente, não se preocupe.
**Só siga o que faremos. Procure no Google coisas que você não entender, ou pergunte no Discord.**

CRie um arquivo com o nome `MyEpicNFT.sol` dentro do diretório `contracts`. A estrutura de arquivos é super importante quando usamos Hardhat, então tenha cuidado com isso!

Nota: eu recomendo fazer o download da [extensão Solidity](https://marketplace.visualstudio.com/items?itemName=JuanBlanco.solidity) para VSCode, que fornece realce de sintaxe.

Eu sempre gosto de começar com um contrato muito básico, só para fazer as coisas andarem.

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.0;
import "hardhat/console.sol";
contract MyEpicNFT {
   constructor() {
       console.log("This is my NFT contract. Whoa!");
   }
}
```

<<<<<<< HEAD
Nota: Algumas vezes o VSCode por si só mostrará erros que não são reais, por exemplo, pode sublinhar o import do hardhat e falar que ele não existe. Isso acontece porque o compilador global de Solidity não está configurado localmente. Se você não sabe como consertar isso, não se preocupe. Ignore esses avisos por enquanto. EU também recomendo que você não use o terminal do VSCode, use o seu terminal separado! As vezes o VSCode traz problemas se o compilador não estiver configurado.
=======
Nota 2: Algumas vezes o VSCode por si só mostrará erros que não são reais, por exemplo, pode sublinhar o import do hardhat e falar que ele não existe. Isso acontece porque o compilador global de Solidity não está configurado localmente. Se você não souber como consertar isso, não se preocupe. Ignore esses avisos por enquanto. Eu também recomendo que você não use o terminal do VSCode, use o seu terminal separado! Às vezes o VSCode traz problemas se o compilador não estiver configurado.
>>>>>>> 72f0a73 (Correct typos and suggest corrections)

Vamos ver linha por linha aqui.

```solidity
// SPDX-License-Identifier: UNLICENSED
```

Só um comentário chique.  É chamado de "identificador de licença SPDX". Você pode ler mais sobre isso [aqui](https://spdx.org/licenses/).

```solidity
pragma solidity ^0.8.0;
```

Isso é uma versão do compilador solidity que queremos que o nosso contrato use. Basicamente, ele diz: "quando rodar isso, eu só quero usar a versão 0.8.0 do compilador Solidity, nada menos". Nota, esteja certo que seu compilador esteja configurado para 0.8.0 em `hardhat.config.js`.

```solidity
import "hardhat/console.sol";
```

Uma mágica é dada para nós pelo Hardhat para poder fazer alguns logs no console no nosso contrato. É desafiador debugar contratos inteligentes, mas essa é uma das vantagens que o Hardhat nos dá para deixar a vida mais fácil.

```solidity
contract MyEpicNFT {
    constructor() {
        console.log("This is my NFT contract. Whoa!");
    }
}
```

Então, contratos inteligente se parecem com uma `class` em outras linguagens, se você alguma vez já viu elas! Uma vez que inicializarmos esse contrato pela primeira vez, aquele construtor vai rodar e escrever aquela linha. Faça daquela linha o que você quiser. Se divirta!

## 😲 Como a gente roda o código?

Incrível - temos nosso contrato inteligente! Mas não sabemos se ele funciona. Na verdade, precisamos:

1. Compilá-lo.

2. Implantá-lo (deploy) na nossa blockchain local.

3. UMa vez que estiver lá, o console.log vai rodar.

Nós vamos escrever um script customizado que cuide desses 3 passos para nós.

Vá dentro do diretório `scripts` e crie um arquivo chamado `run.js`.
Isso é o que `run.js` vai ter dentro dele:

```javascript
const main = async () => {
  const nftContractFactory = await hre.ethers.getContractFactory("MyEpicNFT");
  const nftContract = await nftContractFactory.deploy();
  await nftContract.deployed();
  console.log("Contract deployed to:", nftContract.address);
};
const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.log(error);
    process.exit(1);
  }
};
runMain();
```

## 🤔 Como isso funciona?

**Nota: O VSCode pode importar automaticamente o ethers. Não precisamos importá-lo.**

De novo, vamos linha por linha.

```javascript
const nftContractFactory = await hre.ethers.getContractFactory("MyEpicNFT");
```

Isso vai na verdade compilar o nosso contrato e gerar os arquivos necessários que precisamos para trabalhar com o nosso contrato dentro do diretório `artifacts`. Cheque depois de rodar isso :).

```javascript
const nftContract = await nftContractFactory.deploy();
```

Isso é bem chique :).

O que está acontecendo aqui é que o Hardhat cria uma rede Ethereum local para a gente, mas só para esse contrato. Depois que o script for terminado, ele vai destruir essa rede local. Então, cada vez que você rodar o contrato, será uma blockchain nova. E qual é o objetivo? É como refazer o seu server local toda vez, de maneira que você sempre parta de um ponto limpo, o que deixa mais fácil o debug de erros.

```javascript
await nftContract.deployed();
```

Nós vamos esperar até que o nosso contrato esteja oficialmente minerado e implementado na nossa blockchain local! Exatamente, hardhat cria "mineradores" falsos na nossa máquina para tentar imitar da melhor forma a blockchain.

Nosso `constructor` roda quando nós estamos completamente implantados (deployed)!

```javascript
console.log("Contract deployed to:", nftContract.address);
```

Finalmente, uma vez que estiver implantado,  `nftContract.address`  vai basicamente nos dar o endereço do contrato implementado. Esse endereço é como nós vamos achar o nosso contrato na blockchain. Nesse momento nossa blockchain local só tem nós. Então, isso não é tão legal.

Mas tem milhões de contratos na blockchain de verdade. Então, esse endereço nos dá fácil acesso ao contrato que estamos interessados em trabalhar! Isso vai ser muito útil quando implantarmos nosso contrato na blockchain de verdade algumas aulas para frente.

## 💨 Rode.

Antes de rodar o código, esteja certo de mudar `solidity: "0.8.4",` para `"solidity: 0.8.0",` no seu arquivo `hardhat.config.js`.

Vamos rodar o código! Abra o seu terminal e rode:

```bash
npx hardhat run scripts/run.js
```

Você deve ver o seu  `console.log`  rodar dentro do contrato e depois você deve ver o endereço do contrato escrito!!! Aqui está o que eu consegui:

![Untitled](https://i.imgur.com/CSBimfv.png)

## 🎩 Hardhat & HRE

Nesses blocos de código você vai notar constantemente que usamos `hre.ethers`, mas `hre` não está importado em lugar nenhum? Que tipo de magia é essa?

Diretamente das documentações do Hardhat (traduzidas), vocês notarão isso:

> O Ambiente de Execução Hardhat (Hardhat Runtime Environment), ou HRE, é um objeto que contém toda a funcionalidade que o hardhat expõe quando roda uma tarefa, um teste ou um script. Na realidade, Hardhat é o HRE.
Mas o que isso significa? Então, toda vez que você roda um comando de terminal que começa com `npx hardhat`, você está pegando esse objeto `hre` construído usando `hardhat.config.js` especificado no seu código! Isso significa que você nunca vai ter que importar isso nos seus códigos, como:

`const hardhat = require("hardhat")`

**Você vai ver `hre` várias vezes no seu código, mas nunca importado em lugar nenhum! Dê uma olhada na [documentação Hardhat](https://hardhat.org/advanced/hardhat-runtime-environment.html) para aprender mais sobre!**