# Anthem

Esta sala é baseada na exploração de uma máquina Windows. Mas, no caminho, também será necessário passar por muitas outras coisas. A coisa mais importante exigida para esta sala é a enumeração. Em cada estágio desta máquina, a enumeração desempenha um papel importante para obter as respostas desejadas. Outra coisa que eu realmente gostei na sala foi a necessidade de OSINT, o que ajuda a encontrar muitas informações úteis.

Sem mais delongas, vamos fazer o deploy da máquina e começar a sala\!

### Enumeração

#### [Tarefa 1] Análise do Site

1.  ##### Vamos rodar o nmap e checar quais portas estão abertas.

<!-- end list -->

```text
┌─[tester@parrot-virtual]─[~/Downloads/anthem]
└──╼ $nmap -A 10.10.63.79
Starting Nmap 7.80 ( https://nmap.org ) at 2020-09-24 20:58 IST
Nmap scan report for 10.10.63.79
Host is up (0.18s latency).
Not shown: 995 closed ports
PORT     STATE SERVICE       VERSION
80/tcp   open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
3389/tcp open  ms-wbt-server Microsoft Terminal Services
| rdp-ntlm-info: 
|   Target_Name: WIN-LU09299160F
|   NetBIOS_Domain_Name: WIN-LU09299160F
|   NetBIOS_Computer_Name: WIN-LU09299160F
|   DNS_Domain_Name: WIN-LU09299160F
|   DNS_Computer_Name: WIN-LU09299160F
|   Product_Version: 10.0.17763
|_  System_Time: 2020-09-24T15:29:45+00:00
| ssl-cert: Subject: commonName=WIN-LU09299160F
| Not valid before: 2020-09-23T15:13:52
|_Not valid after:  2021-03-25T15:13:52
|_ssl-date: 2020-09-24T15:30:38+00:00; -1s from scanner time.
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
|_clock-skew: mean: -1s, deviation: 0s, median: -1s
| smb2-security-mode: 
|   2.02: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2020-09-24T15:29:45
|_  start_date: N/A

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 137.63 seconds
```

2.  ##### Qual porta é do servidor web?

Pelos resultados do nmap, podemos ver a porta em que o serviço HTTP está rodando e enviar a mesma como resposta para esta pergunta.

3.  ##### Qual porta é do serviço de remote desktop (área de trabalho remota)?

A princípio, no scan do nmap não conseguimos encontrar nenhum serviço com o nome de *remote desktop*. Mas depois, pesquisando um pouco, podemos descobrir que o `Microsoft Terminal Services` é, na verdade, um serviço de área de trabalho remota. Portanto, a porta associada pode ser enviada como resposta para esta pergunta.

4.  ##### Qual é uma possível senha em uma das páginas que os web crawlers verificam?

Agora, se entendermos o que são *web crawlers*, podemos perceber imediatamente que esta pergunta está falando sobre o arquivo `robots.txt`. Então, podemos visitar rapidamente `<IP_da_maquina>/robots.txt` e verificar o seu conteúdo.

```text
******************

# Use for all search robots
User-agent: *

# Define the directories not to crawl
Disallow: /bin/
Disallow: /config/
Disallow: /umbraco/
Disallow: /umbraco_client/
```

No topo do arquivo `robots.txt`, podemos ver uma string irrelevante que se parece muito com uma senha. Então, podemos tentar enviar essa string como resposta para esta pergunta.

5.  ##### Qual CMS o site está usando?

Se navegarmos por todos os diretórios bloqueados no `robots.txt`, encontraremos uma página de login no diretório `/umbraco`. Geralmente, esse tipo de sistema de login é encontrado em vários serviços web em si. Então, podemos pesquisar no Google pelo termo `umbraco` para verificar se é algum serviço web, e conseguimos alguns detalhes confirmando que se trata de um CMS. Com isso, também temos a resposta para esta pergunta.

6.  ##### Qual é o domínio do site?

Quando visitamos o endereço IP da máquina `http://<IP_da_maquina>`, somos levados à página inicial. Lá, podemos encontrar o nome de domínio do site bem na nossa frente.

7.  ##### Qual é o nome do Administrador?

Esta pergunta envolve um pouco de enumeração, bem como OSINT. Até agora, sabemos sobre a página de login e a página inicial. Não conseguimos obter nenhum detalhe útil na página de login, nem mesmo em seu código-fonte. Portanto, nos resta a página inicial. Podemos ler os artigos presentes lá e procurar qualquer informação útil. No artigo intitulado `We are hiring` (Estamos contratando), podemos encontrar um e-mail `JD@anthem.com` e o nome `John Doe`, mas ele é o autor e não o administrador.

Avançando para o outro artigo chamado `A cheers to our IT department` (Um brinde ao nosso departamento de TI), podemos encontrar o nome do autor do artigo, mas ele também não é o administrador. Lendo o artigo, podemos ver que o poema foi escrito para o administrador, mas o nome dele não é mencionado em nenhum lugar.

Podemos pesquisar rapidamente esse poema no Google e verificar se conseguimos algo de lá. A partir dos resultados, podemos descobrir o nome da pessoa para quem esse poema foi escrito. Assim, podemos presumir que esse também seja o nome do nosso administrador e tentar enviá-lo como resposta para a sétima pergunta.

8.  ##### Conseguimos encontrar o endereço de e-mail do administrador?

