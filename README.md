# 📌 Aplicativo Bancário: Arquitetura, Fluxo e Modelo de Ameaças  

## Primeira tarefa - Descrição do Sistema e Identificação de Ativos
### *1️⃣ Componentes do Sistema*  

#### *🔹 Browser*  
O *browser* é o canal de comunicação entre os usuários e a aplicação. Através dele, os usuários acessam o *Web Client*, permitindo a realização de operações bancárias online.  

#### *🔹 Web Client*  
O *Web Client* representa a interface gráfica onde os usuários podem consultar saldos, transferir fundos e pagar contas.  

#### *🔹 Virtual Machine*  
A *máquina virtual (VM)* hospeda a aplicação e garante sua execução contínua e segura quando exposta à rede externa.  

#### *🔹 Docker Container*  
O *Docker container* fornece um ambiente isolado e portátil para a aplicação, facilitando a manutenção e a comunicação eficiente entre serviços.  

#### *🔹 Nginx*  
O *Nginx* atua como *proxy reverso* e gerenciador de tráfego HTTPS. Ele:  
- Exige comunicação segura via *TLS/HTTPS*.  
- Configura o *handshake TLS* (certificados, algoritmos e versões suportadas).  
- Encaminha requisições para o *RESTful Web Service*.  

#### *🔹 RESTful Web Service*  
O *RESTful Web Service* é responsável por processar as solicitações do Web Client e interagir com os serviços internos. Ele utiliza *APIs seguras* para comunicação entre os componentes do sistema.  

---

### *2️⃣ Fluxo de Funcionamento do Aplicativo*  

1. *Usuário acessa o Web Client via Browser*  
   - O usuário insere a URL no navegador.  
   - O *Nginx* recebe a requisição, valida os certificados TLS e estabelece uma conexão segura via HTTPS.  

2. *Autenticação e Autorização*  
   - O Web Client solicita credenciais do usuário.  
   - A requisição é enviada ao *RESTful Web Service, que valida os dados e retorna um **token de sessão*.  
   - O token é armazenado no Web Client para requisições futuras.  

3. *Operações Bancárias*  
   - O usuário escolhe uma ação: *consultar saldo, transferir fundos ou pagar contas*.  
   - O Web Client envia a solicitação ao *RESTful Web Service* via API.  
   - O serviço processa a requisição e retorna a resposta.  

4. *Processamento no Backend*  
   - O *RESTful Web Service* roda dentro de um *Docker Container* hospedado em uma *Virtual Machine*.  
   - O Nginx atua como *proxy reverso*, garantindo comunicação segura com o servidor.  

5. *Resposta ao Usuário*  
   - O Web Client exibe os dados na interface gráfica.  
   - Toda a comunicação permanece protegida via TLS/HTTPS.  

---

### *3️⃣ Modelo de Ameaças: Riscos e Mitigações*  

#### *🔴 Possíveis Riscos de Segurança & 🔵 Medidas de Mitigação*  

| *Categoria* | *Risco Identificado* | *Mitigação* |
|--------------|------------------------|---------------|
| *Autenticação* | Ataques de força bruta contra credenciais | Implementar *MFA (Autenticação Multifator)* e bloquear IPs após múltiplas falhas. |
| *Comunicação* | Interceptação de tráfego (Man-in-the-Middle) | Utilizar *TLS 1.2 ou superior, certificados confiáveis e **HSTS (HTTP Strict Transport Security)*. |
| *Gerenciamento de Sessão* | Sequestro de sessão via *XSS ou CSRF* | Usar *SameSite Cookies*, validar tokens CSRF e armazenar sessões no backend. |
| *API Security* | Chamadas não autorizadas a APIs sensíveis | Implementar *OAuth 2.0*, escopos de permissões e logs de acesso. |
| *Armazenamento de Dados* | Vazamento de informações sensíveis no banco | Utilizar *criptografia AES-256* e mascaramento de dados para PII (informações pessoais). |
| *Servidores* | Exploração de vulnerabilidades no Nginx ou Web Service | Aplicar *patches regulares, restringir portas expostas e configurar **firewalls*. |

---

### *4️⃣ Apresentação dos Resultados*  
Os riscos e as medidas de mitigação serão apresentados à *Equipe de Modelagem de Ameaças* e à *Alta Gestão, destacando a importância da **segurança da aplicação bancária* e as recomendações para minimizar vulnerabilidades.  

