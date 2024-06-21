# Construindo-ChatBot-no-Telegram-com-Dialogflow-e-Serverless

Neste projeto, vamos explorar como criar um ChatBot para o Telegram utilizando Node.js, Dialogflow e Serverless Framework. O Dialogflow é um serviço de NLP (Natural Language Processing) da Google que permite construir interfaces de conversação para bots e aplicativos. O Serverless Framework facilita a criação e implantação de funções sem servidor na nuvem.

#### **1. Introdução**

Integrar um ChatBot com o Telegram permite automatizar interações e responder às consultas dos usuários de maneira eficiente. O Dialogflow é utilizado para processar as intenções e entidades das mensagens recebidas, enquanto o Serverless Framework gerencia as funções de backend sem a necessidade de gerenciar servidores.

#### **2. Benefícios do Dialogflow e Serverless**

- **Facilidade de Implementação**: Criação de bots com processamento de linguagem natural de forma simplificada.
  
- **Escalabilidade**: Implementação sem servidor permite lidar com picos de tráfego sem preocupações com infraestrutura.
  
- **Integração com Telegram**: Utilização da API do Telegram para interações em tempo real.

#### **3. Configuração do Ambiente**

Antes de começar, é necessário configurar suas credenciais no Google Cloud Platform para Dialogflow e configurar o ambiente de desenvolvimento com Node.js e Serverless Framework.

##### **Módulo 1: Configuração Inicial**

1. **Criar um Projeto no Google Cloud Platform**:
   
   - Crie um projeto no Google Cloud Console para usar o Dialogflow.
   - Ative a API do Dialogflow para o projeto criado e configure as credenciais.

2. **Instalação do Node.js e Serverless Framework**:
   
   - Instale o Node.js se ainda não estiver instalado: [Node.js](https://nodejs.org/)
   - Instale o Serverless Framework globalmente via npm:

   ```bash
   npm install -g serverless
   ```

3. **Configuração do Ambiente Local**:
   
   - Crie um novo diretório para o projeto e inicialize o Serverless Framework:

   ```bash
   mkdir telegram-chatbot-dialogflow
   cd telegram-chatbot-dialogflow
   serverless create --template aws-nodejs --path telegram-bot
   cd telegram-bot
   ```

   - Isso cria uma estrutura básica para um serviço sem servidor com Node.js.

##### **Módulo 2: Configuração do Dialogflow**

1. **Configuração do Agente no Dialogflow**:
   
   - Crie um agente no Dialogflow para o seu bot.
   - Defina intenções, entidades e respostas de acordo com as necessidades do seu bot.

2. **Criação de Chave de Acesso ao Google Cloud**:
   
   - Gere uma chave de acesso (JSON) no Console do Google Cloud e salve o arquivo em um local seguro.

##### **Módulo 3: Implementação do ChatBot e Integração com Telegram**

1. **Configuração da API do Telegram**:
   
   - Crie um bot no Telegram usando o BotFather.
   - Anote o token do bot gerado para usar na integração.

2. **Implementação do Webhook para o Bot do Telegram**:
   
   - Crie um endpoint no Serverless Framework que receberá as mensagens do Telegram e processará com o Dialogflow.

   ```javascript
   const axios = require('axios');

   module.exports.handler = async (event) => {
       const { message } = JSON.parse(event.body);

       // Enviar mensagem recebida para o Dialogflow
       const response = await axios.post(`https://dialogflow.googleapis.com/v2/projects/${process.env.DIALOGFLOW_PROJECT_ID}/agent/sessions/123456789:detectIntent`, {
           queryInput: {
               text: {
                   text: message.text,
                   languageCode: 'pt-BR',
               },
           },
           queryParams: {
               timeZone: 'America/Sao_Paulo',
           },
           auth: {
               bearer: process.env.DIALOGFLOW_PRIVATE_KEY,
           },
       });

       const botResponse = response.data.queryResult.fulfillmentText;

       // Retornar resposta para o Telegram
       return {
           statusCode: 200,
           body: JSON.stringify({
               method: 'sendMessage',
               chat_id: message.chat.id,
               text: botResponse,
           }),
       };
   };
   ```

   - Este exemplo mostra como configurar uma função Lambda no Serverless Framework para receber mensagens do Telegram, enviar para o Dialogflow e retornar a resposta.

3. **Implantação e Configuração do Webhook**:
   
   - Implante a função no AWS Lambda usando `serverless deploy`.
   - Configure o webhook do Telegram para enviar mensagens para a URL do endpoint criado.

#### **Conclusão**

Com este projeto, aprendemos a integrar o Dialogflow e o Serverless Framework para criar um ChatBot no Telegram. A integração permite responder às mensagens dos usuários de forma automática e escalável, aproveitando as capacidades de processamento de linguagem natural do Dialogflow e a infraestrutura sem servidor do Serverless Framework.
