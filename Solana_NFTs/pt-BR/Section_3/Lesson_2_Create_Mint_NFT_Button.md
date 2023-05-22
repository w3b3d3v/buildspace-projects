É hora de finalmente cunharmos nosso primeiro NFT na Solana.

Neste ponto, vamos chamar nossa Candy Machine para cunhar um único NFT quando alguém clicar em nosso botão.

Bora lá fazer isso!

### 🎩 Passando pela função `mintToken`

Dentra da função `CandyMachine`, no diretório `app/components/CandyMachine/index.js` você verá uma função chamada `mintToken`. Ela faz parte da biblioteca de front-end do Metaplex.

Esta função é bastante complexa. Eu não vou passar por ela linha por linha. Vá e descubra como funciona você mesmo! Uma coisa que eu recomendo fazer é usar CMD (MacOS) ou CTRL (Windows) + clique nas funções para ver como elas funcionam em um nível inferior. Observar o código geralmente é a melhor maneira de aprender como ele funciona.

Vamos então dar uma olhada em alguns pedaços de código:


```jsx
const mint = web3.Keypair.generate();

const userTokenAccountAddress = (
  await getAtaForMint(mint.publicKey, walletAddress.publicKey)
)[0];
```

Aqui estamos criando uma conta para o nosso NFT. Na Solana, os programas **não mantém o estado**, o que é muito diferente da Ethereum, onde os contratos mantêm o estado. Confira mais sobre contas [aqui](https://docs.solana.com/developing/programming-model/accounts).

```jsx
const userPayingAccountAddress = candyMachine.state.tokenMint
  ? (await getAtaForMint(candyMachine.state.tokenMint, walletAddress.publicKey))[0]
  : walletAddress.publicKey;

const candyMachineAddress = candyMachine.id;
const remainingAccounts = [];
const signers = [mint];
```

Aqui estão todos os parâmetros que a Candy Machine precisa para cunhar o NFT. Ela precisa de tudo, desde `userPayingAccountAddress`, que é a pessoa que paga e recebe o NFT, até o `mint`, que é o endereço da conta do NFT que iremos cunhar.

```jsx
const instructions = [
  web3.SystemProgram.createAccount({
    fromPubkey: walletAddress.publicKey,
    newAccountPubkey: mint.publicKey,
    space: MintLayout.span,
    lamports:
      await candyMachine.program.provider.connection.getMinimumBalanceForRentExemption(
        MintLayout.span,
      ),
    programId: TOKEN_PROGRAM_ID,
  }),
  Token.createInitMintInstruction(
    TOKEN_PROGRAM_ID,
    mint.publicKey,
    0,
    walletAddress.publicKey,
    walletAddress.publicKey,
  ),
  createAssociatedTokenAccountInstruction(
    userTokenAccountAddress,
    walletAddress.publicKey,
    walletAddress.publicKey,
    mint.publicKey,
  ),
  Token.createMintToInstruction(
    TOKEN_PROGRAM_ID,
    mint.publicKey,
    userTokenAccountAddress,
    walletAddress.publicKey,
    [],
    1,
  ),
];
```

Em Solana, uma transação é um amontoado de instruções. Então, aqui reunimos algumas instruções que são basicamente funções que vivem em nossa Candy Machine. O Metaplex nos deu essas funções. Acabamos de chegar nelas.

```jsx
   if (candyMachine.state.gatekeeper) {
    }
    
    if (candyMachine.state.whitelistMintSettings) {
    }
  
    if (candyMachine.state.tokenMint) {
    }
```

Aqui, estamos verificando se a Candy Machine está usando um captcha para evitar bots (`gatekeeper`), se há uma configuração de lista segura (whitelist) ou se a cunhagem é compatível com token gating (restrição por token, ou seja, quando um certo acesso é restrito e somente liberado ao detentor de certo token, como por exemplo, o acesso a um clube, no qual somente entra quem possui certo NFT ou token). Cada um destes itens tem um conjunto diferente de verificações que a conta do usuário precisa passar. Uma vez aprovadas, instruções adicionais são inseridas na transação.

```jsx
const metadataAddress = await getMetadata(mint.publicKey);
const masterEdition = await getMasterEdition(mint.publicKey);

const [candyMachineCreator, creatorBump] = await getCandyMachineCreator(
  candyMachineAddress,
);

instructions.push(
  await candyMachine.program.instruction.mintNft(creatorBump, {
    accounts: {
      candyMachine: candyMachineAddress,
      candyMachineCreator,
      payer: walletAddress.publicKey,
      wallet: candyMachine.state.treasury,
      mint: mint.publicKey,
      metadata: metadataAddress,
      masterEdition,
      mintAuthority: walletAddress.publicKey,
      updateAuthority: walletAddress.publicKey,
      tokenMetadataProgram: TOKEN_METADATA_PROGRAM_ID,
      tokenProgram: TOKEN_PROGRAM_ID,
      systemProgram: SystemProgram.programId,
      rent: web3.SYSVAR_RENT_PUBKEY,
      clock: web3.SYSVAR_CLOCK_PUBKEY,
      recentBlockhashes: web3.SYSVAR_RECENT_BLOCKHASHES_PUBKEY,
      instructionSysvarAccount: web3.SYSVAR_INSTRUCTIONS_PUBKEY,
    },
    remainingAccounts:
      remainingAccounts.length > 0 ? remainingAccounts : undefined,
  }),
);
```

Finalmente, depois que todas as verificações forem aprovadas, criamos as instruções para realmente cunhar o NFT.

```jsx
try {
    return (
      await sendTransactions(
        candyMachine.program.provider.connection,
        candyMachine.program.provider.wallet,
        [instructions, cleanupInstructions],
        [signers, []],
      )
    ).txs.map(t => t.txid);
  } catch (e) {
    console.log(e);
  }
```

Isso você já sabe! Usamos um provedor, nossa carteira, todas as nossas instruções e, em seguida, chamamos `sendTransactions`, que é uma função que se comunica com a blockchain. **Este é o momento mágico onde realmente chegamos para a nossa Candy Machine e dizemos para ela cunhar nosso NFT**.

Eu passei por todas essas etapas, então, certifique-se de passar por tudo isso também! Além disso, seria incrível se alguém fizesse disso tudo um módulo NPM bem legal 😂 .

### ✨ Cunhe o seu NFT

Em seu componente `CandyMachine`, faça com que seu botão "Cunhar NFT" chame a função `mintToken`:

```jsx
return (
    // Mostre isso apenas se candyMachine e candyMachine.state estiverem disponíveis
    candyMachine && candyMachine.state && (
      <div className="machine-container">
        <p>{`Data do Drop: ${candyMachine.state.goLiveDateTimeString}`}</p>
        <p>{`Itens Cunhados: ${candyMachine.state.itemsRedeemed} / ${candyMachine.state.itemsAvailable}`}</p>
        <button className="cta-button mint-button" onClick={mintToken}>
            Cunhar NFT
        </button>
      </div>
    )
  );
```


Antes de clicar em "Cunhar NFT", você precisa ter certeza que tem algum SOL da devnet em sua carteira Phantom. Esse processo é muito fácil.

Primeiro pegue o endereço público da sua carteira clicando ao lado de `Wallet`:

![Untitled](https://i.imgur.com/aIrhjCr.png)

![Untitled](https://i.imgur.com/hP51VhD.png)


Então, no seu terminal, execute:


```plaintext
solana airdrop 2 INSIRA_O_ENDEREÇO_DA_SUA_CARTEIRA_PHANTOM
```

E é isso. Parabéns por todo o dinheiro grátis que você recebeu 😂.

Legal, então agora quando você clicar em "Cunhar NFT", verá um pop-up como este abaixo:	

![Untitled](https://i.imgur.com/F12wMOq.png)

Depois de clicar em "Aprovar" e pagar a taxa de transação, sua carteira dirá à sua Candy Machine para cunhar o NFT.

**Não temos nenhum indicador de carregamento atualmente configurado** **aqui**, então pode parecer que nada está acontecendo 😂. Pode demorar uns 10 segundos para o mint realmente ocorrer

Você pode ver essa mensagem no seu console quando terminar o mint:
![Untitled](https://i.imgur.com/EszxhAH.png)

Você também pode abrir a última aba da carteira Phaton e verificar as transações:

![Untitled](https://i.imgur.com/5PCi1Vi.png)

Você cunhou com **SUCESSO** seu primeiro NFT na Solana. **Isso aí! Então, onde está o NFT?**

Clicando na transação você pode abrir o site do explorer Solana.

Caso não aparecer nenhuma trasanção abra o console e verifique o problema.

Finalmente abra a sua carteira Phantom e verifique se seu NFT aparece na seção "Colecionáveis" na seguinte aba:

![Untitled](https://i.imgur.com/viQ40iK.png)

**Pode demorar alguns segundos para atualizar !**

É incrível ver tudo isso se completando 🥳.

Você trabalhou muito para isso acontecer. Agora é hora de se divertir e deixar o seu NFT ainda melhor. Na próxima seção, adicionaremos uma interface de usuário que exibirá todas os NFTs já cunhados em seu aplicativo da web.

Tire algum tempo agora para organizar as coisas. Limpe um pouco seu código. Altere o CSS. Dê uma respirada... 😊

### 🚨 Relatório de progresso

Por favor, faça isso, senão o Yan vai ficar triste 😭.

Em `#progresso`, poste uma captura de tela dos NFTs que você cunhou! Agora também seria uma boa hora de publicar um tweet contando ao mundo o que você está fazendo. Só não se esqueça de marcar a `@web3dev_`!