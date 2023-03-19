📦 Armazene os dados!
------------------

A partir daqui, vamos adicionar alguns detalhes ao nosso contrato.

Queremos ser capazes de deixar alguém acenar para nós e depois armazenar esse aceno.

Então, a primeira coisa que precisamos é de uma função que eles possam usar para acenar!

A blockchain = Pense nela como um provedor de nuvem, como a AWS, mas que não pertence a ninguém. É executada pelo poder computacional de máquinas de mineração em todo o mundo. Normalmente essas pessoas são chamadas de mineradores e nós os pagamos para executar nosso código!

Um contrato inteligente = algo como o código do nosso servidor com diferentes funções e que as pessoas podem acessar.

Então, aqui está nosso contrato atualizado que podemos usar para "armazenar" os acenos.

```solidity
// SPDX-License-Identifier: UNLICENSED

pragma solidity ^0.8.0;

import "hardhat/console.sol";

contract WavePortal {
    uint256 totalWaves;

    constructor() {
        console.log("Yo yo, I am a contract and I am smart");
    }

    function wave() public {
        totalWaves += 1;
        console.log("%s has waved!", msg.sender);
    }

    function getTotalWaves() public view returns (uint256) {
        console.log("We have %d total waves!", totalWaves);
        return totalWaves;
    }
}
```

Boom!

Então, é assim que você escreve uma função no Solidity. E também adicionamos uma variável `totalWaves` que é inicializada automaticamente em 0. Mas essa variável é especial porque é chamada de "variável de estado" e é legal porque é armazenada permanentemente na área de armazenamento do contrato.

Nós também fazemos alguma mágica aqui com `msg.sender`. Este é o endereço da carteira da pessoa que chamou a função. Isso é incrível! É como autenticação embutida. Sabemos exatamente quem chamou a função porque, para chamar uma função de um contrato inteligente você precisa estar conectado com uma carteira válida!

No futuro, podemos escrever funções que apenas determinados endereços de carteira podem acessar. Por exemplo, podemos alterar esta função para que apenas nosso endereço tenha permissão para enviar um aceno. Ou, talvez, tê-lo apenas onde seus amigos podem acenar para você!

✅ Atualizando run.js para chamar nossas funções
---------------------------------------

Então, `run.js` precisa mudar!

Por quê?

Bem, precisamos chamar manualmente as funções que criamos.

Basicamente, quando implantamos nosso contrato na blockchain (o que fazemos quando executamos `waveContractFactory.deploy()`), nossas funções ficam disponíveis para serem chamadas na blockchain porque usamos essa palavra-chave especial **public** em nossa função.

Pense nisso como um endpoint de API pública :).

Então agora queremos testar essas funções especificamente!

```javascript
const main = async () => {
  const [owner, randomPerson] = await hre.ethers.getSigners();
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();

  console.log("Contract deployed to:", waveContract.address);
  console.log("Contract deployed by:", owner.address);

  let waveCount;
  waveCount = await waveContract.getTotalWaves();

  let waveTxn = await waveContract.wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();
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
**O VSCode pode importar automaticamente `ethers`, logo não precisamos importar `ethers`. Portanto, certifique-se de não ter importações. Lembre-se, sobre o que falamos na última aula sobre hre?**

🤔 Como funciona?
-----------------

```javascript
const [owner, randomPerson] = await hre.ethers.getSigners();
```

Para fazer o deploy de algo na blockchain, precisamos ter um endereço de carteira! A Hardhat faz isso para nós magicamente em segundo plano, mas aqui eu peguei o endereço da carteira do proprietário do contrato e também peguei um endereço aleatório da carteira e chamei de `randomPerson`. Isso fará mais sentido em um momento.

Acrescentei também:

```javascript
console.log("Contract deployed by:", owner.address);
```

Estou fazendo isso só para ver o endereço da pessoa que está fazendo o deploy do nosso contrato. Estou curioso!

A última coisa que adicionei foi isso:

```javascript
let waveCount;
waveCount = await waveContract.getTotalWaves();

let waveTxn = await waveContract.wave();
await waveTxn.wait();

waveCount = await waveContract.getTotalWaves();
```

Basicamente, precisamos chamar manualmente nossas funções! Assim como faríamos com qualquer API normal. Primeiro eu chamo a função para pegar o número total de acenos. Então, eu faço o aceno. Finalmente, pego o waveCount mais uma vez para ver se mudou.

Execute o script como faria normalmente:

```bash
npx hardhat run scripts/run.js
```

Aqui está minha saída:

![](https://i.imgur.com/NgfOns3.png)

Muito legal, hein :)?

Você também pode ver o endereço da carteira que acenou igual ao endereço que implantou o contrato. Eu acenei para mim mesmo!

Então nós:\
1\. Chamamos nossa função de onda.\
2\. Mudamos a variável de estado.\
3\. Lemos o novo valor da variável.

Esta é praticamente a base da maioria dos contratos inteligentes. Funções de leitura. Funções de escrita. E alterando uma variável de estado. Temos os blocos necessários para nos manter trabalhando no nosso WavePortal épico.

Muito em breve, poderemos chamar estas funções a partir de nosso aplicativo de reação no qual estaremos trabalhando :).


🤝 Testar outros usuários
--------------------

Então, provavelmente queremos que outra pessoa além de nós nos envie uma onda, certo? Seria muito chato se pudéssemos enviar uma onda!! Queremos fazer nosso site **multiplayer***!

Confira. Acrescentei algumas linhas no final da função. Não vou explicar muito (mas por favor, faça perguntas em #general-chill-chat). Basicamente, é assim que podemos simular outras pessoas chamando nossas funções :). Fique de olho nos endereços das carteiras em seu terminal uma vez que você mudar e executar o código.

```javascript
const main = async () => {
  const [owner, randomPerson] = await hre.ethers.getSigners();
  const waveContractFactory = await hre.ethers.getContractFactory("WavePortal");
  const waveContract = await waveContractFactory.deploy();
  await waveContract.deployed();

  console.log("Contract deployed to:", waveContract.address);
  console.log("Contract deployed by:", owner.address);

  let waveCount;
  waveCount = await waveContract.getTotalWaves();

  let waveTxn = await waveContract.wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();

  waveTxn = await waveContract.connect(randomPerson).wave();
  await waveTxn.wait();

  waveCount = await waveContract.getTotalWaves();
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

Os itens mais recentes adicionados a este bloco de código são:

```javascript
waveTxn = await waveContract.connect(randomPerson).wave();
await waveTxn.wait();

waveCount = await waveContract.getTotalWaves();
```

🚨 Antes de clicar em "Next Lesson" (próxima lição)
-------------------------------------------

*Note: se você não fizer isso, Farza ficará muito triste :(.*

Personalize um pouco seu código!! Talvez você queira armazenar algo mais? Eu quero que você faça bagunça. Talvez você queira armazenar o endereço do remetente em uma matriz? Talvez você queira armazenar um mapa de endereços e contagens de ondas para que você mantenha um registro de quem está acenando mais para você? Mesmo que você apenas mude os nomes das variáveis e funções para ser algo que você ache interessante, isso é um grande negócio. Tente não me copiar diretamente! Pense em seu site final e no tipo de funcionalidade que você deseja. Construa a funcionalidade **que você quer***.

Uma vez que tudo tenha terminado aqui, não deixe de publicar um _screenshot_ do seu terminal em #progress.
