# 🛡️ Anthem – Windows Exploitation Writeup

Este repositório apresenta uma análise completa da máquina **Anthem** (TryHackMe), com uma abordagem estruturada e orientada a raciocínio técnico.
O objetivo é demonstrar não apenas **o que foi feito**, mas **por que cada decisão foi tomada**, evidenciando pensamento crítico — algo valorizado por recrutadores técnicos.

---

## 🎯 Objetivos Técnicos Demonstrados

* Enumeração ativa e passiva
* Web reconnaissance (manual + automatizado)
* Uso de ferramentas (Nmap, Gobuster, Feroxbuster)
* OSINT aplicado à exploração
* Exploração de credenciais
* Acesso remoto via RDP
* Enumeração local em Windows
* Escalação de privilégios via permissões

---

## 🔍 Fase 1 – Enumeração Inicial

### 🔧 Nmap Scan

```bash id="nmapscan01"
nmap -A -p- <IP>
```

### 🔎 Portas relevantes identificadas:

| Porta | Serviço | Interpretação               |
| ----- | ------- | --------------------------- |
| 80    | HTTP    | Superfície web exposta      |
| 3389  | RDP     | Vetor de acesso remoto      |
| 445   | SMB     | Potencial enumeração futura |

➡️ **Insight:** Mesmo com poucos serviços, já temos dois vetores fortes: Web + RDP.

---

### 🔎 Enumeração de diretórios

```bash id="dirscan01"
gobuster dir -u http://<IP>/ -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt
```

Alternativa utilizada:

```bash id="ferox01"
feroxbuster -u http://<IP>/
```

➡️ **Objetivo:** Encontrar endpoints ocultos (ex: admin panels, CMS, backups)

---

## 🌐 Fase 2 – Reconhecimento Web

### 📄 Análise do conteúdo

O site apresenta:

* Blogs
* Categorias
* Tags
* Conteúdo aparentemente legítimo

➡️ **Insight:** Ambientes com conteúdo “realista” frequentemente escondem pistas (CTF design).

---

### 🤖 robots.txt

```id="robots01"
http://<IP>/robots.txt
```

Revela:

```id="robots02"
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

➡️ **Conclusões:**

* Diretórios sensíveis expostos
* Indicação direta do CMS

---

### 🧩 Identificação do CMS

➡️ CMS identificado: **Umbraco**

Painel encontrado:

```id="umbraco01"
http://<IP>/umbraco/#/login
```

[INSERIR IMAGEM: tela de login do Umbraco]

---

## 🕵️ Fase 3 – OSINT e Enumeração de Identidade

### 📧 Informações encontradas

* Email: `jd@anthem.com`
* Domínio: `anthem.com`

➡️ **Padrão identificado:**

```id="pattern01"
<iniciais>@anthem.com
```

---

### 🧠 Descoberta do Administrador

Um poema encontrado no site contém a palavra “admin”.

[INSERIR IMAGEM: poema encontrado]

➡️ Estratégia:

1. Copiar o poema
2. Pesquisar no Google

➡️ Resultado:

* Referência a **Solomon Grundy**

---

### 🔐 Credenciais derivadas

* Nome: Solomon Grundy
* Iniciais: SG

➡️ Email inferido:

```id="email01"
sg@anthem.com
```

➡️ Credenciais completas:

```id="creds01"
Usuário: sg@anthem.com
Senha: UmbracoIsTheBest!
```

---

## 🚩 Fase 4 – Captura de Flags

### 📍 Localizações

* `/authors` → Flag 1
* Homepage (source code) → Flag 2
* Blog posts → Flags 3 e 4

[INSERIR IMAGEM: flags no código fonte]

[INSERIR IMAGEM: flags em páginas de blog]

➡️ **Insight:** Sempre verificar:

* Código-fonte
* Perfis
* Conteúdo dinâmico

---

## 🧪 Fase 5 – Exploração do CMS

Login realizado em:

```id="umbraco02"
/umbraco/#/login
```

➡️ Resultado:

* Acesso válido
* Nenhuma funcionalidade explorável relevante

➡️ **Decisão técnica:** Pivotar para outro vetor (RDP)

---

## 💻 Fase 6 – Acesso Inicial via RDP

```bash id="rdp01"
xfreerdp /f /u:SG /p:UmbracoIsTheBest! /v:<IP>
```

Alternativa:

```bash id="rdp02"
rdesktop <IP>
```

➡️ **Insight:** Quando Web não gera shell → testar serviços expostos

---

## 📂 Fase 7 – Enumeração Local (Windows)

### 🔎 Passos realizados

1. Abrir File Explorer
2. Navegar até `C:\`
3. Ativar **Hidden Files**

[INSERIR IMAGEM: ativando arquivos ocultos]

---

### 📁 Diretório encontrado

```id="backup01"
C:\backup
```

[INSERIR IMAGEM: pasta backup]

Arquivo:

```id="restore01"
restore
```

---

## 🔓 Fase 8 – Escalação de Privilégios

### 🚫 Problema

Arquivo sem permissão de acesso

---

### 🛠️ Solução

Modificar permissões:

1. Botão direito → Properties
2. Aba Security
3. Edit → Add
4. Adicionar usuário **SG**
5. Aplicar permissões

[INSERIR IMAGEM: alteração de permissões]

---

### 🔑 Credencial obtida

```id="admincred01"
Usuário: Administrator
Senha: ChangeMeBaby1MoreTime
```

---

## 👑 Fase 9 – Acesso Root

```id="rootpath01"
C:\Users\Administrator\Desktop
```

Arquivo:

```id="rootflag01"
root.txt
```

➡️ Flag final capturada com sucesso.

---

## 🧠 Lições Aprendidas

* Enumeração é o passo mais crítico em qualquer ataque
* **robots.txt** pode revelar informações sensíveis
* OSINT pode ser decisivo (ex: identificação de usuários)
* Padrões de email são exploráveis
* Nem todo acesso válido (CMS) leva à exploração direta
* RDP é um vetor essencial em ambientes Windows
* Permissões mal configuradas são vulnerabilidades comuns

---

## 📊 Diferenciais Técnicos Demonstrados

Este projeto evidencia:

* Capacidade de correlação de informações (OSINT + Web)
* Tomada de decisão baseada em contexto
* Adaptação de estratégia (pivoting)
* Conhecimento prático de ambiente Windows
* Metodologia estruturada de exploração

---

## 📌 Considerações Finais

Este writeup foi estruturado com foco em:

✔ Clareza técnica
✔ Organização lógica
✔ Raciocínio documentado
✔ Reprodutibilidade

Ideal para demonstrar habilidades em:

* Pentest
* Red Team
* Cybersecurity Analysis

---

## 📎 Observação

As imagens devem ser adicionadas nos pontos indicados para enriquecer a evidência visual do processo.


Só me dizer 👍
