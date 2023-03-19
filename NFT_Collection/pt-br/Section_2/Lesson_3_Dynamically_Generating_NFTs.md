🔤 Gerando randomicamente palavras em uma imagem.
------------------

Bacana — nós criamos um contrato que agora cria NFTs dentro da rede (on-chain). Mas ainda é sempre o mesmo NFT argh !!! Vamos torná-lo dinâmico. 

**Eu escrevi esse código [aqui](https://gist.github.com/farzaa/b788ba3a8dbaf6f1ef9af57eefa63c27) que irá gerar um SVG com uma combinação de três palavras aleatórias.**

Eu acho que essa seria a melhor maneira para as pessoas olharem todo o código de uma vez e entender como ele está funcionando.

Eu também escrevi um comentário acima da maioria das linhas que adicionei/alterei! Quando você olhar para este código, tente escrevê-lo você mesmo. Pesquise no Google as funções que você não entende!

Eu quero fazer algumas observações sobre algumas dessas linhas.

📝 Escolha as suas próprias palavras.
------------------

```solidity
string[] firstWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];
string[] secondWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];
string[] thirdWords = ["YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD", "YOUR_WORD"];
```

Estas são nossas palavras aleatórias!! Por favor, divirta-se com isso. Certifique-se de que cada palavra seja única e sem espaços!

Quanto mais engraçadas as palavras, melhor será rs. Eu gostei de fazer de cada array um determinado tema. Por exemplo, `firstWords` pode ser o primeiro nome de seus personagens favoritos de anime. Então, `secondWords` pode ser um alimento de que você goste. E `thirdWords` podem ser nomes de animais aleatórios. Divirta-se com isso!!! Deixe com a sua cara

Aqui estão alguns dos meus. Eu gosto que a primeira linha tenha palavras que parecem "descrever" algo!

![](https://i.imgur.com/ADawgrB.png)

Talvez você queira gerar um nome de banda aleatório. Talvez você queira gerar nomes de personagens aleatórios para suas sessões de Dungeons and Dragons. Faça o que você quiser. Talvez você não dê a mínima para combinações de três palavras e só queira fazer SVGs de pinguins de pixel art. Vá em frente. Faça o que você quiser :).

Observação: Eu comendo entre 15-20 palavras por array. Notei que cerca de 10 geralmente não é aleatório o suficiente.

🥴 Números aleatórios.
------------------

```solidity
function pickRandomFirstWord
```

Esta função parece meio descoladona, né? Vamos falar sobre como estamos escolhendo aleatoriamente as coisas dos arrays

Então, gerar um número aleatório em contratos inteligentes é amplamente conhecido como um **problema difícil**.

Por quê? Bem, pense em como um número aleatório é gerado normalmente. Quando você gera um número aleatório normalmente em um programa, **pegará vários números diferentes de seu computador como uma fonte de aleatoriedade** como: a velocidade das ventoinhas, a temperatura da CPU, o número de vezes que você pressionou "L" às 15:52 desde que comprou o computador, a velocidade da sua internet e vários outros variáveis que são difíceis de controlar. Ele pega **todos** esses números que são "aleatórios" e os coloca juntos em um algoritmo que gera um número que parece ser a melhor tentativa de um número verdadeiramente "aleatório". Fez sentido?

No blockchain, não há **quase nenhuma fonte de aleatoriedade**. É determinista e tudo o que o contrato vê, o público vê. Por causa disso, alguém poderia enganar o sistema apenas olhando para o smart contract, vendo em que variável ele se baseia para aleatoriedade, e então a pessoa poderia manipulá-lo, se quisesse.

```solidity
random(string(abi.encodePacked("FIRST_WORD", Strings.toString(tokenId))));
```

O que isso está fazendo é pegar duas coisas: a string atual `FIRST_WORD` e uma versão stringificada do` tokenId`. Eu combino essas duas strings usando `abi.encodePacked` e então essa string combinada é o que eu uso como fonte de aleatoriedade.

**Isso não é aleatoriedade verdadeira.** Mas é o melhor que temos por agora!

Existem outras maneiras de gerar números aleatórios no blockchain (verifique o [Chainlink](https://docs.chain.link/docs/chainlink-vrf/)), mas o Solidity nativamente não nos dá nada confiável porque não pode! Todos as varaiveis que nosso contrato pode acessar são públicas e nunca verdadeiramente aleatórios.

Isso pode ser um pouco irritante para alguns aplicativos como o nosso aqui! Em qualquer caso, ninguém vai atacar nosso pequeno aplicativo, mas quero que você saiba de tudo isso quando estiver construindo um dApp que tem milhões de usuários!

✨  Criando o SVG dinamicamente.
------------------

Verifique a variável `string baseSvg` no contrato. Isso ta louco demais rs. Basicamente, a única parte do nosso SVG que muda é o combo de três palavras, certo? Então o que fazemos é criar uma variável `baseSvg` que podemos reutilizar continuamente conforme criamos novos NFTs.


Em seguida, reunimos tudo usando:

```
string memory finalSvg = string(abi.encodePacked(baseSvg, first, second, third, "</text></svg>"));
```
`</text></svg>` são as tags de fechamento! Então, para `finalSvg`, estamos dizendo: "Ei - vá combinar meu baseSVG, meu combo de três palavras que acabei de gerar e minhas tags de fechamento." É isso aí :)! Parece assustador, mas tudo o que estamos fazendo é trabalhar com o código SVG.

😎 Executando!
------------------------

Depois de escrever tudo, vá em frente e execute-o usando `npx hardhat run scripts0/run.js`. Verifique a saida produzido por `console.log(finalSvg);`.

Isto é oque aparece no meu terminal.

```plaintext
This is my NFT contract. Woah!
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
--------------------
<svg xmlns='http://www.w3.org/2000/svg' preserveAspectRatio='xMinYMin meet' viewBox='0 0 350 350'><style>.base { fill: white; font-family: serif; font-size: 24px; }</style><rect width='100%' height='100%' fill='black' /><text x='50%' y='50%' class='base' dominant-baseline='middle' text-anchor='middle'>SandwichSakuraNinja</text></svg>
--------------------
An NFT w/ ID 0 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
--------------------
<svg xmlns='http://www.w3.org/2000/svg' preserveAspectRatio='xMinYMin meet' viewBox='0 0 350 350'><style>.base { fill: white; font-family: serif; font-size: 24px; }</style><rect width='100%' height='100%' fill='black' /><text x='50%' y='50%' class='base' dominant-baseline='middle' text-anchor='middle'>GoatSasukeNinja</text></svg>
--------------------
An NFT w/ ID 1 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
```

Haha, isso é um monte de coisas. Vá em frente e copie um dos SVGs que foi gerado em seu terminal e cole-o [aqui](https://www.svgviewer.dev/) para ver o que você obtém.

Você poderá ver o SVG que foi gerado! Aqui está o meu:

![Untitled](https://i.imgur.com/uS8SXYu.png)

**TACALE PAU NESSE CARRINHO!!!!** Geramos isso aleatoriamente em nosso contrato! Se você pegar o outro SVG gerado perceberá que ele é diferente. Tudo está sendo gerado instantaneamente. YAY.

👩‍💻 Gerando os metadados dinamicamente.
------------------

Agora, precisamos definir os metadados JSON! Primeiro, precisamos de algumas funções auxiliares. Crie uma pasta chamada `libraries` em `contracts`. Em `libraries`, crie um arquivo chamado` Base64.sol` e copie e cole o código [aqui](https://gist.github.com/farzaa/f13f5d9bda13af68cc96b54851345832) nele. Este arquivo tem algumas funções auxiliares criadas por outra pessoa para nos ajudar a converter nosso SVG e JSON para Base64 no Solidity.

Okay, agora atualize o nosso contrato.

**Mesma coisa, escrevi todo o código e adicionei comentários [aqui](https://gist.github.com/farzaa/dc45da3eb91a41913767f3eb4d7830f1).**

Sinta-se à vontade para copiar e colar algumas dessas partes e entender como funciona depois de executá-lo :). Às vezes eu gosto de fazer isso porque posso ver o código sendo executado e entender como funciona depois!!

Depois de executar o contrato, aqui está o que recebo do terminal:

```plaintext
Compilation finished successfully
This is my NFT contract. Woah!
Contract deployed to: 0x5FbDB2315678afecb367f032d93F642f64180aa3
--------------------
data:application/json;base64,eyJuYW1lIjogIlNhbmR3aWNoU2FrdXJhTmluamEiLCAiZGVzY3JpcHRpb24iOiAiQSBoaWdobHkgYWNjbGFpbWVkIGNvbGxlY3Rpb24gb2Ygc3F1YXJlcy4iLCAiaW1hZ2UiOiAiZGF0YTppbWFnZS9zdmcreG1sO2Jhc2U2NCxQSE4yWnlCNGJXeHVjejBuYUhSMGNEb3ZMM2QzZHk1M015NXZjbWN2TWpBd01DOXpkbWNuSUhCeVpYTmxjblpsUVhOd1pXTjBVbUYwYVc4OUozaE5hVzVaVFdsdUlHMWxaWFFuSUhacFpYZENiM2c5SnpBZ01DQXpOVEFnTXpVd0p6NDhjM1I1YkdVK0xtSmhjMlVnZXlCbWFXeHNPaUIzYUdsMFpUc2dabTl1ZEMxbVlXMXBiSGs2SUhObGNtbG1PeUJtYjI1MExYTnBlbVU2SURJMGNIZzdJSDA4TDNOMGVXeGxQanh5WldOMElIZHBaSFJvUFNjeE1EQWxKeUJvWldsbmFIUTlKekV3TUNVbklHWnBiR3c5SjJKc1lXTnJKeUF2UGp4MFpYaDBJSGc5SnpVd0pTY2dlVDBuTlRBbEp5QmpiR0Z6Y3owblltRnpaU2NnWkc5dGFXNWhiblF0WW1GelpXeHBibVU5SjIxcFpHUnNaU2NnZEdWNGRDMWhibU5vYjNJOUoyMXBaR1JzWlNjK1UyRnVaSGRwWTJoVFlXdDFjbUZPYVc1cVlUd3ZkR1Y0ZEQ0OEwzTjJaejQ9In0=
--------------------
An NFT w/ ID 0 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
--------------------
data:application/json;base64,eyJuYW1lIjogIkdvYXRTYXN1a2VOaW5qYSIsICJkZXNjcmlwdGlvbiI6ICJBIGhpZ2hseSBhY2NsYWltZWQgY29sbGVjdGlvbiBvZiBzcXVhcmVzLiIsICJpbWFnZSI6ICJkYXRhOmltYWdlL3N2Zyt4bWw7YmFzZTY0LFBITjJaeUI0Yld4dWN6MG5hSFIwY0RvdkwzZDNkeTUzTXk1dmNtY3ZNakF3TUM5emRtY25JSEJ5WlhObGNuWmxRWE53WldOMFVtRjBhVzg5SjNoTmFXNVpUV2x1SUcxbFpYUW5JSFpwWlhkQ2IzZzlKekFnTUNBek5UQWdNelV3Sno0OGMzUjViR1UrTG1KaGMyVWdleUJtYVd4c09pQjNhR2wwWlRzZ1ptOXVkQzFtWVcxcGJIazZJSE5sY21sbU95Qm1iMjUwTFhOcGVtVTZJREkwY0hnN0lIMDhMM04wZVd4bFBqeHlaV04wSUhkcFpIUm9QU2N4TURBbEp5Qm9aV2xuYUhROUp6RXdNQ1VuSUdacGJHdzlKMkpzWVdOckp5QXZQangwWlhoMElIZzlKelV3SlNjZ2VUMG5OVEFsSnlCamJHRnpjejBuWW1GelpTY2daRzl0YVc1aGJuUXRZbUZ6Wld4cGJtVTlKMjFwWkdSc1pTY2dkR1Y0ZEMxaGJtTm9iM0k5SjIxcFpHUnNaU2MrUjI5aGRGTmhjM1ZyWlU1cGJtcGhQQzkwWlhoMFBqd3ZjM1puUGc9PSJ9
--------------------
An NFT w/ ID 1 has been minted to 0xf39fd6e51aad88f6f4ce6ab8827279cfffb92266
```

ISSO É ÉPICO. 

NOS ACABAMOS DE GERAR DINAMICAMENTE UM NFT INTEIRO. DENTRO DA BLOCKCHAIN (ON-CHAIN). ESTE É UM MOMENTO ÉPICO. 

Se você pegar um dos blobs `data:application/json;base64` e jogar na barra de endereço do navegador, verá todos os metadados JSON como antes. Exceto que agora tudo é feito automaticamente em nosso contrato :).

👀 Como raios funciona o `finalTokenUri`?
------------------

Essa linha grande com `string memory json = Base64.encode` pode parecer um pouco confusa, mas, só parece confusa por causa de todas as aspas rs. Tudo o que estamos fazendo é codificar em base64 os metadados JSON! Mas - está tudo **on-chain**. Então, todo esse JSON viverá no próprio contrato.

Nós tambem adicionamos dinamicamente o `name` e o SVG codificado em base64!

Finalmente, temos este `finalTokenUri` onde colocamos tudo junto e fazemos:

```solidity
abi.encodePacked("data:application/json;base64,", json)
```

Tudo o que está acontecendo aqui é que estamos juntando tudo e adicionando o nosso velho conhecido `data:application/json;base64,` que fizemos antes e depois acrescentamos o json codificado em base64!!


🛠 Debugando o conteudo de `finalTokenUri`
------------------------

Agora que você configurou o tokenURI, como sabemos se ele está realmente correto? Afinal, ele contém todos os nossos dados para nosso NFT! Você pode usar uma ferramenta legal como - [NFT Preview](https://nftpreview.0xdev.codes/) para ver uma visualização rápida da imagem e o conteúdo do json sem deplpoar de novo e e de novo na testnet do opensea.

Para facilitar, você pode passar o código `tokenURI` como um parâmetro de consulta como este,

```solidity
string memory finalTokenUri = string(
    abi.encodePacked("data:application/json;base64,", json)
);
console.log("\n--------------------");
console.log(
    string(
        abi.encodePacked(
            "https://nftpreview.0xdev.codes/?code=",
            finalTokenUri
        )
    )
);
console.log("--------------------\n");
```
![image](https://i.imgur.com/CsBxROj.png)

🚀 Deployando na Rinkeby
------------------
A parte mais legal é que podemos apenas re-deployar sem alterar nosso script usando:

```bash
npx hardhat run scripts/deploy.js --network rinkeby
```

Assim que fizermos o redeploy, você poderá ver seus NFTs em [https://testnets.opensea.io/](https://testnets.opensea.io/) assim que pesquisar o endereço do contrato recém deployado. Novamente, **não clique o botão enter**. O OpenSea é estranho, então você precisa clicar na própria coleção quando ela aparecer.

OBSERVAÇÃO: Lembre-se de usar `https://rinkeby.rarible.com/token/INSIRA_O_ENDEREÇO_DE_CONTRATO_AQUI:INSIRA_O_TOKEN_ID_AQUI` se você estiver usando o Rarible.

Os contratos são **permanentes**. Então, toda vez que você redeployar o nosso contrato na verdade, estamos criando uma coleção totalmente nova.

Você deve conseguir ver a nova coleção no OpenSea ou no Rarible :)!

🤖 Permitindo que os usuários cunhem (Mint).
------------------

Épico - agora podemos cunhar NFTs dinamicamente e temos esta função `makeAnEpicNFT` que os usuários podem chamar. Muito progresso foi feito!! Mas não há como pessoas aleatórias criarem NFTs :(.

Tudo o que precisamos é um site que permita que os usuários criem um NFT por conta própria.


Então, vamos construir isso :)!

🚨 Relatório de progresso.
------------------------

Se você tiver um, envie uma captura de tela em #progress de seu novo NFT gerado dinamicamente no OpenSea/Rarible em #progress :). Além disso - se você ainda não tweetou uma imagem de sua coleção maravilhosa de NFT, agora é a hora de fazê-lo!! Lembre-se de marcar @_buildspace !!! Traremos o máximo de pessoas possível!