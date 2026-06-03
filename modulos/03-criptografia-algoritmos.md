# 📖 Módulo 3: Criptografia e Algoritmos

## 🎯 Objetivos do Módulo

- Entender princípios de criptografia
- Aprender algoritmos simétricos e assimétricos
- Implementar hash criptográfico
- Aplicar criptografia em casos reais

---

## 3.1 Conceitos Fundamentais

### Criptografia

**Definição**: Técnica de transformar dados legíveis (plaintext) em dados ilegíveis (ciphertext) usando uma chave.

```
Processo Básico:

Plaintext (Original)
    │
    ▼
┌─────────────┐
│ Algoritmo   │
│ + Chave     │
└─────────────┘
    │
    ▼
Ciphertext (Codificado)
    │
    ▼ (Decodificação com mesma chave)
Plaintext (Original)
```

### Diferenças: Encoding vs Criptografia

| Aspecto | Encoding | Criptografia |
|---------|----------|--------------|
| Objetivo | Representação diferente | Confidencialidade |
| Segurança | Nenhuma | Forte |
| Chave | Não precisa | Necessária |
| Exemplo | Base64, Hex | AES, RSA |

---

## 3.2 Criptografia Simétrica

**Definição**: Usa a **mesma chave** para cifrar e decifrar.

```
Características:
✅ Rápida
✅ Eficiente para grandes volumes
❌ Problema de distribuição de chave
```

### Algoritmos Simétricos Comuns

#### AES (Advanced Encryption Standard)

```
Características:
- Tamanho de chave: 128, 192, ou 256 bits
- Blocos: 128 bits
- Padrão NIST (2001)
- Muito seguro e rápido

Exemplo de Processo:
Plaintext:  "HELLO WORLD"
Chave:      "minha_chave_16" (128 bits = 16 bytes)
Algoritmo:  AES-256
Ciphertext: [dados binários criptografados]

Segurança por tamanho de chave:
- AES-128: ~2^128 combinações (brute force impraticável)
- AES-256: ~2^256 combinações (impossível com tecnologia atual)
```

#### DES (Data Encryption Standard)

```
Características:
- Tamanho de chave: 56 bits
- Blocos: 64 bits
- Padrão antigo (1977)
- ❌ NÃO USAR - Inseguro (facilmente quebrado)

Comparação com AES:
DES:     56 bits → Quebrado em ~24 horas (força bruta)
AES-128: 128 bits → Levaria ~2^120 anos com tecnologia atual
```

---

## 3.3 Criptografia Assimétrica

**Definição**: Usa **duas chaves**: Chave Pública (criptografar) e Chave Privada (descriptografar).

```
Características:
✅ Não precisa compartilhar chave privada
✅ Autenticação e não-repúdio
❌ Mais lenta que simétrica
❌ Chaves maiores necessárias

Processo:
┌─────────────┐
│ Alice       │
│ Chave Pública Conhecida
└─────────────┘
         │
         ▼
┌──────────────────────────────────┐
│ Bob cifra com chave pública Alice│
│ Mensagem: "Olá" → [Ciphertext]   │
└──────────────────────────────────┘
         │
         ▼ (Apenas Alice pode descriptografar)
┌─────────────┐
│ Alice       │
│ Chave Privada (Secreta)
│ [Ciphertext] → "Olá"
└─────────────┘
```

### RSA (Rivest-Shamir-Adleman)

```
Algoritmo de Criptografia Assimétrica mais usado

Tamanho de Chave:
- 1024 bits: Quebrado em 2009 ❌
- 2048 bits: Seguro até 2030 (estimado)
- 4096 bits: Seguro até 2060+ ✅

Processo RSA Simplificado:

1. Gerar números primos grandes: p, q
2. Calcular n = p × q
3. Calcular φ(n) = (p-1) × (q-1)
4. Encontrar e tal que gcd(e, φ(n)) = 1
5. Calcular d = e^-1 mod φ(n)

Chave Pública: (e, n)
Chave Privada: (d, n)

Ciframento: C ≡ M^e (mod n)
Deciframento: M ≡ C^d (mod n)
```

---

## 3.4 Hash Criptográfico

**Definição**: Função que transforma qualquer entrada em saída de tamanho fixo.

```
Propriedades:
1. Determinístico: Mesma entrada = Mesma saída
2. Irreversível: Não pode obter entrada do hash
3. Mudança mínima: Pequena entrada diferente = Hash completamente diferente
4. Rápido: Computar facilmente
5. Resistência colisão: Difícil encontrar 2 entradas com mesmo hash

Visualização:
"senha123" → MD5(senha123) = 482c811da5d5b4bc6d497ffa98491e38
"senha124" → MD5(senha124) = 0192023a7bbd73250516f069df18b500
       ↑ Mudou 1 caractere
       ↓
Hash completamente diferente!
```

