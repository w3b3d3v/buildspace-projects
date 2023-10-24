## 🤖 Obtenha o código do cliente + faça a configuração

Isso vai ser BOM DEMAIS. Eu me diverti muito fazendo a loja, colocando todo tipo de bobagem que não pertence a uma loja. Imagine uma loja que vende itens imaginários.

Vamos começar com uma das coisas mais mágicas da web3, conectar-se a um aplicativo com sua carteira.

Permitiremos que nosso usuário essencialmente "faça login" com sua carteira Solana. A construção da autenticação geralmente é bem difícil. Você precisa ter um banco de dados de nomes de usuário, senha, etc. Eu odeio como as lojas sempre pedem seu endereço de e-mail, nome de solteira da mãe, tamanho da meia esquerda e pontuação ELO do Lichess. Apenas deixe-me comprar minha única cópia do JavaScript para Leigos e sair daqui.

Neste caso é muito mais fácil do que você imagina! Aqui está o plano passo a passo:

1. Obtenha o código base do aplicativo da Web para este projeto (forneci alguns HTML/CSS iniciais para que você possa se concentrar nas coisas que realmente importam 😂.
2. Escreva o código que permitirá que os usuários conectem sua carteira Solana e se conectem ao seu aplicativo para configurar um estado de "autenticação" básico.
3. Configure um item que você deseja vender no [IPFS](https://pt.w3d.community/beperello/como-usar-o-ipfs-a-espinha-dorsal-da-web3-57jm).
4. Comece a escrever o código Solana Pay para receber pagamentos.

Vai ser **MUITO MASSA**!

Uma coisa que realmente amamos na web3dev é a criatividade insana que as pessoas colocam em seus projetos. Torne este projeto seu e faça as coisas da maneira que achar melhor.

**Se tudo o que você está fazendo é copiar/colar código, isso não será tão divertido.**

O código base do aplicativo da Web que forneço é apenas para você começar. Mude as coisas. Talvez você odeie as cores que usei. Mude. Talvez você queira o site com um tema em modo mais claro. Faça isso.

Se você acabar mudando as coisas, me marque em `#progresso` no chat do bootcamp no discord e diga - "Fala @danicuki olha que íncrivel o que eu implementei no código" e poste uma captura de tela, isso vai ser imensamente gratificante e motivador para seus colégas e nosso time do `#pod-bootcamp`.

Então pronto - mão na massa!

### 🏁 Começando

Vamos usar o **Next.js** para construir nosso aplicativo da web. É um framework **construído em cima do React.js**. Se você já está familiarizado com o React, isso será muito fácil. Se você não praticou muito com o React, não se preocupe! Você ainda pode passar por este projeto, mas pode ser um pouco mais difícil.

Porém não desista! Quanto mais dificuldades você passa, mais você aprende 🧠.

Se você não tem experiência com React ou Next você pode confirerir os documentos de introdução [aqui](https://nextjs.org/docs). Ou não faça nada de especial, apenas comece. O que for melhor para você 😊.

Você será um feiticeiro em Next após este projeto… isso se você já não for um 🧙‍♂!

Na verdade, eu escrevi este projeto primeiro no React, mas o migrei para o Next por causa do servidor embutido. Isso torna as coisas muuuuito fáceis e você não precisa lidar com o Express.js.

### ⬇️ Obtendo o código

Vá até [este link](https://github.com/w3b3d3v/solana-pay-starter) e clique em "Fork" no canto superior direito.

![](https://i.imgur.com/gMLU93Y.png)

Ótimo! Quando você bifurca esse repositório, na verdade você está criando uma cópia idêntica dele que reside no seu perfil do GitHub. Então agora você tem sua própria versão deste código que você pode editar para o conteúdo que desejar.

A etapa final aqui é realmente obter o repositório recém-bifurcado em sua máquina local. Clique no botão "Code" e copie o link!

Vá para o seu terminal e de o camando `cd` para o diretório em que seu projeto ficará. Eu recomendo colocá-lo em uma pasta conhecida onde você tem outros projetos.

Copie o link para baixar o repositório em sua máquina.

![](https://i.imgur.com/o4sl2gM.png)

```
# Estou executando isso no meu diretório "raiz"
git clone LINK_DO_FORK
cd solana-pay-starter
```

É isso! A partir daí, vá em frente e execute:

```
npm install
```

Um erro pode ocorrer se você não tiver o Node instalado. Você pode instalá-lo via NVM, seguindo [estas instruções](https://github.com/nvm-sh/nvm#installing-and-updating).

Agora você pode executar o aplicativo Web localmente com:

```
npm run dev
```

Isso deve abrir o aplicativo em seu navegador em localhost:3000.

É assim que o meu `localhost:3000` renderiza o código inicial :

![](https://i.imgur.com/2xa6feD.png)

Você também opção de usar o [Replit](https://replit.com/~) se preferir!

Observação: **Você não precisa usar o replit para criar + deploy do seu site. Se você quiser trabalhar localmente no VSCode e usar Vercel/Heroku/AWS para fazer o deploy e estiver confiante em suas habilidades de desenvolvimento web - isso é totalmente legal.**

É um IDE baseado em navegador que nos permite facilmente criar aplicativos web e fazer o deploy deles a partir do navegador. E é totalmente legal. Em vez de ter que configurar um ambiente local completo e escrever comandos para fazer o deploy, tudo é dado para a gente.

Se você decidir ir com a Replit, faça uma conta lá antes de seguir em frente!

Já criei um projeto básico de Next que você pode fazer um **fork** no Replit.

[Basta clicar aqui](https://replit.com/@vitormancio/solana-pay-starter?v=1) **e, à direita, você verá o botão "Fork Repl".** Certifique-se você está logado, então clique no botão.

Você clonará magicamente meu repositório e IDE completo em seu navegador para trabalhar com o código. Quando ele parar de carregar e mostrar algum código, clique em "Run" na parte superior e pronto. Pode levar de 2 a 4 minutos na primeira vez.

[Aqui está um vídeo rápido](https://www.loom.com/share/4578eb9fba1243499a6913d214b21dc3) que fiz para outro projeto, abordando alguns conceitos básicos da Replit.

Aí está 😊. Hora de codificar!

Para quem quer começar do zero: atenção! As bibliotecas Solana Pay são novinhas em folha. Isso significa que, se você configurar do zero usando o Create-React-App, terá [vários problemas](https://github.com/solana-labs/wallet-adapter/issues/241).. Eu passei por esses problemas de antemão e deixei tudo pronto para você!

Você pode estar se perguntando "Hmm, como o danicuki faz isso? Que segredos ele está escondendo de mim?". Bem, caro leitor, uma das primeiras coisas que todo projeto lança são **os modelos**, para que pioneiros como *você* possam trabalhar em vez de desperdiçar tempo nas configurações. Dei uma olhada nos documentos e [encontrei esses modelos iniciais](https://github.com/solana-labs/wallet-adapter/tree/master/packages/starter). Tudo o que fiz foi converter o modelo inicial do Next.js do TypeScript para JavaScript e adicionei várias estilizações.

### 🚨 Relatório de progresso

Por favor, faça isso, senão o vitordev vai ficar triste 😟

Brinque com a sua página inicial! Talvez dar outro nome? Que tipo de coisa você quer vender? Esta é a sua chance de ser criativo!

**Poste uma captura de tela do seu aplicativo da Web inicial em `#progresso` na sala exclusiva do bootcamp no nosso discord 😊.**
