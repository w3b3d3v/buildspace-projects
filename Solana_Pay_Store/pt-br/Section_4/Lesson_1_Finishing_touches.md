### 🧹Toques finais

Parabéns. Você está no caminho certo para se tornar um magnata do comércio eletrônico. Jeff Bezos ?

### 🚢 Movendo-se para a rede principal

Minha parte favorita sobre este projeto é o fato de **não haver custo de implantação**. QUALQUER UM pode "implantar" este projeto gratuitamente e começar a gerar renda vendendo suas coisas. Para começar a aceitar transações na rede principal, basta atualizar duas variáveis.

1. O endereço do token "USDC" em `createTransaction.js`. O endereço do token SPL USDC da rede principal é `EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v`, portanto, sua instrução deve ficar assim:

```jsx
const usdcAddress = new PublicKey("EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v");
```

2. A enumeração de rede (`network`) em `WalletAdapterNetwork`, dentro do `_app.js` e `createTransaction.js`:

```jsx
const network = WalletAdapterNetwork.Mainnet;
```

Seu aplicativo agora está recebendo dinheiro real na rede principal. Isso é mágico.

### 🚀 Implante para o mundo (GTFOL)

A implantação de um aplicativo Next.js ficou tão fácil que não há razão para não fazê-lo neste momento 🤣. Além disso, é grátis. Você chegou até aqui, a implantação é a etapa final.

**Nota:** Como o Vercel é um sistema de arquivos somente leitura, adicionar pedidos ou produtos do aplicativo da web **não funcionará**! Isso é porque seus arquivos json do "banco de dados" não serão salvos. Se você não quiser usar o Vercel, tudo bem. Use o que quiser.

Basicamente:

- Envie seu código mais recente para o Github;
- Conecte o Vercel ao seu repositório;
- Certifique-se de adicionar suas variáveis .env;
- Implantação do aplicativo;
- Feito.

Nota: No Vercel, você precisará adicionar a 6ª variável de ambiente CI=false. Isso garantirá que nossa compilação não falhe devido a avisos.

![Vercel upload](https://i.imgur.com/wn2Uhj4.png)

### 😍 Olá, Mestre da Solana

Super emocionante que você chegou ao fim. Isso é grandioso!! Solana é uma plataforma bem recente e muito poderosa, e agora você colocou as mãos na massa com sua tecnologia central. Aí sim!! Agora você tem todas as habilidades que precisa para construir sua própria Gumroad.

Obrigado por contribuir para o futuro da web3 aprendendo essas coisas. O fato de você saber como isso funciona e como codificá-lo é um superpoder. Use seu poder com sabedoria 😉.

### 🥞 Carreiras na Web3

Várias pessoas também conseguiram empregos em tempo integral nas principais empresas da web3 pela WEB3DEV... Estou constantemente vendo as pessoas arrasarem nas suas entrevistas depois de fazerem alguns projetos de nossos bootcamps.

**As pessoas parecem pensar que a web3 só precisa de pessoas que possam codificar contratos inteligentes ou escrever código que faça interface com a blockchain. Não é verdade.**

Há muito trabalho a fazer e a maior parte do trabalho nem precisa ser feito com contratos inteligentes. **Ser um engenheiro na web3 significa apenas que você pega suas habilidades da web2 e as aplica à web3.**

Eu quero passar rapidamente por "que diabos significa trabalhar na web3 como engenheiro?" *Você precisa ser um profissional na Solana? Você precisa saber como funciona cada pequena coisa sobre o blockchain?*

Por exemplo, digamos que você seja um ótimo engenheiro de front-end. Se você terminou este projeto, **você tem quase tudo o que precisa para ser um ótimo engenheiro de front-end em uma empresa web3**. Por exemplo, a empresa pode dizer "Ei - por favor, vá e construa nosso recurso de conexão à carteira" - e você já terá uma ideia sólida de como fazer isso 😊.

Eu só quero inspirar você a trabalhar na web3. Essa coisa é incrível. E seria legal se você desse uma chance 😉.

🤟 Seu NFT!
---------
Enviaremos seu NFT em até uma hora e enviaremos um e-mail assim que estiver em sua carteira. Ele está sendo executado em um cron job! Se você não receber o e-mail em 24 horas, por favor, envie-nos uma mensagem em `#pod-bootcamp` e tag@**vitordev#1838**.

### 🌈 Antes de você ir embora

Vá para **#progresso** no Discord e envie-nos o link do seu aplicativo final 😊.

Além disso, você deve postar seu projeto final no Twitter ou Likedin e mostrar para o mundo a sua criação épica! O que você fez não foi nada fácil. Tente até fazer um pequeno vídeo mostrando seu projeto e anexe-o ao tweet. Deixe seu tweet bonito e mostre para o mundo o que você fez!!

E se você quiser, marque a @web3dev_ 😊. **Isso nos dá muita motivação, sempre que vemos as pessoas enviarem seus projetos.** Além disso, você pode inspirar outra pessoa a entrar no mundo da Solana.

Nos dê essa dose de dopamina, por favor.

Por fim, o que também seria incrível, é se você nos dissesse na seção de feedback do Discord o quanto gostou deste projeto e de como ele foi estruturado. O que você mais gostou no bootcamp? O que não curtiu? O que gostaria que mudássemos para projetos futuros? Seu feedback seria incrível!

Não deixe de mandar seu feeback na sala `#pod-bootcamp` no nosso discord.

Vejo você por aí!!!