Novamente essa pergunta tem uma pequena pegadinha, já que não conseguimos encontrar os detalhes do administrador diretamente no site. Mas sabemos que muitas organizações usam um padrão específico para endereços de e-mail e nomes de usuário de seus funcionários. E na última pergunta, nos deparamos com o e-mail `JD@anthem.com`. Então, podemos tentar algo semelhante para o e-mail do administrador, usando as iniciais do seu primeiro e último nome junto com `@anthem.com` e enviar isso como resposta para a oitava pergunta.

#### [Tarefa 2] Encontre as flags (Spot the flags)

Nesta tarefa, precisamos apenas encontrar diferentes flags que estão escondidas pelo site e enviá-las.

1.  ##### Qual é a flag 1?

Para iniciar nossa busca, podemos começar com o primeiro artigo chamado `We are hiring` e procurar qualquer coisa que se pareça com uma flag em seu código-fonte.

E podemos encontrar duas flags aqui. A primeira e também a segunda.

2.  ##### Qual é a flag 2?

Já encontrada acima.

3.  ##### Qual é a flag 3?

Na página do mesmo artigo, podemos ver que o perfil do autor pode ser aberto. E quando clicamos nele, temos a nossa terceira flag bem na nossa frente\!

4.  #### Qual é a flag 4?

Podemos ir para o segundo artigo no site e verificar o código-fonte dele também.

E temos a quarta flag também.

NOTA: Pode ser que você não encontre todas as flags na sequência exata. Eu mesmo encontrei algumas durante a minha enumeração inicial e outras um pouco mais tarde.

### Ponto de Apoio Inicial (Initial Foothold)

1.  ##### Vamos descobrir o nome de usuário e a senha para logar na máquina.

Normalmente, a porta 22 está aberta nas máquinas, então tentamos fazer o login via SSH, mas aqui a porta 22 não está aberta. Em vez disso, sabemos que o RDP está disponível. Portanto, podemos acessar a máquina diretamente por RDP para obter o acesso inicial.

Nós já temos o endereço de e-mail e a senha do administrador do CMS das perguntas anteriores que resolvemos. Mas, para obter acesso à estação de trabalho (workstation), precisamos do usuário e da senha do sistema operacional. Podemos tentar logar usando as mesmas credenciais na máquina, mas elas não funcionam. Além disso, podemos notar que o `@anthem.com` está ali por causa do nome de domínio do site. Então, talvez possamos tentar fazer o login usando o nome de usuário sem a parte do domínio e a mesma senha.

Podemos acessar a máquina via RDP usando o comando:

```text
xfreerdp /f /u:** /p:****************** /v:<IP_da_maquina>
```

Aqui, `/f` é para iniciar a sessão RDP em tela cheia, `/u` é para o nome de usuário (username), `/p` é para a senha (password) e `/v` é para o endereço IP da máquina remota. Além disso, se você enfrentar problemas relacionados ao NLA, adicione `-sec-nla` ou `/cert-ignore` e então tente acessar a máquina novamente.

Com isso, conseguimos entrar na estação de trabalho.

2.  ##### Obtenha acesso inicial à máquina, qual é o conteúdo do user.txt?

Podemos ver o arquivo `user.txt` bem na nossa frente no Desktop (Área de Trabalho), de onde podemos pegar a flag do usuário.

3.  ##### Conseguimos encontrar a senha do admin?

Para esta pergunta, precisamos enumerar a máquina e, pela própria pergunta, podemos adivinhar que a senha do administrador está armazenada em algum arquivo.

Podemos vasculhar várias pastas, como Documents, Pictures, Videos, Desktop, etc. Certifique-se também de ativar a opção `Show the Hidden Files` (Mostrar Arquivos Ocultos), pois o arquivo contendo a senha pode estar oculto.

Navegando no disco C:, podemos ver uma pasta oculta chamada `backup`. E nessa pasta, há um arquivo chamado `restore`, mas não temos acesso a esse arquivo. Então, podemos tentar alterar as permissões dele assim:

1.  Clique com o botão direito e selecione Propriedades (*Properties*).
2.  Vá até a aba Segurança (*Security*) e clique em Editar (*Edit*).
3.  Uma nova janela de permissões se abrirá, então clique em Adicionar (*Add*).
4.  Novamente, uma nova janela para Selecionar Usuários ou Grupos será aberta. Nela, precisamos adicionar o nome do usuário com o qual estamos logados atualmente. Também podemos verificar o nome de usuário usando o botão Verificar Nome (*Check Name*) na mesma janela.
5.  Aplique as mudanças.

Uma vez feito isso, agora podemos abrir o arquivo `restore` e encontrar a senha do Administrador.

4.  ##### Escale seus privilégios para root, qual é o conteúdo de root.txt?

Agora, temos a senha do Administrador. Então, podemos ir até `C:\Users\Administrator\Desktop` e, quando for solicitada a senha do administrador, inserimos a senha que acabamos de obter. Uma vez na pasta `Desktop`, podemos abrir o arquivo `root.txt` e pegar nossa flag.

Com isso, completamos a sala `Anthem`\!

## Alguns Pontos Chave para Lembrar (Key Takeaways)

1.  Se a porta 22 não estiver aberta, procure por alguma outra porta através da qual possamos obter acesso à máquina.
2.  Se você não conseguir se conectar por RDP usando o `xfreerdp`, adicione `/cert-ignore` ou `-sec-nla` ao comando e tente estabelecer a sessão RDP novamente.
3.  Em máquinas Windows, tente obter acesso a arquivos inacessíveis adicionando o seu usuário à lista de usuários permitidos nas Propriedades (aba de Segurança) daquele arquivo específico.