---

## Segunda tarefa - Fronteiras de confiança 

### 📌 Principais Ativos de Risco  

1. *Credenciais de Usuário* – Login, senhas e tokens de autenticação.  
2. *Sessões de Usuário* – Tokens de acesso e cookies de autenticação.  
3. *Dados Bancários Sensíveis* – Saldos, transações e informações financeiras.  
4. *APIs RESTful* – Endpoints que processam operações bancárias.  
5. *Banco de Dados* – Armazenamento de contas, transações e credenciais.  
6. *Comunicação via HTTPS/TLS* – Transmissão segura entre cliente e servidor.  
7. *Web Client (Frontend)* – Interface onde usuários realizam operações.  
8. *Servidor de Aplicação* – Backend que processa solicitações do Web Client.  
9. *Nginx (Proxy Reverso)* – Gerenciamento de tráfego e segurança TLS.  
10. *Logs e Registros* – Histórico de acesso e atividades para auditoria.

---

## Terceira tarefa - Identificação de Ameaças
### 📌 Modelagem de Ameaças – Aplicativo Bancário (STRIDE)  
Este documento utiliza o modelo *STRIDE* para identificar ameaças nos componentes e fluxos de dados do sistema.  

---

### *1️⃣ STRIDE – Visão Geral*  

| *Categoria* | *Descrição* |
|--------------|--------------|
| *S*poofing (Falsificação) | Um atacante se passa por outro usuário ou sistema. |
| *T*ampering (Manipulação) | Dados ou código são alterados de forma maliciosa. |
| *R*epudiation (Repúdio) | Ações não podem ser rastreadas, permitindo negação de responsabilidade. |
| *I*nformation Disclosure (Exposição de Informações) | Dados sensíveis são acessados sem autorização. |
| *D*enial of Service (Negação de Serviço) | Recursos são sobrecarregados, tornando o sistema indisponível. |
| *E*levation of Privilege (Elevação de Privilégio) | Um usuário obtém permissões indevidas. |

---

### *2️⃣ Ameaças por Componente e Fluxo de Dados*  

#### *🔹 Browser & Web Client*  
📌 *Fluxo:* O usuário acessa o Web Client para realizar operações bancárias.  

| *Categoria* | *Ameaça* | *Descrição* |
|--------------|-----------|--------------|
| *Spoofing* | Phishing | Um site falso pode roubar credenciais do usuário. |
| *Tampering* | Ataque Man-in-the-Middle (MITM) | Um invasor pode alterar solicitações antes de chegarem ao servidor. |
| *Repudiation* | Falta de logs no Web Client | O usuário pode negar ações realizadas. |
| *Information Disclosure* | Vazamento de dados no frontend | Informações sensíveis podem ser armazenadas no cache do navegador. |
| *Denial of Service* | Exploração de scripts no navegador | Ataques XSS podem sobrecarregar o Web Client. |
| *Elevation of Privilege* | Sequestro de sessão | Cookies não seguros podem permitir que um invasor assuma a identidade do usuário. |

---

#### *🔹 Nginx (Proxy Reverso & TLS)*  
📌 *Fluxo:* O Nginx gerencia o tráfego HTTPS e encaminha requisições ao backend.  

| *Categoria* | *Ameaça* | *Descrição* |
|--------------|-----------|--------------|
| *Spoofing* | TLS downgrade attack | Um atacante pode forçar uma versão insegura do TLS. |
| *Tampering* | Modificação de cabeçalhos HTTP | Cabeçalhos inseguros podem permitir manipulação de tráfego. |
| *Repudiation* | Logs insuficientes | Sem logs adequados, um ataque pode não ser rastreado. |
| *Information Disclosure* | Exposição de certificados inválidos | Certificados TLS comprometidos podem expor tráfego criptografado. |
| *Denial of Service* | Ataque DDoS | Envio massivo de requisições pode derrubar o serviço. |
| *Elevation of Privilege* | Proxy aberto indevido | Configuração errada pode permitir que usuários acessem endpoints internos. |

---

#### *🔹 RESTful Web Service*  
📌 *Fluxo:* O backend processa requisições do Web Client.  