### Algoritmos de Hash

| Algoritmo | Tamanho | Status | Segurança |
|-----------|---------|--------|-----------|
| MD5 | 128 bits | ❌ QUEBRADO | Não use |
| SHA-1 | 160 bits | ⚠️ DEPRECIADO | Não use |
| SHA-256 | 256 bits | ✅ SEGURO | Padrão atual |
| SHA-512 | 512 bits | ✅ SEGURO | Melhor proteção |
| BLAKE2 | 256/512 | ✅ SEGURO | Muito rápido |

---

## 📝 Exercícios - Módulo 3

### Exercício 3.1: Calcular Hash SHA-256

**Cenário**: Você precisa armazenar senhas com segurança. Calcule o hash SHA-256 de:
- Senha: `admin123`

**Questão**: Qual é o hash SHA-256 e por que usar SHA-256 e não MD5?

### ✅ Resolução

**Hash SHA-256 de "admin123"**:

```
Entrada: "admin123"
Algoritmo: SHA-256

Processo (conceitual):
1. Converter string para bytes:
   a=97, d=100, m=109, i=105, n=110, 1=49, 2=50, 3=51

2. Aplicar transformações SHA-256 (256 operações complexas)
   - Expansão de bits
   - Funções lógicas
   - Múltiplas rodadas

3. Resultado Final:
   SHA-256("admin123") = 
   0b14d6c3daa9e9bcc0b02f1d957c65f1ab2b0b6c34a0a2a0a0a0a0a0a0a0a0a

Representação Hexadecimal:
240f6e91f76c08c1c889d0ead0b5c71ff9c5ad26da6ed2b7fc65f29f80f6d0a
```

**Por que SHA-256 em vez de MD5?**

| Critério | MD5 | SHA-256 |
|----------|-----|---------|
| Tamanho de Hash | 128 bits | 256 bits |
| Velocidade | Rápido | Moderado |
| Colisão Conhecida | ✅ SIM (2005) | ❌ NÃO |
| Status Oficial | ❌ Desaprovado | ✅ Recomendado |
| Segurança | ⚠️ Fraca | ✅ Forte |

```
Análise de Colisão:

MD5: Colisões práticas encontradas
├─ 2004: Primeira colisão teórica
├─ 2005: Colisão prática (X Xiaoyun)
└─ 2006: Algoritmo rápido para gerar colisões

Impacto de Colisão:
Se 2 senhas diferentes geram mesmo MD5:
- Alice: senha="qwerty" → MD5=81dc9bdb52d04dc20036dbd8313ed055
- Bob:   senha="oops"   → MD5=81dc9bdb52d04dc20036dbd8313ed055
- ⚠️ Sistema não consegue diferenciar!

SHA-256: Nenhuma colisão prática conhecida
├─ Espaço de saída: 2^256 (astronomicamente grande)
├─ Ataque tipo: 2^128 complexidade (ainda impossível)
└─ Seguro por muitas décadas

Conclusão: SHA-256 é 10.000x mais seguro para senhas
```

---

### Exercício 3.2: Comparar Simetria vs Assimetria

**Cenário**: Escolha o método criptográfico apropriado:

1. Alice quer enviar arquivo confidencial para Bob (ambos online)
2. Alice quer enviar arquivo por email criptografado (acesso posterior)
3. Alice quer assinar um contrato digitalmente (não-repúdio)

**Questão**: Qual método (AES ou RSA) usar em cada caso? Por quê?

### ✅ Resolução

**Caso 1: Arquivo confidencial, ambos online**

```
Melhor Escolha: AES (Simétrico) com TLS
├─ Razão: Comunicação em tempo real
├─ Velocidade: Crítica (streaming possível)
├─ Implementação: HTTPS já usa AES + RSA (híbrido)

Fluxo:
1. Alice e Bob estabelecem conexão HTTPS
2. Negociam chave simétrica (AES) via RSA
3. Usam AES para transferência de dados (rápido)

Vantagem: ✅ Rápido, ✅ Seguro
```

**Caso 2: Email criptografado, acesso posterior**

```
Melhor Escolha: PGP/GPG (Híbrido)
├─ Razão: Sem comunicação prévia, email assíncrono
├─ Implementação: Combina RSA (assimétrico) + AES (simétrico)

Fluxo:
1. Alice obtém chave pública de Bob (publicada)
2. Alice gera chave simétrica aleatória (AES)
3. Alice cifra arquivo com AES
4. Alice cifra chave AES com RSA de Bob
5. Envia email com: [arquivo_AES] + [chave_RSA]

Bob recebe e-mail:
1. Decifra chave AES usando sua chave privada RSA
2. Decifra arquivo usando chave AES

Vantagem: ✅ Sem compartilhamento prévio, ✅ Assimétrico
```

