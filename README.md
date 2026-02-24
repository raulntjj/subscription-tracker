# Subscription Tracker

O **Subscription Tracker** é uma plataforma robusta desenvolvida para simplificar a governança, o monitoramento e o rastreio de assinaturas (SaaS, serviços, recorrências) da sua empresa ou uso pessoal. 

O foco central do produto é trazer previsibilidade de gastos e resiliência na notificação de renovações. Com uma infraestrutura distribuída, monitoramento contínuo e envios de eventos via Webhooks, o sistema se adapta a regras de cobrança complexas garantindo que você nunca seja pego de surpresa por renovações indesejadas.

## Estrutura do Ecossistema

Esta plataforma é concebida sobre uma arquitetura distribuída, dividindo responsabilidades claras em repositórios/diretórios separados:

* **Backend (`/backend`):** Núcleo da inteligência de negócios. Uma API de alta performance escrita em PHP (Swoole) utilizando Domain-Driven Design (DDD). Cuida da segurança dos dados (JWT), persistência em banco de dados relacional e processamento assíncrono rigoroso usando mensageria.
* **Frontend (`/frontend`):** Interface web cliente ("Single Page Application" com Next.js). Entrega uma experiência de usuário (UX) premium, fluida e reativa onde os gestores governam as assinaturas, visualizam métricas financeiras e configuram integrações.
* **Ops / Infraestrutura (`/ops`):** Motor de orquestração do ecossistema. Mantém todas as configurações estruturais para executar a aplicação via containers, incluindo os serviços de mensageria (RabbitMQ).

## Como Inicializar e Executar (Build Geral)

Pensando na esteira de desenvolvimento e simulando um ambiente produtivo padronizado, o projeto raiz centraliza a orquestração via **Docker Compose**. 

Não é necessário instalar PHP, RabbitMQ, ou Node.js e NPM nativamente na sua máquina se você usar este fluxo; apenas o **Docker** é requerido.

### Passo 1: Preparando a infraestrutura

Na raiz do projeto, acesse a pasta de operações e suba todo o ecossistema:

```bash
cd ops/development
docker-compose up -d --build
```
*Este comando construirá as imagens para o Frontend e para o Backend, além de baixar as imagens de serviços necessários.*

### Passo 2: Acessando os Projetos

Com os containers em execução, os serviços ficarão expostos nas seguintes portas mapeadas do `localhost`:

* **Interface de Usuário (Frontend):** `http://localhost:3000`
* **API Principal (Backend):** `http://localhost:8001` (com porta interna 8000 mapeada, conforme o compose)
* **Banco de Dados Relacional:** Acessível localmente na porta `3307`
* **RabbitMQ (Painel de gerência):** `http://localhost:15672` (Usuário de rabbitmq padrão fica acessível no .env)

### Passo 3: Comandos Administrativos (Backend)

O backend precisa ter suas dependências configuradas na primeira execução. Você pode fazer isso executando os comandos diretamente no container da API usando o gerenciador centralizador:

```bash
docker exec -it subs-tracker-backend composer install
docker exec -it subs-tracker-backend php artisan migrate
docker exec -it php artisan key:generate --show
docker exec -it php artisan jwt:secret --show
```
*(Ou, equivalentemente, usando o script `./backend-exec` fornecido).*

---

A modularidade deste repositório garante que o ciclo de desenvolvimento das interfaces de tela (Frontend) seja independente do motor de regras de domínio (Backend), enquanto a orquestração do Docker Compose do projeto une todas as peças harmonicamente em seu ambiente de desenvolvimento ou homologação.
