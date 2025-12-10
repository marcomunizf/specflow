# 03 — Arquitetura do Sistema

Este documento descreve a **arquitetura do sistema**, apresentando a organização das
camadas, responsabilidades de cada parte, fluxos de comunicação e visão geral da
infraestrutura.

A arquitetura define **como o sistema é estruturado**, **como os componentes se
relacionam** e **onde cada tipo de responsabilidade deve estar**, servindo como base
para a implementação do código e para os agentes de IA envolvidos no desenvolvimento
(Spec-Driven Development e Compounding Engineering).

Este arquivo **não define tecnologias específicas, bibliotecas ou versões**.
Essas decisões estão documentadas no arquivo `techstack.txt`.

---

## Objetivos da Arquitetura

- Garantir separação clara de responsabilidades.
- Facilitar manutenção, testes e evolução do sistema.
- Evitar acoplamento excessivo entre camadas.
- Fornecer um modelo claro para implementação guiada por SPEC.
- Servir como referência para os agentes de **Delegate** e **Assess**.

---

## Visão Geral da Arquitetura

O sistema é dividido em quatro grandes blocos:

- **Front-end**
- **Back-end**
- **Database**
- **Infraestrutura**

A comunicação segue o fluxo geral:

Front-end ⇄ Back-end ⇄ Database

- Front-end e Back-end se comunicam via **HTTP**, utilizando **JSON**.
- Back-end e Database se comunicam via **consultas estruturadas (SQL ou abstrações equivalentes)**.
- A Infraestrutura é responsável por isolar e organizar os serviços.

---

## Arquitetura do Front-end (Conceitual)

Responsabilidades do Front-end:

- Apresentar a interface ao usuário.
- Capturar interações do usuário.
- Consumir a API do Back-end por meio de requisições HTTP.
- Enviar e receber dados no formato JSON.
- Gerenciar estado da interface e navegação.

O Front-end **não deve conter regras de negócio críticas** nem realizar acesso direto
ao banco de dados.

---

## Arquitetura do Back-end

O Back-end é responsável por:

- Expor uma API HTTP.
- Implementar regras de negócio.
- Validar dados de entrada.
- Controlar autenticação e autorização.
- Orquestrar o acesso ao banco de dados.

### Organização em Camadas

- `/routes` – definição das rotas HTTP.
- `/controllers` – recebem requisições e retornam JSON.
- `/services` – regras de negócio.
- `/repositories` – acesso ao banco de dados.
- `/middlewares` – autenticação, autorização, logs e erros.
- `/utils` – funções auxiliares.
- `/config` – configurações da aplicação.
- `app.(js|ts)` e `server.(js|ts)` – inicialização do servidor.

---

## Fluxo de uma Requisição

Request → Route → Controller → Service → Repository → Database  
Database → Repository → Service → Controller → Response (JSON)

---

## Infraestrutura (Visão Conceitual)

A infraestrutura é responsável por:

- Isolar serviços.
- Facilitar execução local e em produção.
- Garantir previsibilidade do ambiente.

---

## O que NÃO é responsabilidade deste arquivo

Este documento **não define** bibliotecas, frameworks, versões ou comandos de execução.
Essas decisões pertencem ao arquivo `techstack.txt`.
