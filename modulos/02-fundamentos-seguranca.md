# 📖 Módulo 2: Fundamentos de Segurança

## 🎯 Objetivos do Módulo

- Entender os conceitos fundamentais de segurança de redes
- Aprender sobre autenticação e autorização
- Conhecer mecanismos de proteção
- Implementar boas práticas de segurança

---

## 2.1 Autenticação vs Autorização

### Autenticação (Authentication)

**Definição**: Verificar a **identidade** de um usuário ou sistema.

```
Processo de Autenticação:
┌─────────────────┐
│  Usuário entra  │
│   com credencial│
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────┐
│ Sistema verifica credencial     │
│ (senha, token, biometria, etc.) │
└────────┬────────────────────────┘
         │
    ✅ SIM / ❌ NÃO
```

**Fatores de Autenticação**:
- **Algo que você sabe**: Senha, PIN
- **Algo que você possui**: Cartão, token, celular
- **Algo que você é**: Biometria (fingerprint, face)

### Autorização (Authorization)

**Definição**: Determinar **o que** o usuário autenticado pode fazer.

```
Exemplo:
Usuário: Alice
Autenticação: ✅ (senha correta)
Autorização: ❌ Não pode acessar relatório financeiro
           ✅ Pode visualizar seu próprio perfil
```

---

## 2.2 Controle de Acesso

### Modelos de Controle de Acesso

#### 1. DAC (Discretionary Access Control)
```
Características:
- Proprietário do recurso define permissões
- Flexível, fácil de gerenciar
- Menos seguro para ambientes empresariais
- Exemplo: Permissões de arquivo no Linux

Comando Linux (DAC):
chmod 755 arquivo.txt
│   │││
│   │││
│   ││└─ Outros: read, execute (5 = r + x)
│   │└──  Grupo: read, execute (5 = r + x)
│   └─── Proprietário: read, write, execute (7 = r + w + x)
```

#### 2. MAC (Mandatory Access Control)
```
Características:
- Administrador central define políticas
- Rígido, muito seguro
- Usado em sistemas militares/governo
- Exemplo: SELinux

Estrutura:
Usuário → Nível de Segurança → Recursos permitidos
```

#### 3. RBAC (Role-Based Access Control)
```
Características:
- Baseado em funções/papéis
- Mais prático para empresas
- Facilita auditoria e compliance

Exemplo:
┌─────────────────────────┐
│       Roles (Papéis)    │
├─────────────────────────┤
│ Admin         → Acesso Total
│ Gerente       → Acesso Moderado
│ Funcionário   → Acesso Limitado
│ Estagiário    → Acesso Restrito
└─────────────────────────┘
```

#### 4. ABAC (Attribute-Based Access Control)
```
Características:
- Baseado em atributos (flexível)
- Mais granular e preciso
- Escalável para grandes ambientes

Exemplo:
Acesso_arquivo.pdf = Permitido se:
- Usuário.departamento == "TI" AND
- Usuário.nivel_seguranca >= 3 AND
- Hora_atual entre 09:00 e 18:00 AND
- IP_origem == "Rede_corporativa"
```

---

## 2.3 Segurança de Redes

### Firewall

```
                  INTERNET
                     │
                     ▼
            ┌─────────────────┐
            │   Firewall      │
            │  (Porta 443)    │
            │  (Porta 80)     │
            │  (Porta 22) ❌  │ Bloqueado
            └────────┬────────┘
                     │
                     ▼
              ┌──────────────┐
              │ Rede Privada │
              │  (Protegida) │
              └──────────────┘
```

### VPN (Virtual Private Network)

```
Sem VPN (Inseguro):
┌────────┐                        ┌──────────┐
│ Cliente│ ─ Dados em claro ──→ │ Internet │
└────────┘                        └──────────┘
   🔓 Vulnerável

Com VPN (Seguro):
┌────────┐                        ┌──────────┐
│ Cliente│ ─ Dados Criptografados → │ Internet │
└────────┘ (Tunel encriptado)     └──────────┘
   🔐 Protegido
```

---

## 2.4 Segurança em Camadas (Defense in Depth)

```
        CAMADA 1: Perímetro
        ├─ Firewall
        └─ IPS/IDS

        CAMADA 2: Rede
        ├─ VLAN
        └─ Segmentação

        CAMADA 3: Host
        ├─ Antivírus
        └─ Host-based Firewall

        CAMADA 4: Aplicação
        ├─ Validação de input
        └─ Sanitização de dados

        CAMADA 5: Dados
        ├─ Criptografia
        └─ Backup
```

---

## 📝 Exercícios - Módulo 2

### Exercício 2.1: Identificar Mecanismo de Controle

**Cenário**: Uma empresa implementou o seguinte sistema:

```
Departamento: Financeiro
Funcionários: Alice (Contadora), Bob (Estagiário)

Acesso ao arquivo: relatorio_vendas.xlsx
- Alice pode: Ler, Editar, Compartilhar
- Bob pode: Apenas Ler
```

**Questão**: Qual modelo de controle de acesso foi implementado?

### ✅ Resolução

**Resposta**: RBAC (Role-Based Access Control)

**Justificativa**:
```
Análise:
✅ Permissões baseadas em funções/papéis
✅ Alice (Role: Contadora) → Mais permissões
✅ Bob (Role: Estagiário) → Menos permissões
✅ Fácil escala (adicionar novos estagiários = mesmo acesso)

Se fosse DAC:
❌ O proprietário do arquivo decidiria individualmente

Se fosse MAC:
❌ Não há níveis de segurança fixos

Se fosse ABAC:
⚠️ Seria mais complexo com múltiplos atributos
```