| *Categoria* | *Ameaça* | *Descrição* |
|--------------|-----------|--------------|
| *Spoofing* | Autenticação bypass | Uso de tokens comprometidos pode permitir acesso não autorizado. |
| *Tampering* | Injeção de SQL/NoSQL | Manipulação de consultas pode comprometer dados bancários. |
| *Repudiation* | Falta de auditoria em APIs | Sem logs, ações maliciosas podem passar despercebidas. |
| *Information Disclosure* | Erros detalhados na API | Respostas mal configuradas podem expor informações sensíveis. |
| *Denial of Service* | Ataques de repetição | Um invasor pode enviar requisições em massa para consumir recursos. |
| *Elevation of Privilege* | Acesso indevido a endpoints administrativos | APIs sem restrições podem ser exploradas para elevação de privilégio. |

---

#### *🔹 Banco de Dados PostgreSQL*  
📌 *Fluxo:* Armazena informações bancárias dos usuários.  

| *Categoria* | *Ameaça* | *Descrição* |
|--------------|-----------|--------------|
| *Spoofing* | Uso de credenciais comprometidas | Um invasor pode acessar o banco com credenciais vazadas. |
| *Tampering* | Alteração de saldos e transações | Ataques internos ou injeções podem modificar registros financeiros. |
| *Repudiation* | Falta de logs de auditoria | Sem rastreamento adequado, fraudes podem ser ocultadas. |
| *Information Disclosure* | Dados armazenados sem criptografia | Informações financeiras podem ser expostas em um vazamento. |
| *Denial of Service* | Consultas pesadas e lock de tabelas | Consultas maliciosas podem tornar o banco lento ou indisponível. |
| *Elevation of Privilege* | Usuários com permissões excessivas | Contas administrativas sem controle podem ser exploradas. |

---

### *3️⃣ Mitigações Recomendadas*  

| *Categoria* | *Mitigação* |
|--------------|--------------|
| *Spoofing* | Implementar autenticação multifator (MFA), usar certificados digitais confiáveis. |
| *Tampering* | Validar entradas de usuários, implementar assinaturas digitais em dados sensíveis. |
| *Repudiation* | Habilitar logs detalhados, implementar trilhas de auditoria imutáveis. |
| *Information Disclosure* | Criptografar dados sensíveis (AES-256), mascarar informações em logs. |
| *Denial of Service* | Implementar rate limiting, firewalls e proteção contra DDoS. |
| *Elevation of Privilege* | Restringir permissões mínimas necessárias para cada usuário e serviço. |

---
## Quarta tarefa: Avaliação de Riscos

### *1️⃣ Matriz de Avaliação de Riscos*  

| **Probabilidade \ Impacto** | **Baixo** | **Médio** | **Alto** |
|----------------------------|----------|----------|----------|
| **Baixo** | Baixo | Baixo | Médio |
| **Médio** | Baixo | Médio | Alto |
| **Alto** | Médio | Alto | Alto |

Critérios:  
- **Probabilidade**: Baseada em histórico de ataques, vulnerabilidades conhecidas e exposição do sistema.  
- **Impacto**: Medido pela severidade da falha (acesso não autorizado, perda de dados, downtime crítico).  

---

### *2️⃣ Ameaças por Componente, Classificação e Mitigação*  

#### *🔹 Browser & Web Client*  
📌 *Fluxo:* O usuário acessa o Web Client para realizar operações bancárias.  

| *Categoria* | *Ameaça* | *Impacto* | *Probabilidade* | *Risco* | *Mitigação* | *Tipo* |
|------------|---------|---------|--------------|-------|------------|--------|
| *Spoofing* | Phishing | Alto | Alto | **Alto** | Implementar **MFA**, treinamentos de segurança e **verificação de URL**. | **Preventiva** |
| *Tampering* | MITM (Man-in-the-Middle) | Alto | Médio | **Alto** | Habilitar **TLS 1.2+**, **HSTS** e validar certificados. | **Preventiva** |
| *Repudiation* | Falta de logs | Médio | Alto | **Alto** | Implementar **logs de auditoria assinados**. | **Detectiva** |
| *Information Disclosure* | Vazamento de dados no cache | Alto | Médio | **Alto** | Desativar cache para dados sensíveis, implementar **CSP (Content Security Policy)**. | **Preventiva** |
| *Denial of Service* | Ataques XSS | Médio | Alto | **Alto** | Aplicar **rate limiting**, **CSP** e validação de entrada. | **Preventiva** |
| *Elevation of Privilege* | Sequestro de sessão | Alto | Alto | **Alto** | Configurar **SameSite Cookies**, token com expiração curta. | **Preventiva e Corretiva** |

