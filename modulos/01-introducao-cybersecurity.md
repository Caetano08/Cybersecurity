# 📖 Módulo 1: Introdução à Cybersecurity

## 🎯 Objetivos do Módulo

- Compreender os conceitos fundamentais de Cybersecurity
- Conhecer as principais ameaças e vulnerabilidades
- Entender o ciclo de vida de um ataque
- Familiarizar-se com princípios de defesa

---

## 1.1 O que é Cybersecurity?

**Definição**: Cybersecurity (Segurança Cibernética) é a prática de proteger sistemas, redes e dados contra ataques digitais, roubo e danos não autorizados.

### Pilares da Segurança

```
┌─────────────────────────────────────┐
│     Tríade de Segurança (CIA)       │
├─────────────────────────────────────┤
│ 1. Confidentiality (Confidencialidade)
│    → Dados acessados apenas por autorizado
│
│ 2. Integrity (Integridade)
│    → Dados não foram alterados
│
│ 3. Availability (Disponibilidade)
│    → Dados/sistemas acessíveis quando necessário
└─────────────────────────────────────┘
```

---

## 1.2 Tipos de Ameaças

| Ameaça | Descrição | Risco |
|--------|-----------|-------|
| **Malware** | Software malicioso (vírus, trojan, ransomware) | Alto |
| **Phishing** | Engenharia social para roubar dados | Médio-Alto |
| **DDoS** | Ataque de negação de serviço distribuído | Alto |
| **SQL Injection** | Exploração de banco de dados | Alto |
| **Man-in-the-Middle** | Interceptação de comunicação | Médio-Alto |
| **Força Bruta** | Tentativa exaustiva de senhas | Médio |
| **Social Engineering** | Manipulação humana | Médio-Alto |

---

## 1.3 Ciclo de Vida de um Ataque

```
1️⃣  RECONHECIMENTO
    ├─ Coleta de informações públicas
    ├─ Identificação de infraestrutura
    └─ Busca por vulnerabilidades

2️⃣  ACESSO INICIAL
    ├─ Exploração de vulnerabilidades
    ├─ Phishing/engenharia social
    └─ Força bruta

3️⃣  PERSISTÊNCIA
    ├─ Instalação de backdoors
    ├─ Criação de contas privilegiadas
    └─ Modificação de sistemas

4️⃣  ESCALAÇÃO DE PRIVILÉGIOS
    ├─ Obtenção de acesso root/admin
    ├─ Exploração de vulnerabilidades do kernel
    └─ Token stealing

5️⃣  EXFILTRAÇÃO DE DADOS
    ├─ Roubo de informações confidenciais
    ├─ Transferência de dados
    └─ Cobertura de rastros

6️⃣  COBERTURA DE RASTROS
    ├─ Limpeza de logs
    ├─ Remoção de evidências
    └─ Destruição de backups
```

---

## 1.4 Profissionais de Cybersecurity

### Hackers Éticos vs Criminosos

| Aspecto | Hacker Ético | Hacker Criminoso |
|--------|--------------|------------------|
| **Autorização** | Contratado e autorizado | Sem permissão |
| **Objetivo** | Melhorar segurança | Lucro/Ganho pessoal |
| **Legalidade** | Dentro da lei | Ilegal |
| **Ética** | Código de conduta | Sem restrições morais |

### Carreiras em Cybersecurity

1. **Security Analyst** - Monitora redes e sistemas
2. **Penetration Tester** - Realiza testes de segurança autorizados
3. **Ethical Hacker (CEH)** - Especialista em segurança ofensiva
4. **Forensic Analyst** - Investiga incidentes de segurança
5. **Security Architect** - Projeta sistemas seguros
6. **CISO** - Chief Information Security Officer

---

## 📝 Exercícios - Módulo 1

### Exercício 1.1: Identificar Ameaças

**Cenário**: Uma empresa de e-commerce sofreu uma violação de segurança. Analise o seguinte:

```
Logs do servidor:
- 10:00 - Múltiplas requisições de login falhadas (15 tentativas em 2 minutos)
- 10:02 - Login bem-sucedido com credenciais do admin
- 10:05 - Acesso ao banco de dados de clientes
- 10:07 - 50MB de dados transferidos para IP externo
- 10:10 - Logs do servidor deletados
```

**Questão**: Identifique as fases do ataque conforme o ciclo de vida.

### ✅ Resolução

```
FASE 1 - RECONHECIMENTO
└─ (Não visível nos logs, mas ocorreu antes)
   Possível: Enumeração de usuários, identificação de sistema admin

FASE 2 - ACESSO INICIAL ⚠️
├─ Evidência: 15 tentativas de login em 2 minutos
├─ Tipo: Força bruta contra conta admin
└─ Timestamp: 10:00-10:02

FASE 3 - PERSISTÊNCIA ✅ (Implícito)
├─ Login bem-sucedido da conta admin
├─ Possível: Criação de backdoor
└─ Timestamp: 10:02

FASE 4 - ESCALAÇÃO (Não necessária - já é admin)

FASE 5 - EXFILTRAÇÃO ⚠️
├─ Evidência: Acesso ao BD de clientes
├─ Evidência: 50MB de dados transferidos
├─ Tipo: Roubo de dados de clientes
└─ Timestamp: 10:05-10:07

FASE 6 - COBERTURA ⚠️
├─ Evidência: Logs deletados
├─ Objetivo: Remover evidências do ataque
└─ Timestamp: 10:10
```

**Análise de Risco**:
- **Tipo de Ameaça**: Força Bruta + Exfiltração de Dados
- **Comprometimento**: ALTO - Dados de clientes (PII) expostos
- **Violação CIA**:
  - ❌ Confidentiality: Violada (dados roubados)
  - ⚠️ Integrity: Comprometida (logs deletados = falta de confiança)
  - ⚠️ Availability: Afetada se sistema usado para ataque DDoS

---

### Exercício 1.2: Classificar Ameaças

**Cenário**: Classifique as seguintes situações conforme sua criticidade:

| Situação | Ameaça | CIA Violada | Criticidade |
|----------|--------|------------|-------------|
| Dados de clientes vazados na dark web | ? | ? | ? |
| Site fora do ar por 2 horas (DDoS) | ? | ? | ? |
| Admin modificou log de auditoria | ? | ? | ? |

### ✅ Resolução

| Situação | Ameaça | CIA Violada | Criticidade |
|----------|--------|------------|-------------|
| Dados de clientes vazados | Exfiltração/Roubo de Dados | Confidentiality | **CRÍTICO** |
| Site fora por 2h (DDoS) | DoS Attack | Availability | **ALTO** |
| Log modificado | Insider Threat/Tampering | Integrity | **CRÍTICO** |

**Justificativa**:
- **Vazamento**: CRÍTICO - Afeta reputação, LGPD/GDPR, clientes
- **DDoS**: ALTO - Perda de receita, mas temporário
- **Log modificado**: CRÍTICO - Quebra cadeia de custódia, impede investigação

---

## 🎓 Resumo do Módulo 1

✅ Cybersecurity protege confidencialidade, integridade e disponibilidade
✅ Ameaças seguem padrões previsíveis (ciclo de vida)
✅ Hackers éticos trabalham dentro da lei com autorização
✅ Cada fase do ataque deixa evidências diferentes

---

## 📚 Recursos Complementares

- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [NIST Cybersecurity Framework](https://www.nist.gov/cyberframework)
- [CIS Controls](https://www.cisecurity.org/controls)
- [Mitre ATT&CK Framework](https://attack.mitre.org/)

