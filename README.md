# ZRP APP Strapi n8n - Acompanhamento do Tempo em São Paulo e Rio de Janeiro

## Sobre o Projeto

Este projeto foi desenvolvido para monitorar as condições climáticas das cidades de São Paulo e Rio de Janeiro, armazenando as previsões duas vezes ao dia e fornecendo uma análise sobre a viabilidade de visitar a praia no Rio de Janeiro no próximo final de semana. A solução utiliza **Strapi** como backend para armazenar os dados climáticos e **n8n** para automação das tarefas.

A previsão do tempo é obtida da API do [Weatherstack](https://weatherstack.com/), e os dados são salvos no banco de dados do Strapi. Além disso, a automação será responsável por processar e enviar notificações.

## Tecnologias Utilizadas

- **Strapi**: Sistema de gerenciamento de conteúdo (CMS) headless para armazenar dados das previsões.
- **n8n**: Ferramenta de automação para integrar o Strapi com a API do Weatherstack e enviar notificações.
- **Weatherstack API**: Para obter previsões do tempo para São Paulo e Rio de Janeiro.
- **Docker & Docker Compose**: Para facilitar a execução e deployment da aplicação em ambientes locais e de produção.

## Funcionalidades

- **Monitoramento Climático**: Consultas da previsão do tempo para São Paulo e Rio de Janeiro duas vezes ao dia (10:00 e 16:00).
- **Armazenamento de Dados**: As previsões são armazenadas no Strapi em uma estrutura de banco de dados otimizada.
- **Cálculo de Viabilidade para a Praia**: Com base nas condições climáticas, calcula-se se será um bom dia para ir à praia no Rio de Janeiro no próximo final de semana.
- **Notificação**: Envio de notificações para o usuário com base nas previsões do tempo.

## Estrutura do Banco de Dados no Strapi

O banco de dados do Strapi foi modelado para armazenar as previsões climáticas de São Paulo e Rio de Janeiro. Cada previsão contém os seguintes campos:

- **Cidade**: Nome da cidade (São Paulo ou Rio de Janeiro).
- **Data e Hora**: Quando a previsão foi obtida.
- **Temperatura Máxima e Mínima**: Temperaturas esperadas.
- **Condições Climáticas**: Descrição das condições do tempo.

## Como Rodar a Aplicação

### Pré-Requisitos

- Docker e Docker Compose instalados.
- Conta na API do [Weatherstack](https://weatherstack.com/) para obter a chave de API.

### Passos

1. **Clone o repositório**:
   ```bash
    git clone https://github.com/leonardo-spy/ZRP-Weather-Strapi-N8N-APP.git
    cd ZRP-Weather-Strapi-N8N-APP
    ```
2. **Copie e configure o arquivo `.env` com as variáveis necessárias**:  
    ```bash
    cp .env.example .env
    cp ./strapi-app/.env.example ./strapi-app/.env
    ```

3. **Configure as variáveis de ambiente**:
   No arquivo `.env`, adicione sua chave da API do Weatherstack (se diferente), e altere o destino de e-mail para notificação (necessário):
   ```bash
   N8N_TOKEN_WEATHERSTACK=<SUA_CHAVE_AQUI>
   N8N_MAIL_TO=<SEU_EMAIL_AQUI>
   ```

4. **Validando Bancos**:
   Caso não queira subir o Strapi com informações précarregadas, basta deletar o banco do strapi:
   ```bash
   rm ./data/data.db
   ```
   Caso não queira também as informações pré-carregadas do n8n, delete em:
   ```bash
   rm ./n8n-data/database.sqlite
   ```

5. **Suba os containers com Docker Compose (se estiver rodando dentro de sistema Linux, o compose é `docker compose` ao inves de `docker-compose`)**:
   ```bash
   docker-compose up -d --build
   ```

6. **Acesse o painel administrativo do Strapi**:
   O painel estará disponível em `http://localhost:1337/admin`. Se não carregou o banco do strapi, preencha as informações de cadastro! As informações de login do banco pré-carregado:
   ```bash
   login: zrp@exemplo.com
   senha: Senha@zrp1
   ```

7.**Alteração de Token Strapi (Não necessário se não deletou o banco do Strapi)**:
   Caso você optou por não subir os dado pré-carregados do Strapi, Acesse: **Configurações** > **API Tokens** > **Full Access** > **View Token**. E então altere o `.env` como:
   ```bash
   N8N_TOKEN_STRAPI_FULL_ACCESS=<SEU_NOVO_TOKEN_AQUI>
   ```
   Depois reinicie a aplicação:
   ```bash
   docker-compose down
   docker-compose up -d --build
   ```

8. **Configure a automação com n8n**:
   O n8n poderá ser acessado em `http://localhost:5678/`. Caso optou por não subir os dados pré-carregado do n8n, configure a introdução do n8n e importe o fluxo com o arquivo `ZRP.json`! Se subiu os dados pré-carregado, poderá acessar com:
   ```bash
   login: zrp@exemplo.com
   senha: Senha@zrp1
   ```
   E então veja os fluxos que realizam as requisições à API do Weatherstack e salvam os dados no Strapi.

9. **Fluxo diferente com a API PREMIUM do Weatherstack**:
   Você encontrará dois fluxos diferentes, o primeiro contempla a API Free! O segundo fluxo permite visualizar previsões futuras do Tempo...

10. **Aproveite!**
   Realize testes para garantir que as previsões estão sendo registradas corretamente e que as notificações estão sendo enviadas no e-mail.

## Docker Compose

O projeto inclui um arquivo `docker-compose.yml` que configura os serviços necessários:

- **Strapi**: Aplicação de backend para gerenciar e armazenar as previsões climáticas.
- **n8n**: Automação para integrar o Strapi com a API do Weatherstack e enviar notificações.

## Fluxo n8n
![fluxo-n8n](https://github.com/user-attachments/assets/84ac1222-d173-48a8-b4a8-62021c99ffe7)

## Exemplo de E-mail Enviado Após Criação do Pedido

Na finalização de execução do fluxo, o n8n envia automaticamente um e-mail com os informações do tempo.

Segue um exemplo do conteúdo do e-mail recebido com a API Free:<br/>
![email-gratuito](https://github.com/user-attachments/assets/72dc4480-da9c-4539-af20-8defd1ad6aa2)

E um exemplo com a API Premium:<br/>
![email-premium](https://github.com/user-attachments/assets/66577934-1f3b-4fde-bfe3-aec931c6b92f)
>OBS: Esse email foi criado para teste dessa aplicação, após a análise, ele será desativado