---

### Exercício 2.2: Análise de Segurança de Rede

**Cenário**: Analise a seguinte arquitetura:

```
        INTERNET (Não confiável)
             │
             ▼
      ┌──────────────┐
      │  DMZ Zone    ��� (Servidores Web)
      │ (Público)    │
      └──────────────┘
             │
      Firewall (Porta 443 e 80 permitidas)
             │
             ▼
      ┌──────────────────┐
      │ Rede Privada     │ (Servidores de BD)
      │ (Confiável)      │
      └──────────────────┘
```

**Questões**:
1. Que camadas de defesa foram implementadas?
2. Por que há uma separação entre DMZ e rede privada?
3. Qual ameaça isto mitiga?

### ✅ Resolução

**1. Camadas de Defesa Implementadas**:
```
✅ CAMADA 1 (Perímetro): Firewall
✅ CAMADA 2 (Rede): Segmentação DMZ/Privada
✅ CAMADA 3 (Host): Servidores isolados
```

**2. Por que separar DMZ e Rede Privada?**
```
DMZ (Demilitarized Zone):
├─ Acessível da Internet
├─ Contém servidores web (público)
└─ Risco: Alto (pode ser comprometida)

Rede Privada:
├─ Não acessível diretamente da Internet
├─ Contém dados sensíveis (BD)
└─ Risco: Baixo (protegida por firewall)

Benefício:
Se servidor web é hackeado, BD permanece protegido
```

**3. Ameaça Mitigada**:
```
Ataque de Propagação Lateral:

❌ SEM DMZ (Cenário perigoso):
┌─────────────────────────────────┐
│ Internet → Servidor Web         │
│              ↓                  │
│         (Hackeado)              │
│              ↓                  │
│         Banco de Dados          │ ← Comprometido!
└─────────────────────────────────┘

✅ COM DMZ (Cenário seguro):
┌──────────────────────────────────┐
│ Internet → Servidor Web (DMZ)    │
│               ↓                  │
│          (Hackeado)              │
│               ↓                  │
│      Firewall BLOQUEIA           │
│               ↓                  │
│       Banco de Dados (Seguro!)   │
└──────────────────────────────────┘

Ameaça Mitigada: Propagação Lateral, Movimento Horizontal do Atacante
```

---

### Exercício 2.3: Cálculo de Força de Senha

**Cenário**: Avalie a força de duas senhas:

- **Senha A**: `password123`
- **Senha B**: `7#mK$p9@xL2Q`

**Questão**: Qual é mais segura? Calcule o espaço de chaves aproximado.

### ✅ Resolução

**Análise Senha A: `password123`**

```
Composição:
├─ Letras minúsculas: p, a, s, s, w, o, r, d
├─ Números: 1, 2, 3
└─ Caracteres especiais: NENHUM

Conjunto de caracteres (charset):
- Letras minúsculas: 26
- Números: 10
- TOTAL: 36 caracteres possíveis

Comprimento: 11 caracteres

Espaço de Chaves = charset ^ comprimento
                 = 36 ^ 11
                 = 131.621.703.842.267.136

Log₂(36^11) = 11 × log₂(36)
            = 11 × 5,17
            = 56,87 bits

Força: FRACA ⚠️
- Palavra comum ("password")
- Padrão previsível (número sequencial)
- Vulnerável a dicionário + força bruta
```

**Análise Senha B: `7#mK$p9@xL2Q`**

```
Composição:
├─ Letras minúsculas: m, p, x, l
├─ Letras maiúsculas: K, Q, L
├─ Números: 7, 9, 2
└─ Caracteres especiais: #, $, @

Conjunto de caracteres (charset):
- Letras minúsculas: 26
- Letras maiúsculas: 26
- Números: 10
- Caracteres especiais: ~32 (estimado)
- TOTAL: 94 caracteres possíveis

Comprimento: 12 caracteres

Espaço de Chaves = charset ^ comprimento
                 = 94 ^ 12
                 = 475.920.314.814.253.376

Log₂(94^12) = 12 × log₂(94)
            = 12 × 6,55
            = 78,6 bits

Força: FORTE ✅
- Sem palavras comuns
- Mistura aleatória
- Combinação de tipos de caracteres
- Difícil de quebrar por força bruta
```

**Comparação**:

| Métrica | Senha A | Senha B |
|---------|---------|---------|
| Comprimento | 11 | 12 |
| Charset | 36 | 94 |
| Espaço de Chaves | 1.3 × 10^16 | 4.8 × 10^23 |
| Bits de Entropia | ~57 | ~79 |
| Tempo Brute Force (1M tentativas/s) | 4 horas | 15 milhões de anos |
| Força | ⚠️ FRACA | ✅ FORTE |

**Conclusão**: Senha B é ~10 mil vezes mais segura que Senha A.

---

## 🎓 Resumo do Módulo 2

✅ Autenticação verifica identidade; Autorização define permissões
✅ RBAC é mais prático para ambientes empresariais
✅ Defesa em camadas aumenta segurança exponencialmente
✅ DMZ separa servidores públicos de dados sensíveis
✅ Senhas fortes usam múltiplos tipos de caracteres

---

## 📚 Recursos Complementares

- [NIST Password Guidelines](https://pages.nist.gov/800-63-3/)
- [OWASP Authentication Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Authentication_Cheat_Sheet.html)
- [Firewall Concepts](https://www.cisco.com/c/en/us/support/docs/security/firewalls/)