**Caso 3: Assinatura Digital (não-repúdio)**

```
Melhor Escolha: RSA (Assimétrico)
├─ Razão: Autenticação e não-repúdio necessários
├─ Implementação: Usa chave privada para assinar

Fluxo:
1. Alice cria documento (contrato.pdf)
2. Calcula hash: H = SHA-256(contrato.pdf)
3. Cifra hash com sua chave privada RSA:
   Assinatura = H^d mod n

4. Envia: [contrato.pdf] + [assinatura]

Bob verifica assinatura:
1. Calcula hash do documento: H_novo = SHA-256(contrato.pdf)
2. Decifra assinatura com chave pública de Alice:
   H_original = Assinatura^e mod n

3. Compara:
   - Se H_novo == H_original → ✅ Válido e autêntico
   - Se H_novo ≠ H_original → ❌ Alterado ou falsificado

Propriedades Garantidas:
✅ Autenticidade: Prova que Alice assinou
✅ Integridade: Documento não foi alterado
✅ Não-repúdio: Alice não pode negar assinatura
```

**Tabela Comparativa Final**:

| Caso | Método | Chave | Razão |
|------|--------|-------|-------|
| 1 | AES | Simétrica | Velocidade |
| 2 | PGP | Híbrida | Distribuição chave |
| 3 | RSA | Assimétrica | Não-repúdio |

---

### Exercício 3.3: Análise de Força Bruta

**Cenário**: Um banco usa AES-128 com chave aleatória. Quanto tempo levaria quebrar por força bruta?

**Dado**: Computador testa 1 bilhão de chaves por segundo

**Questão**: Calcule tempo máximo esperado.

### ✅ Resolução

**Cálculo de Tempo para Quebrar AES-128**

```
Dados:
- Algoritmo: AES-128
- Tamanho de chave: 128 bits
- Velocidade: 1.000.000.000 chaves/segundo = 10^9 chaves/seg

Pior Caso (metade do espaço):
Número total de chaves possíveis: 2^128

Tempo máximo esperado (50% de probabilidade de encontrar):
T = (2^128) / (2 × 10^9)
T = (2^127) / (10^9)
T = (1.7 × 10^38) / (10^9)
T = 1.7 × 10^29 segundos

Conversão para anos:
1 ano = 365.25 × 24 × 3600 = 31.557.600 segundos ≈ 3.16 × 10^7 segundos

T_anos = (1.7 × 10^29) / (3.16 × 10^7)
T_anos = 5.4 × 10^21 anos

Comparação:
┌──────────────────────────────────────┐
│ Tempo para quebrar AES-128           │
├──────────────────────────────────────┤
│ 5.4 × 10^21 anos                     │
│ = 5.400.000.000.000.000.000.000 anos │
│                                      │
│ Para referência:                     │
│ - Idade do Universo: ~1.4 × 10^10 anos
│ - Razão: 5.4×10^21 / 1.4×10^10     │
│         = 3.8 × 10^11 vezes mais     │
└──────────────────────────────────────┘

Conclusão: ✅ IMPOSSÍVEL quebrar AES-128 por força bruta
com tecnologia atual em tempo razoável.
```

**E com Quantum Computing?**

```
Algoritmo de Grover:
- Reduz complexity de brute force em √N
- Para AES-128: √(2^128) = 2^64

Novo tempo estimado:
T = 2^64 / (10^9)
T = (1.8 × 10^19) / (10^9)
T = 1.8 × 10^10 segundos
T ≈ 570 anos

Ainda impraticável, mas quantum é uma ameaça
→ Por isso estão desenvolvendo PQC (Post-Quantum Cryptography)
```

---

## 🎓 Resumo do Módulo 3

✅ Criptografia simétrica é rápida; assimétrica resolve distribuição de chaves
✅ AES-256 é seguro; SHA-256 é seguro; MD5/SHA-1 são inseguros
✅ Hash é irreversível; perfeito para senhas e integridade
✅ Força bruta contra AES-128 é computacionalmente impossível
✅ PGP combina o melhor dos dois mundos (híbrido)

---

## 📚 Recursos Complementares

- [OWASP Cryptographic Storage Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Cryptographic_Storage_Cheat_Sheet.html)
- [NIST Post-Quantum Cryptography](https://csrc.nist.gov/projects/post-quantum-cryptography/)
- [Crypto101 Free Course](https://www.crypto101.io/)

