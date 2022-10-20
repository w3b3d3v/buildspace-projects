<h1>🚀 Fazendo deploy na devnet</h1>
Nós praticamente temos uma API básica agora - certo 😊?

Podemos fazer um **POST** dos dados e um **GET** dos dados.

Vamos conectar nosso aplicativo web com nosso programa! O que precisamos fazer é implantar no devnet. Esta é uma rede administrada pela Solana que roda com SOL falsos.

Tecnicamente, nós *poderíamos* implantar nosso programa localmente com `solana-test-validator` e construir nosso aplicativo da web usando o programa local — mas aqui na WEB3DEV estamos muito interessados em produzir o mais rápido possível 😊. Por que mexer localmente quando podemos implantar diretamente na blockchain de verdade!?! 😂

Além disso, acho que é mais fácil construir o aplicativo da web quando implantamos nosso programa Solana no devnet! Vamos fazer isso.

*Observação: certifique-se de que `solana-test-validator` **não** esteja sendo executado em nenhum lugar.*

### 🌳 Configure seu ambiente para devnet

Na verdade, é muito complicado implantar no devnet. Fique comigo aqui e certifique-se de não perder nenhuma etapa 😊.

Primeiro, mude para devnet:

```bash
solana config set --url devnet
```

Uma vez que fizer isso, rode:

```bash
solana config get
```

