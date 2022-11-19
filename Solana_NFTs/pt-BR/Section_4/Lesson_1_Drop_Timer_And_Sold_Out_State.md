### 🌍 Aprimore seus NFTs com IPFS

Ao implantar na devnet, você não precisa se preocupar em armazenar seus NFTs, pois o Metaplex permite que você carregue até 10 ativos gratuitamente. Isso é super útil, mas você não pode depender disso quando for para a rede principal 😂 .

O que acontece quando você quiser ir para a rede principal? Você pode seguir a rota padrão e enviá-los para o Arweave, mas isso custará algum dinheiro. Em vez disso, podemos usar algo chamado <a href="https://www.web3dev.com.br/fatimalima/como-salvar-um-artigo-em-ipfs-um-guia-de-7-passos-je6">IPFS.</a> Em poucas palavras, é um sistema de armazenamento de arquivos descentralizado, semelhante ao Arweave, mas **muito** mais barato (às vezes até gratuito). Achei mais fácil usar o [Pinata](https://www.pinata.cloud/) para fazer o upload para o IPFS. Além disso, eles oferecem 1 GB de armazenamento gratuito, o que é suficiente para milhares de ativos. Eu ainda não implantei nada na rede principal, mas de qualquer maneira usei o Pinata, porque ele permite fazer o upload de arquivos muito maiores.

Usá-lo é bastante simples. Depois de se inscrever em uma conta, selecione "API Keys" (chaves de API) no menu suspenso do canto superior direito.

![API KEY](https://i.imgur.com/W1SziZt.png)

Crie uma nova chave e certifique-se de que o acesso à `pinFileToIPFS` esteja ativado.

![Pinata config](https://i.imgur.com/lJFWI54.png)

Depois de criar a chave, você verá um pop-up com todos os segredos. Copie o token JWT e mantenha-o à mão. Agora apenas atualizaremos nosso arquivo `config.json` com um novo objeto com quatro novos objetos, o único que precisamos alterar é o objeto `JWT`:

```json
{
  "price": 0.01,
  "number": 3,
  "gatekeeper": null,
  "creators": [
    {
      "address": "ENDEREÇO_DA_SUA_CARTEIRA",
      "share": 100      // Certifique -se de que a participação total entre todos os criadores se resume até exatamente 100
    }
  ],
  "solTreasuryAccount": "ENDEREÇO_DA_SUA_CARTEIRA",
  "splTokenAccount": null,
  "splToken": null,
  "goLiveDate": "2022-05-02T18:00:00+00:00",
  "endSettings": null,
  "whitelistMintSettings": null,
  "hiddenSettings": null,
  "freezeTime": null,
  "retainAuthority": true,
  "isMutable": true,
  "symbol": "NB",
  "sellerFeeBasisPoints": 1,
  "awsConfig": null,
  "uploadMethod": "pinata",
   "pinataConfig":
  {
    "jwt": "<JWT>",
    "apiGateway": "https://api.pinata.cloud",
    "contentGateway":"https://gateway.pinata.cloud",
    "parallelLimit":3
  },
  "shdwStorageAccount": null
}
```

Então siga os passos citadados anteriormente para atulizar os dados da coleção de NFTs.

**Não** se esqueça de atualizar o valor de `NEXT_PUBLIC_CANDY_MACHINE_ID` em `.env.local` com o novo `Candy machine ID`.

E pronto! Agora realmente você tem NFTs de alta qualidade na devnet. Se você quiser saber mais sobre o IPFS, [confira isso aqui](https://www.web3dev.com.br/paulogio/um-guia-tecnico-para-ipfs-o-armazenamento-descentralizado-da-web3-432o).

### 🚀 Lance para o mundo

A implatação pode ser feita facilmente pela Vercel, criadora do Next.js, e ainda é **grátis**.

Você chegou até aqui, a implantação é a etapa final. Além disso, seus colegas construtores da web3dev não devem ser privados de seus NFTs!! Por favor, nos dê a oportunidade de cunhar suas criações raras 😂.

Basicamente:

* Envie seu código mais recente para o Github. Não faça o commit do `cache.json`;
* Conecte o Vercel ao seu repositório;
* Certifique-se de definir sua raiz para `app`;
* Faça a implantação;
* Concluído!

🚧 Para dar mais segurança, verifique o arquivo `.gitignore` na pasta `root`, para que ele ignore automaticamente todos os arquivos e não seja enviado ao Github. É assim que meu `gitignore` se parece:

```javascript
.DS_Store
.env
.env.local
node_modules
cache.json
config.json
sugar.log
```

[Loom](https://www.loom.com/share/ce89a285b90a4b34ac358fce9ae7f92d)

Nota: No Vercel, você precisará adicionar a 6ª variável de ambiente como `CI=false`. Isso garantirá que nossa compilação não falhe devido a avisos.

![Untitled](https://i.imgur.com/wn2Uhj4.png)


### 😍 Olá, Mestre da Solana

É super emocionante que você conseguiu chegar ao fim. Isso é algo grandioso!! A Solana ainda está **super no início** e já é muito poderosa! E agora você teve a chance de mexer com a tecnologia principal. Aí sim!! Agora você tem todas as habilidades que precisa para construir seus próprios drops de NFT na Solana.

Obrigado por contribuir para o futuro da web3 aprendendo essas coisas. O fato de você saber como isso funciona e como codificar tudo isso é um superpoder. Use seu poder com sabedoria 😉.

### 🌈 Antes de você ir embora

Vá para **#progresso** no Discord e envie-nos o link do seu aplicativo final, pois queremos cunhar sua nft 😊.

Além disso, você deve postar seu projeto final no Twitter ou Likedin e mostrar para o mundo a sua criação épica! O que você fez não foi nada fácil. Tente até fazer um pequeno vídeo mostrando seu projeto e anexe-o ao tweet. Deixe seu tweet bonito e mostre para o mundo o que você fez!!

E se você quiser, marque a @web3dev_ 😊. **Isso nos dá muita motivação, sempre que vemos as pessoas enviarem seus projetos.** Além disso, você pode inspirar outra pessoa a entrar no mundo da Solana.

Nos dê essa dose de dopamina, por favor.

Por fim, o que também seria incrível, é se você nos dissesse na seção de feedback do Discord o quanto gostou deste projeto e de como ele foi estruturado. O que você mais gostou no bootcamp? O que não curtiu? O que gostaria que mudássemos para projetos futuros? Seu feedback seria incrível!

Não deixe de mandar seu feeback na sala `#pod-bootcamp` no nosso discord.

Vejo você por aí!!!