---

#### *🔹 Nginx (Proxy Reverso & TLS)*  
📌 *Fluxo:* O Nginx gerencia o tráfego HTTPS e encaminha requisições ao backend.  

| *Categoria* | *Ameaça* | *Impacto* | *Probabilidade* | *Risco* | *Mitigação* | *Tipo* |
|------------|---------|---------|--------------|-------|------------|--------|
| *Spoofing* | TLS downgrade | Alto | Médio | **Alto** | Configurar **TLS 1.2+**, desativar protocolos inseguros. | **Preventiva** |
| *Tampering* | Modificação de cabeçalhos | Médio | Médio | **Médio** | Implementar **CSP**, assinar headers HTTP críticos. | **Preventiva** |
| *Repudiation* | Logs insuficientes | Médio | Alto | **Alto** | Configurar **log centralizado e alertas de anomalias**. | **Detectiva** |
| *Information Disclosure* | Certificado inválido | Alto | Baixo | **Médio** | Implementar **Certificados confiáveis e renovação automática**. | **Preventiva** |
| *Denial of Service* | Ataque DDoS | Alto | Alto | **Alto** | Configurar **WAF, Rate Limiting e Firewall**. | **Preventiva e Detectiva** |
| *Elevation of Privilege* | Proxy aberto indevido | Alto | Médio | **Alto** | Restringir **IP allowlist**, autenticação obrigatória. | **Preventiva** |

---

#### *🔹 RESTful Web Service*  
📌 *Fluxo:* O backend processa requisições do Web Client.  

| *Categoria* | *Ameaça* | *Impacto* | *Probabilidade* | *Risco* | *Mitigação* | *Tipo* |
|------------|---------|---------|--------------|-------|------------|--------|
| *Spoofing* | Autenticação bypass | Alto | Médio | **Alto** | Implementar **OAuth 2.0 + JWT**. | **Preventiva** |
| *Tampering* | Injeção SQL | Alto | Médio | **Alto** | Usar **ORM seguro**, validação de entrada. | **Preventiva** |
| *Repudiation* | Falta de auditoria | Alto | Alto | **Alto** | Implementar **logs detalhados** e auditoria imutável. | **Detectiva** |
| *Information Disclosure* | Erros detalhados na API | Médio | Médio | **Médio** | Configurar **mensagens genéricas**, evitar **stack trace** exposta. | **Preventiva** |
| *Denial of Service* | Ataques de repetição | Alto | Alto | **Alto** | Implementar **rate limiting, CAPTCHA**. | **Preventiva** |
| *Elevation of Privilege* | Acesso indevido a APIs | Alto | Médio | **Alto** | Implementar **RBAC (Role-Based Access Control)**. | **Preventiva e Detectiva** |

---

#### *🔹 Banco de Dados PostgreSQL*  
📌 *Fluxo:* Armazena informações bancárias dos usuários.  

| *Categoria* | *Ameaça* | *Impacto* | *Probabilidade* | *Risco* | *Mitigação* | *Tipo* |
|------------|---------|---------|--------------|-------|------------|--------|
| *Spoofing* | Uso de credenciais comprometidas | Alto | Alto | **Alto** | Implementar **MFA e rotação de credenciais**. | **Preventiva** |
| *Tampering* | Alteração de saldos | Alto | Médio | **Alto** | Implementar **tabelas de auditoria imutáveis**. | **Preventiva** |
| *Repudiation* | Falta de logs | Alto | Alto | **Alto** | Habilitar **PostgreSQL Audit Logs**. | **Detectiva** |
| *Information Disclosure* | Dados sem criptografia | Alto | Alto | **Alto** | Aplicar **AES-256, TDE (Transparent Data Encryption)**. | **Preventiva** |
| *Denial of Service* | Consultas pesadas | Alto | Médio | **Alto** | Configurar **limites de conexão e índices otimizados**. | **Preventiva e Detectiva** |
| *Elevation of Privilege* | Usuários com privilégios excessivos | Alto | Alto | **Alto** | Aplicar **Princípio do Menor Privilégio**. | **Preventiva** |

---



