👀 Escrevendo um script para fazer o deploy local
--------------------------

*"Espere, eu já não fiz o deploy na minha rede local??"*

Bem, quase isso.

Lembre-se, quando você executa `scripts/run.js`, na verdade você está

1\. Criando uma rede local Ethereum.\
2\. Fazendo o deploy do seu contrato.\
3\. Então, quando o script terminar, a Hardhat automaticamente **destruirá** aquela rede local.

Precisamos de uma maneira de manter a rede local ativa. Por quê? Bem, pense em um servidor local. Você quer mantê-lo vivo para poder continuar falando com ele! Por exemplo, se você tiver um servidor local com uma API que você criou, você deseja manter esse servidor local ativo para poder trabalhar em seu site e testá-lo.

Nós vamos fazer a mesma coisa aqui.

Vá para o seu terminal e abra uma **nova** janela ou aba. Nesta janela, entre no diretório do seu `projeto my-wave-portal`. Então, vá em frente e execute

```bash
npx hardhat node
```

BOOM.

Você acabou de iniciar uma rede local Ethereum que **permanece viva**. E, como você pode ver, a Hardhat nos deu 20 contas para trabalhar e deu a todas elas 10.000 ETH, agora estamos ricos! Uau! Melhor projeto de todos os tempos.

Então, agora, esta é apenas uma blockchain vazia. Sem blocos!

Queremos criar um novo bloco e colocar nosso contrato inteligente nele! Vamos fazer isso.

Na pasta `scripts`, crie um arquivo chamado `deploy.js`. Aqui está o código. Parece bastante com o `run.js`.

```javascript
const main = async () => {
  const [deployer] = await hre.ethers.getSigners();
  const accountBalance = await deployer.getBalance();

  console.log("Deploying contracts with account: ", deployer.address);
  console.log("Account balance: ", accountBalance.toString());

  const Token = await hre.ethers.getContractFactory("WavePortal");
  const portal = await Token.deploy();
  await portal.deployed();

  console.log("WavePortal address: ", portal.address);
};

const runMain = async () => {
  try {
    await main();
    process.exit(0);
  } catch (error) {
    console.error(error);
    process.exit(1);
  }
};

runMain();
```

🎉 DEPLOY
---------

Agora o comando que vamos executar para realizar o deploy local é:

```bash
npx hardhat run scripts/deploy.js --network localhost
```

**Você precisa se certificar de fazer isso no diretório** `my-wave-portal` **em uma janela de terminal diferente. Não queremos mexer com a janela do terminal que mantém nossa rede local Ethereum ativa.**

Ok, então uma vez que eu executo, isso é o que que eu recebo:

![](https://i.imgur.com/ZXehYOk.png)

Épico.

Implantamos o contrato e também temos seu endereço na blockchain! Nosso site vai precisar disso para saber onde procurar o seu contrato na blockchain. (Imagine se tivesse que pesquisar em toda a blockchain pelo nosso contrato. Isso seria... ruim!).

Na janela do seu terminal que mantém sua rede local Ethereum ativa, você verá algo novo!

![](https://i.imgur.com/DmhZRJN.png)

INTERESSANTE. Mas... o que é **gas**? O que significa bloco nº 1? O que é o código grande ao lado de "Transação"? Eu quero que você tente procurar essas coisas no Google. Faça perguntas no #general-chill-chat :).


🚨 Antes de clicar em "Próxima lição"
--------------------------------------------

Honestamente, apenas dê um tapinha nas costas. Você já fez muito. Em seguida, estaremos construindo um site que interagirá com nossa rede local Ethereum e será incrível. Vá para #progress e deixe-me saber como este projeto está indo até agora para você. Eu adoraria seu feedback.


🎁 Conclusão da Seção
------------------

Ótimo! Você chegou ao final da seção. Confira [este link](https://gist.github.com/adilanchian/9f745fdfa9186047e7a779c02f4bffb7) para ter certeza de que está no caminho certo com seu código!