E você verá que agora está apontando para [`https://api.devnet.solana.com`](https://api.devnet.solana.com/). É assim que a Anchor saberá para onde implantar!

A partir daqui, precisaremos adicionar um pouco de SOL no devnet. Na verdade, é muito fácil, apenas executamos **duas** vezes:

```bash
solana airdrop 2
```

Agora, rode:

```bash
solana balance
```

E você deve ver 4 SOL falsos em sua carteira! Na verdade, isto está mostrando seu saldo no devnet!

*Nota: às vezes você receberá um erro que diz algo como "fundos insuficientes" - sempre que isso acontecer, apenas faça um airdrop `2` SOL como acima. Nota: `2` é o máximo que você pode fazer airdrop por vez agora. Portanto, você precisará atualizar sua carteira ocasionalmente.*

### ✨ Mudando algumas variáveis

Agora, precisamos alterar algumas variáveis em `Anchor.toml`. É aqui que fica um pouco complicado.

Em `Anchor.toml`, altere `[programs.localnet]` para `[programs.devnet]`.

Em seguida, altere `cluster = "localnet"` para `cluster = "devnet"`.

Agora, execute:

```bash
anchor build
```

Isso criará uma nova compilação para nós com um ID de programa. Podemos acessá-lo executando:

```bash
solana address -k target/deploy/myepicproject-keypair.json
```

Isso produzirá o ID do seu programa, **copie-o**. Falaremos mais sobre isso em um segundo.

Agora, vá para `lib.rs`. Você verá esse id na parte superior.

```bash
declare_id!("Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS");
```

Então... o que é isso? Isso me confundiu muito quando eu estava pesquisando pela primeira vez lol.

Basicamente, é um id inicialmente gerado pelo `anchor init` que especifica o id do nosso programa.

Isso é importante porque o id do programa especifica como carregar e executar o programa e contém informações sobre como o _runtime_ da Solana deve executar o programa.

O ID do programa também ajuda o _runtime_ da Solana a ver todas as contas criadas pelo próprio programa — então, lembra como nosso programa cria "contas" que armazenam dados relacionados ao programa? Pois bem, com este ID Solana pode ver rapidamente todas as contas geradas pelo programa e consultá-las facilmente.

Portanto, **precisamos alterar este id de programa** em `declare_id!` para a saída de `solana address -k target/deploy/myepicproject-keypair.json`. Por quê? Bem, o que o `anchor init` nos deu era apenas um espaço reservado. Agora teremos um ID de programa com o qual podemos implantar!

*Observação: o ID do programa de todos será diferente! É gerado pelo Anchor.*

Agora, vá para `Anchor.toml` e em `[programs.devnet]` você verá algo como `myepicproject = "Fg6PaFpoGXkYsidMpWTK6W2BeZ7FEfcYkg476zPFsLnS"`. Vá em frente e altere este id para a mesma saída de id quando você executar `solana address -k target/deploy/myepicproject-keypair.json`.

Ótimo, então você alterou o ID do programa em dois pontos!

Finalmente, depois de fazer tudo isso, precisamos executar o comando build novamente:

```bash
anchor build
```

Por quê? Porque queremos realmente construir o projeto com nosso novo ID de programa! O Anchor gera certos arquivos em uma compilação no diretório `target` e queremos garantir que esses arquivos gerados tenham o mais recente e maior ID do programa.

** Foram muitos passos. É fácil errar e obter um erro aleatório e confuso. Para resumir:**

```
solana config set --url devnet

// Garanta que você está na devnet:
solana config get

anchor build

// Obtenha o novo ID de programa
solana address -k target/deploy/myepicproject-keypair.json

// Atualize Anchor.toml and lib.rs com o novo ID de programa
// Garanta que Anchor.toml está na devnet.

// Construa de novo
anchor build
```

### 🚀 Implante na devnet!

E, finalmente, você está pronto para implantar 😊! Vá em frente e execute:

```bash
anchor deploy
```

Você deve ver a palavra "Deploy success" 😊.

Eu recebo isso no meu terminal :

![Sem titulo](https://i.imgur.com/su2MmTb.png)

Depois de fazer isso, vá para o [Solana Explorer](https://explorer.solana.com/?cluster=devnet) para ver se tudo funcionou! *Observação: certifique-se de ir para o canto superior direito, clicar em "Mainnet" e depois em "Devnet", já que implantamos no Devnet.*

No explorer, cole o id do seu programa, o `Program Id` é listado em cima do `deploy sucess` *(no último comando que você executou).*

![Sem título](https://i.imgur.com/ISk6D55.png)

Você verá seu programa implantado!! Role para baixo e veja o histórico de transações e você verá a implantação ali mesmo.

![Sem título](https://i.imgur.com/bt1Mcgv.png)

**YO - VOCÊ ACABOU DE IMPLANTAR NA VERDADEIRA BLOCKCHAIN DA SOLANA. LEGAL.**

Obviamente, isso não é "Mainnet", mas o "Devnet" é executado por mineradores reais e é legítimo.

**Não há muitos "desenvolvedores Solana". Então neste ponto você provavelmente está entre os 10% melhores desenvolvedores de Solana lol. Parabéns!**

*Nota: A partir deste ponto, por favor, não faça alterações em lib.rs até que eu diga. Basicamente, sempre que você alterar seu programa, precisará reimplantar e seguir as etapas acima novamente. Eu sempre perco passos facilmente e recebo bugs estranhos. Vamos nos concentrar no aplicativo da Web agora, e depois mostrarei um bom fluxo de trabalho para alterar seu programa + reimplantar depois!*

### 🚨 Relatório de progresso

*Faça isso senão a Anna Bida vai ficar triste 😔*

Você implantou um programa Solana!!! Que demais! -- isso é maravilhoso!!

Vimos que os melhores construtores criaram o hábito de "construir em público". Tudo isso significa compartilhar alguns aprendizados sobre o marco que eles acabaram de atingir!

Faça uma atualização rápida na WEB3DEV em `#progresso` em nosso discord.

Este também é um bom momento para twittar que você está aprendendo sobre Solana e acabou de implantar seu primeiro programa no Solana Devnet. Inspire outros a se juntarem à web3!

Certifique-se de incluir seu link do Solana Explorer e anexar uma captura de tela do seu programa implantado, talvez. Ou adicione uma captura de tela no Solana Explorer!! Marque `@web3dev_` se estiver se sentindo bem 😊.
