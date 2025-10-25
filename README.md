# 🤖 Triagem e Resposta Automática de E-mails com IA (Make.com)

![Make.com](https://img.shields.io/badge/Plataforma-Make.com-blue?style=for-the-badge&logo=make)

Este projeto é um cenário de automação avançado construído na plataforma Make.com, projetado para automatizar o atendimento ao cliente por e-mail para um banco fictício, o "Bytebank".

O sistema monitora uma caixa de entrada do Outlook, utiliza a API do Groq com o modelo Llama 3 para classificar e entender as solicitações dos clientes, consulta uma base de conhecimento (FAQ) em um arquivo Excel e, em seguida, decide autonomamente entre:

1.  Gerar e enviar uma resposta automática (para dúvidas comuns).
2.  Encaminhar o e-mail para uma equipe de suporte humana (para dúvidas complexas).
3.  Arquivar o e-mail (se não for uma dúvida).

## ✨ Funcionalidades Principais

* **Triagem Inteligente (Nível 1):** Classifica automaticamente os e-mails recebidos em `"dúvida"` ou `"não dúvida"` usando IA.
* **Consulta à Base de Conhecimento:** Lê dinamicamente um arquivo `Perguntas Frequentes - Bytebank.xlsx` no Microsoft 365 para buscar respostas.
* **Análise de Dúvida (Nível 2):** Compara a dúvida do cliente com o FAQ e determina se a resposta está contida nele (`"dúvida FAQ"`) ou se requer intervenção humana (`"dúvida - time C"`).
* **Geração de Resposta por IA:** Utiliza o Groq (Llama 3) para redigir respostas cordiais, claras e formatadas em HTML para o cliente, com base nas informações do FAQ.
* **Roteamento Automático:** Move os e-mails entre pastas do Outlook (`Processados`, `Time C`, `Respondido pela IA`) com base na classificação da IA, mantendo a caixa de entrada principal limpa.

##  Exemplo Com Imagem

[exemplo](assets\automação-email-make)

##  workflow Fluxo da Automação

O cenário é executado em uma sequência lógica de 13 passos principais:

1.  **`[Outlook]` Listar E-mails:** Monitora a "Caixa de Entrada" por novos e-mails (configurado para processar 1 por vez).
2.  **`[Regexp]` HTML para Texto:** Converte o corpo do e-mail de HTML para texto puro para facilitar a análise pela IA.
3.  **`[Groq]` Classificar E-mail (Nível 1):** Envia o texto do e-mail para a IA (Llama 3) para classificar como `"dúvida"` ou `"não dúvida"`.
4.  **`[Router]` Roteador 1:** Divide o fluxo com base na classificação anterior.
    * **Rota A (`não dúvida`):**
5.  **`[Outlook]` Mover E-mail:** Move o e-mail para uma pasta de "Processados" e encerra o fluxo.
    * **Rota B (`dúvida`):**
6.  **`[Excel]` Listar Linhas da Planilha:** Acessa o arquivo `Perguntas Frequentes - Bytebank.xlsx` no OneDrive e lê todas as linhas (Perguntas e Respostas).
7.  **`[Agregador]` Agregar FAQ:** Compila todo o conteúdo do FAQ em um único bloco de texto para ser usado como contexto.
8.  **`[Groq]` Classificar Dúvida (Nível 2):** Envia a dúvida do cliente e o FAQ completo para a IA (Llama 3) classificar como `"dúvida FAQ"` ou `"dúvida - time C"`.
9.  **`[Router]` Roteador 2:** Divide o fluxo novamente.
    * **Rota A (`dúvida - time C`):**
10. **`[Outlook]` Mover E-mail:** Move o e-mail para a pasta do "Time C" (atendimento humano) e encerra o fluxo.
    * **Rota B (`dúvida FAQ`):**
11. **`[Groq]` Gerar Resposta:** Envia a dúvida e o FAQ para a IA (Llama 3) com um prompt para gerar uma resposta em HTML, cordial e completa, para o cliente.
12. **`[Outlook]` Criar e Enviar E-mail:** Envia a resposta gerada pela IA para o cliente (com o assunto `RE: [Assunto Original]`).
13. **`[Outlook]` Mover E-mail:** Move o e-mail original do cliente para a pasta "Respondido pela IA".

## 🛠️ Tecnologias Utilizadas

* **Plataforma de Automação:** [Make.com](https://www.make.com/)
* **Serviços de E-mail e Arquivos:** [Microsoft 365](https://www.microsoft.com/pt-br/microsoft-365) (Outlook e Excel)
* **Inteligência Artificial (LLM):** [Groq](https://groq.com/)
* **Modelo de IA:** `llama-3.3-70b-versatile`
* **Módulos Nativos:** HTML to Text, Text Aggregator, Router.

## 🚀 Instalação e Configuração

Para utilizar este blueprint no seu próprio ambiente Make.com, siga os passos abaixo:

1.  **Baixar o Blueprint:** Faça o download do arquivo `Integration Microsoft 365 Email (Outlook).blueprint.json` deste repositório.
2.  **Importar no Make.com:**
    * Faça login na sua conta Make.com.
    * Vá para a seção "Scenarios" (Cenários).
    * Clique em "More" (Mais) no canto superior direito e selecione "Import blueprint" (Importar blueprint).
    * Faça o upload do arquivo `.json`.
3.  **Preparar o Excel (FAQ):**
    * Crie um arquivo Excel no seu OneDrive chamado `Perguntas Frequentes - Bytebank.xlsx`.
    * Na `Planilha1`, formate-o com duas colunas:
        * **Coluna A:** Perguntas (ex: "Como alterar minha senha?")
        * **Coluna B:** Respostas (ex: "Você pode alterar sua senha através do app...")
4.  **Reconfigurar Conexões (Obrigatório):**
    * **Microsoft 365 (Outlook & Excel):** Clique em cada módulo da Microsoft (ícones azuis) e reautorize a conexão com a sua própria conta.
    * **Groq:** Clique em cada módulo do Groq (ícones roxos), selecione sua conexão ou crie uma nova inserindo sua Chave de API (API Key) do Groq.
5.  **Atualizar IDs de Pastas e Arquivos (Obrigatório):**
    * Os IDs de pastas e arquivos são únicos para cada conta Microsoft. Você **precisa** atualizá-los manualmente:
    * **Módulo 6 (List Worksheet Rows):** Selecione o seu arquivo `Perguntas Frequentes - Bytebank.xlsx` e a planilha correta.
    * **Módulos de Mover E-mail (5, 10, 13):** Em cada um desses módulos, selecione manualmente as pastas de destino (ex: "Processados", "Time C", "Respondidos") da sua própria conta do Outlook.

Após seguir esses passos, seu cenário estará pronto para ser ativado.
