# 🛡️ Anthem – Windows Exploitation Writeup

Este repositório documenta a exploração da máquina **Anthem**, proposta pela plataforma TryHackMe. O objetivo deste material é demonstrar, de forma clara e profissional, o processo completo de comprometimento de um ambiente Windows, evidenciando habilidades práticas relevantes para segurança ofensiva.

---

## 🎯 Objetivos Técnicos Demonstrados

* Enumeração de serviços e aplicações
* Análise de aplicações web
* Uso de OSINT (Open Source Intelligence)
* Exploração de credenciais
* Acesso remoto via RDP
* Escalação de privilégios em ambiente Windows
* Manipulação de permissões de arquivos

---

## 🔍 Fase 1 – Enumeração

A enumeração foi conduzida utilizando **Nmap** para identificar serviços ativos e possíveis vetores de ataque.

```bash
nmap -A <IP>
```

### 🔎 Resultados relevantes:

| Porta   | Serviço     | Observação               |
| ------- | ----------- | ------------------------ |
| 80      | HTTP        | Aplicação web ativa      |
| 3389    | RDP         | Possível acesso remoto   |
| 445     | SMB         | Compartilhamento Windows |
| 135/139 | RPC/NetBIOS | Comunicação interna      |

---

## 🌐 Fase 2 – Análise Web

### 📁 robots.txt

A análise do arquivo revelou diretórios sensíveis e uma possível string utilizada como senha.

```
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

➡️ **Insight:** Informações em `robots.txt` frequentemente expõem caminhos sensíveis.

---

### 🧩 Identificação do CMS

O diretório `/umbraco` revelou que a aplicação utiliza o CMS **Umbraco**.

---

### 🕵️ OSINT e Enumeração de Usuários

Através da análise de conteúdo:

* Email identificado: `JD@anthem.com`
* Nome encontrado: John Doe (não administrador)

Um artigo contendo um poema levou à identificação do administrador via OSINT.

➡️ **Técnica aplicada:** correlação de conteúdo + pesquisa externa

---

### 📧 Padrão de Email

Com base no padrão identificado:

```
<iniciais>@anthem.com
```

Foi possível inferir o email do administrador.

---

## 🚩 Fase 3 – Captura de Flags

As flags foram encontradas através de:

* Inspeção de código-fonte
* Perfis de usuários
* Conteúdo oculto em páginas

### 📸 Evidências

[INSERIR IMAGEM: código-fonte com flags]

[INSERIR IMAGEM: perfil de usuário com flag]

[INSERIR IMAGEM: código-fonte adicional]

---

## 💻 Fase 4 – Acesso Inicial

### 🔐 Acesso via RDP

Com credenciais obtidas:

```bash
xfreerdp /f /u:USUARIO /p:SENHA /v:<IP>
```

Caso necessário:

* `/cert-ignore`
* `-sec-nla`

➡️ **Decisão técnica:** uso de RDP devido ausência de SSH

---

## 📂 Fase 5 – Enumeração Local

Após acesso:

* Identificação de arquivos sensíveis
* Busca por credenciais armazenadas

### 🔎 Diretório relevante:

```
C:\backup
```

[INSERIR IMAGEM: pasta backup oculta]

Arquivo encontrado:

```
restore
```

---

## 🔓 Fase 6 – Escalação de Privilégios

O arquivo `restore` possuía restrições de acesso.

### 🛠️ Técnica utilizada:

Alteração de permissões via GUI:

1. Propriedades do arquivo
2. Aba "Segurança"
3. Adicionar usuário atual
4. Conceder acesso

[INSERIR IMAGEM: alteração de permissões]

➡️ **Resultado:** acesso à senha do administrador

---

## 👑 Fase 7 – Acesso Administrativo

Com a senha:

```
C:\Users\Administrator\Desktop
```

Arquivo final:

```
root.txt
```

---

## 🧠 Lições Aprendidas

* Enumeração é a base de qualquer exploração bem-sucedida
* Pequenos vazamentos (como `robots.txt`) podem ser críticos
* OSINT pode revelar informações que não estão diretamente no alvo
* Ambientes Windows frequentemente possuem falhas de permissões exploráveis
* RDP é um vetor importante quando SSH não está disponível

---

## 📌 Considerações Finais

Este projeto demonstra uma abordagem estruturada e prática para exploração de ambientes Windows, destacando competências essenciais para atuação em:

* Pentest
* Red Team
* Cybersecurity Analyst

---

## 📎 Observação

As imagens foram omitidas propositalmente e devem ser adicionadas posteriormente nos pontos indicados para complementar a documentação visual.

---
