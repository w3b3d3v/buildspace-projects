>_Se você já fez outros projetos na WEB3DEV, vários dos itens de configuração das próximas duas aulas são repetidos de projetos passados. Se você ja entende, incrível! Você é um pro. Sinta-se livre para passar por eles bem rápido._

### **📚 Um pequeno resumo sobre blockchain.**

Antes de qualquer coisa, vamos precisar colocar a nossa rede Ethereum para funcionar. Isso é como nós poderemos compilar e testar nosso contrato inteligente. Você sabe como você precisa construir um ambiente local pra trabalhar? Mesma coisa aqui!

Por agora, tudo o que você precisa saber é que um contrato inteligente é um pedaço de código que vive na blockchain. A blockchain é um lugar público onde qualquer um pode ler e escrever dados seguramente por uma taxa. Pense nela como parecida com AWS ou Heroku, tirando o fato de que ninguém é dono dela! A blockchain é rodada por milhares de pessoas aleatórias conhecidas como "mineradoras".

O quadro maior aqui é:

1. **Nós vamos escrever um contrato inteligente**. Esse contrato terá toda a lógica acerca do nosso jogo.

2. **Nosso contrato inteligente será implantado na blockchain (deployed)**. Desse jeito, qualquer pessoa no mundo poderá acessar e rodar nosso contrato inteligente - e nós vamos deixá-los acessar nosso jogo.

3. **Nós vamos construir um site para o cliente**. Isso vai deixar as pessoas conectarem suas carteiras Ethereum facilmente e jogar nosso jogo.

Eu recomendo ler [essas](https://ethereum.org/pt-br/developers/docs/intro-to-ethereum/) documentações quando você puder, por diversão. Esses são os melhores guias da internet para entender como o Ethereum funciona, na minha opinião!

### **⚙️ Configurando ferramentas locais.**

Nós vamos usar uma muito ferramenta chamada  **Hardhat**, a qual vai nos deixar compilar e testar contratos inteligentes rapidamente e localmente. Primeiro você precisa ter o node/npm. Se você não tiver, vá até  [aqui](https://hardhat.org/tutorial/setting-up-the-environment).

> 💡**Nota:** Eu estou usando o Node 16. Eu sei que algumas pessoas tiveram "erros de falta de memória" em versões mais velhas, então, se isso acontecer, utilize um versão do Node `>=16.0`!

Depois, vamos abrir o terminal. Crie a pasta chamada `epic-game` acesse ela pelo terminal utilizando o comando `cd`. Uma vez que estiver lá, rode esses comandos:

```bash
# Criar uma pasta e acessá-la
mkdir epic-game
cd epic-game

# Inicializar o projeto
npm init -y
npm install --save-dev hardhat@2.19.4
```

Você pode ver uma mensagem sobre vulnerabilidades depois de rodar o último comando e instalar o Hardhat. Toda vez que você instalar algo do NPM, existe uma triagem de segurança para ver se algum dos pacotes da biblioteca que você está instalando teve alguma vulnerabilidade reportada. Isso é mais como um aviso para que você esteja ciente! Pesquise no google um pouco mais sobre essas vulnerabilidades se quiser saber mais!

### **🔨 Colocar o projeto teste rodando**

Legal, agora nós temos o hardhat. Vamos colocar um projeto experimental funcionando.

```bash
npx hardhat init
```

> 💡**Nota:**  Se você estiver no Windows usando Git Bash para instalar o Hardhat, você pode dar de cara com um erro nesse passo (HH1). Você pode tentar usar a CMD Windows para performar a instalação do HardHat se você tiver problemas. Informações adicionais podem ser encontradas [aqui](https://github.com/nomiclabs/hardhat/issues/1400#issuecomment-824097242).

1. Esse comando irá te fazer algumas perguntas para saber que tipo de projeto você deseja criar, vamos criar um projeto JavaScript:

```bash
What do you want to do?
┗ Create a JavaScript project
```

2. Onde você deseja criar o projeto do hardhat:

```bash
Hardhat project root:
┗ Enter
```

3. Se deseja criar o arquivo `.gitignore`:

```bash
Do you want to add a .gitignore?
┗ Y
```

4. Se deseja já instalar as dependências do projeto:

```bash
Do you want to install this sample project's dependencies with npm (@nomicfoundation/hardhat-toolbox)?
┗ Y
```

O projeto vai pedir para você instalar o `@nomicfoundation/hardhat-toolbox`. Vamos utilizar isso mais tarde.

Você também vai querer instalar algo chamado  **OpenZeppelin** , que é outra biblioteca muito usada para desenvolver contratos inteligentes seguros. Vamos aprender mais sobre ela depois. Por agora, só a instale 😃.

```bash
npm install @openzeppelin/contracts
```

Depois rode:

```bash
npx hardhat run scripts/deploy.js 
```

Boom! Se você vir algumas coisas no seu terminal sobre um contrato sendo implantado **deployed**.
Isso **significa que seu ambiente local está configurando** e você também implantou **um contrato inteligente numa blockchain local** 🥳.

Isso é bastante épico. Vamos mais a fundo nisso, mas basicamente o que está acontecendo aqui é:

1. O Hardhat compila seu contrato inteligente de solidity para bytecode.
2. O Hardhat vai rodar uma "blockchain local" no seu computador. É como uma mini versão de teste do Ethereum rodando no seu computador para ajudar você a rapidamente testar coisas.
3. O Hardhat vai então fazer o "deploy" do seu contrato inteligente compilado. Esse é o endereço que você vê no final. É o seu contrato inteligente já implantado, na nossa mini versão do Ethereum.

Se estiver curioso, sinta-se livre para olhar o código dentro do projeto e ver como ele funciona. Especificamente, olhe  `Lock.sol` que é o contrato inteligente e `deploy.js` que roda o contrato.

Uma vez que tiver explorado, vamos para a próxima seção e começar o contrato do nosso jogo.

### 🚨 Reporte seu Progresso

Poste uma screenshot em #progresso com a saída do terminal quando você rodou `deploy.js` para mostrar que seu ambiente local tá rodando 😃.
