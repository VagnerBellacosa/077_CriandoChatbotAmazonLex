### Pedindo uma pizza com o Amazon Lex

Publicado em [junho 22, 2019](https://douglaspicolotto.com/2019/06/22/pedindo-uma-pizza-com-o-amazon-lex/)por [Douglas](https://douglaspicolotto.com/author/douglaspicolotto/)

 

Olá, pessoal! Tudo bem?

Hoje a ideia é brincarmos um pouco com o *Amazon Lex*.

O *Amazon Lex* é uma das tecnologias *serverless* da *AWS*, utilizada para construir *chatbots* de forma muito simples. Ele abstrai toda a complexidade envolvida em construir e treinar um *chatbot*, possibilitando uma interação mais “humana” entre aplicações e seus usuários.

O *Amazon Lex* é a base da *Amazon Alexa*, com suporte a reconhecimento automático de fala (ASR) e processamento de linguagem natural (NLP). É importante frisar que ele não responde automaticamente com voz. Para isso, ele faz uma integração com o *[Amazon Polly](https://aws.amazon.com/pt/polly/)*.

### Conceitos básicos

Para começar, vamos repassar algumas terminologias básicas do Amazon Lex:

- ***Intent\*s:** Um *intent* é a “intenção” pela qual a conversação tem início, ou seja, o que o usuário está querendo fazer. Como por exemplo, pedir uma pizza ou reservar um carro;
- ***Utterances:\*** São as sentenças que iniciam determinado *intent*. Por exemplo, se tivermos um *intent* chamado *OrderAPizza*, poderíamos ter uma *utterance* com a sentença “*I would like to order a pizza*“;
- ***Slots:\*** *Slots* são os dados necessários para completar um *intent*. Para pedir uma pizza, precisaríamos de uma série de informações como, sabor (vamos simplificar usando um sabor só) da pizza, o tipo da borda e o endereço de entrega;
- ***Prompt:\*** São as perguntas feitas pelo *Amazon Lex* ao usuário, afim de obter os dados necessários para completar todos os *slots*. Como exemplo, ele pode perguntar “*What pizza flavour do you want?*“, para perguntar o sabor da pizza;
- ***Channels:\*** São integrações com serviços de mensagens. Atualmente ele suporta o *Facebook Messenger*, o *Kik*, o *Slack* e o *Twilio*.

Bom, dadas as informações gerais, vamos ao que interessa.

### Vamos pedir uma pizza?

A ideia deste post é fazermos um exemplo bem simples, explorando algumas funcionalidades do *Lex*. Para começar, precisamos de uma conta na *AWS*. Se você quiser fazer este exemplo, é fácil se cadastrar e o cadastro é gratuito.

Em seguida, localizaremos o serviço “*Amazon Lex*” e iniciaremos a criação do *bot*.

![img](https://i1.wp.com/douglaspicolotto.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-20-at-18.43.28.png?fit=748%2C396&ssl=1)Criação do *chatbot*

Durante a criação do *bot*, algumas informações importantes devem ser observadas:

- ***Name***: O nome do *bot*;
- ***Output voice***: Através da integração com o *Amazon Polly*, é possível selecionar uma voz para respostas em áudio;
- ***Session timeout:*** O *timeout* da sessão com o *Lex*. Como veremos a seguir, é possível utilizar variáveis de uma sessão. Enquanto a sessão estiver aberta, é possível manter contexto entre os *intents*;
- ***IAM Role*****:** É a role básica do *Lex*, contendo apenas com uma *policy* para permitir a sintetização de voz no *Amazon Polly*;
- ***COPPA\*:** Controle de privacidade na utilização do *bot* por crianças abaixo dos 13 anos de idade.

Criado o bot, vamos para o *intent* *“OrderAPizza”*, utilizado para fazer o pedido.

![img](https://i2.wp.com/douglaspicolotto.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-22-at-15.37.04.png?resize=748%2C387&ssl=1)*Intent configuration*

#### *Utterances*

Como citado anteriormente, para ativar este *intent* devemos definir algumas amostras de *utterance* (como *I need a pizza!!!!* ![😁](https://s.w.org/images/core/emoji/13.1.0/svg/1f601.svg)), para ele entender os padrões de ativação. O mais legal é que não precisamos utilizar exatamente estas frases. Depois de treinado, ele entende qualquer aproximação ao que for definido, como “*I need pizza*” ou “*I like pizza*“.

As palavras grifadas nos *utterances* são tokens representando os *slots* definidos logo abaixo na imagem. Então eu poderia dizer “*I want a cheese pizza at my street, 000*“, que ele extrairia dois slots do próprio *utterance*. Como falei anteriormente, ele é bem flexível, então eu poderia dizer “*I want a cheese pizza*” que ele iniciaria o *intent* e pediria os slots que faltam (*address* e *crust*) em seguida.

#### *Lambda Initialization and Validation*

Como podemos ver na imagem, também é possível integrar o *Lex* com a *AWS Lambda*, em ***Lambda Initialization e Validation***. Neste caso, a cada requisição recebida, o *Lex* irá invocar a Lambda que foi configurada, enviando uma série de informações sobre o seu estado, permitindo a manipulação das respostas (veremos essa integração no próximo *post*).

#### *Slots*

Em *slots*, devemos definir as variáveis necessárias para efetuarmos a ordem das pizzas. Para cada variável, podemos definir algumas configurações:

- ***Priority***: Define a ordem em que serão solicitadas as informações;
- ***Required***: Indica se o *slot* é obrigatório;
- ***Name***: O nome do *slot*, que posteriormente também poderá ser usado como *token* nos *utterances*;
- ***Slot Type***: O tipo do dado informado. O *Lex* possui uma série de *slots* já prontos (*built-in*). Porém é possível definir tipos customizados na barra *Slot Types* à esquerda:

![img](https://i1.wp.com/douglaspicolotto.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-22-at-15.41.14.png?resize=493%2C317&ssl=1)*Slot Type*

- ***Version***: A versão do *slot*;
- ***Prompt***: A pergunta que o *Lex* fará para obter a informação sobre o *slot*.

Se clicarmos na engrenagem de um *slot*, poderemos obter algumas configurações mais avançadas, como definir diversas opções de *prompt* e definir cards ao invés de perguntas em texto:

![img](https://i1.wp.com/douglaspicolotto.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-22-at-15.39.21.png?resize=748%2C681&ssl=1)*Slot* – Configurações avançadas

#### *Confirmation Prompt*

Ao preencher todos os *slots*, temos a possibilidade de enviar uma mensagem pedindo a confirmação do usuário, e definir uma mensagem customizada, caso ele não confirme.

![img](https://i0.wp.com/douglaspicolotto.com/wp-content/uploads/2019/06/Screen-Shot-2019-06-22-at-15.49.18.png?resize=748%2C383&ssl=1)*Intent – Fullfilment*

#### *Fullfilment*

Quando os critérios de todos os *slots* forem atendidos e o usuário confirmar o *Confirmation Prompt*, o Lex poderá seguir dois caminhos: ele pode responder diretamente ao cliente com todos os slots e o seu estado atual, ou invocar outra lambda, enviando todos os dados necessários para continuar o fluxo (no caso, persistir o pedido da pizza).

#### *Response*

Por fim podemos enviar uma resposta final ao usuário, que pode ser uma simples mensagem, e um card, dando a possibilidade de iniciar outro *intent*.

#### Falando com o *bot*

Por fim, basta salvar o *intent*, construir o *chatbot* no botão *build* (no lado superior direito) e testar. Para melhor expor o teste, abaixo gravei um vídeo com as interações com o *Lex*. Como a construção do *bot* demora um pouco, no vídeo o *intent* já está salvo e o *bot* já está construído.



*Amazon Lex example*

### Conclusão

O Amazon Lex é uma excelente ferramenta para adicionar um aspecto mais humano às aplicações. Como vimos, o único inconveniente hoje, para nós brasileiros, é que ele ainda não suporta a língua portuguesa ![🙁](https://s.w.org/images/core/emoji/13.1.0/svg/1f641.svg).

Por hoje era isso, pessoal! No próximo *post* integraremos o *Lex* com *Lambda*, dando mais poder ao *bot*.

Um grande abraço e boa semana.