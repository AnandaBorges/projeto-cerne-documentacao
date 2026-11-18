# 03 - Modelagem de Dados com PostgreSQL (Neon)

## 1. Justificativa do Paradigma Relacional no PostgreSQL

A transição da base de dados em grafo para o **PostgreSQL hospedado no Neon** proporciona um modelo de persistência de alta confiabilidade corporativa, conformidade estrita com as propriedades ACID e excelente capacidade analítica:
* **Integridade Referencial Forte:** Garantia de consistência através de chaves primárias, estrangeiras e restrições de unicidade (`UNIQUE`), impedindo a criação de registros órfãos ou duplicidades.
* **Transversalidade via Tabelas Associativas ($N:N$):** A relação em que uma ação comprova múltiplas práticas é modelada de maneira padronizada e performática através da tabela associativa `acoes_praticas`.
* **Cálculo de Indicadores e Práticas com Zero Evidências:** Consultas relacionais com `LEFT JOIN` e funções agregadas (`COUNT`, `COALESCE`) resolvem de forma determinística e veloz a contagem exigida pelo modelo CERNE.
* **Infraestrutura Serverless e Branching no Neon:** O Neon oferece separação entre computação e armazenamento, *connection pooling* nativo (via PgBouncer) e a funcionalidade de *database branching* (permitindo criar cópias instantâneas do banco para testes automatizados e migrações em CI/CD sem afetar os dados de produção).

---

## 2. Diagrama Entidade-Relacionamento Conceitual

```
+--------------------+        1:N        +-----------------------+
|       niveis       |-------------------|    processos_chave    |
+--------------------+                   +-----------------------+
| PK id              |                   | PK id                 |
|    codigo (1..4)   |                   |    codigo ('1.1')     |
|    nome            |                   |    nome               |
+--------------------+                   | FK nivel_id           |
         |                               +-----------------------+
         | 1:N                                       | 1:N
         v                                           v
+--------------------+                   +-----------------------+
|  niveis_cumulativo |                   |        praticas       |
+--------------------+                   +-----------------------+
| PK/FK nivel_id     |                   | PK id                 |
| PK/FK nivel_exigido|                   |    codigo ('1.1.1')   |
+--------------------+                   |    nome               |
                                         |    procedimento       |
                                         |    metrica            |
                                         |    aprendizado        |
                                         |    peso_qualitativo   |
                                         | FK processo_id        |
                                         | FK nivel_id           |
                                         +-----------------------+
                                                     |
                                                     | 1:N
                                                     v
+--------------------+       N:M         +-----------------------+
|       acoes        |===================|    acoes_praticas     |
+--------------------+ (acoes_praticas)  +-----------------------+
| PK id (UUID)       |                   | PK/FK acao_id         |
|    titulo          |                   | PK/FK pratica_id      |
|    data_acao       |                   +-----------------------+
|    tipo_acao       |                               ^
|    status_temporal |                               |
|    descricao       |                   +-----------------------+
|    created_at      |                   |  praticas_influencias |
+--------------------+                   +-----------------------+
   |          |                          | PK/FK pratica_origem  |
   | 1:N      | N:M (acoes_tags)         | PK/FK pratica_destino |
   |          v                          |    distancia_euclid   |
   |     +--------------------+          |    peso_influencia    |
   |     |        tags        |          +-----------------------+
   |     +--------------------+
   |     | PK id              |
   |     |    nome (UNIQUE)   |
   |     +--------------------+
   v
+--------------------+        N:M        +-----------------------+
|     evidencias     |===================|       empresas        |
+--------------------+  (acoes_empresas) +-----------------------+
| PK id (UUID)       |                   | PK id                 |
| FK acao_id         |                   |    nome_razao_social  |
|    tipo_documento  |                   |    cnpj / cpf         |
|    descricao       |                   |    status (inc/grad)  |
|    url_arquivo     |                   |    data_ingresso      |
|    hash_sha256     |                   +-----------------------+
+--------------------+                               | 1:N
                                                     v
                                         +-----------------------+
                                         | empresas_monitoramento|
                                         +-----------------------+
                                         | PK id                 |
                                         | FK empresa_id         |
                                         |    data_avaliacao     |
                                         |    nota_empreendedor  |
                                         |    nota_tecnologia    |
                                         |    nota_capital       |
                                         |    nota_mercado       |
                                         |    nota_gestao        |
                                         +-----------------------+
```

---

## 3. Catálogo de Tabelas e Esquema Físico (DDL PostgreSQL)

### 3.1. Taxonomia CERNE (Níveis, Processos e Práticas)
```sql
-- Degraus de maturidade CERNE (1 a 4)
CREATE TABLE niveis (
    id SERIAL PRIMARY KEY,
    codigo INT NOT NULL UNIQUE CHECK (codigo BETWEEN 1 AND 4),
    nome VARCHAR(50) NOT NULL,
    descricao TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabela de cumulatividade (CERNE 2 exige CERNE 1, etc.)
CREATE TABLE niveis_cumulatividade (
    nivel_id INT NOT NULL REFERENCES niveis(id) ON DELETE CASCADE,
    nivel_exigido_id INT NOT NULL REFERENCES niveis(id) ON DELETE CASCADE,
    PRIMARY KEY (nivel_id, nivel_exigido_id)
);

-- Processos-chave (12 processos funcionais)
CREATE TABLE processos_chave (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(10) NOT NULL UNIQUE, -- ex: '1.1', '1.2', '2.1'
    nome VARCHAR(150) NOT NULL,
    nivel_id INT NOT NULL REFERENCES niveis(id) ON DELETE RESTRICT,
    descricao TEXT
);

-- Práticas-chave (29 práticas atômicas no modelo base)
CREATE TABLE praticas (
    id SERIAL PRIMARY KEY,
    codigo VARCHAR(15) NOT NULL UNIQUE, -- ex: '1.1.1', '1.1.2'
    nome VARCHAR(150) NOT NULL,
    procedimento TEXT,
    metrica TEXT,
    aprendizado TEXT,
    peso_qualitativo NUMERIC(4,2) DEFAULT 1.00 NOT NULL,
    processo_id INT NOT NULL REFERENCES processos_chave(id) ON DELETE RESTRICT,
    nivel_id INT NOT NULL REFERENCES niveis(id) ON DELETE RESTRICT,
    ativa BOOLEAN DEFAULT TRUE NOT NULL
);
```

### 3.2. Ações, Evidências e Transversalidade ($1:N$)
```sql
-- Ocorrências e atividades realizadas na incubadora
CREATE TABLE acoes (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    titulo VARCHAR(200) NOT NULL,
    data_acao DATE NOT NULL,
    tipo_acao VARCHAR(50) NOT NULL, -- 'evento', 'mentoria', 'edital', 'reuniao'
    status_temporal VARCHAR(20) NOT NULL CHECK (status_temporal IN ('passada', 'presente', 'planejada')),
    descricao TEXT NOT NULL,
    publico_alvo VARCHAR(100),
    total_participantes INT DEFAULT 0,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Comprovantes formais gerados pela ação
CREATE TABLE evidencias (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    acao_id UUID NOT NULL REFERENCES acoes(id) ON DELETE CASCADE,
    tipo_documento VARCHAR(50) NOT NULL, -- 'ata', 'lista_presenca', 'foto', 'contrato'
    descricao VARCHAR(255) NOT NULL,
    url_arquivo TEXT NOT NULL,
    hash_sha256 VARCHAR(64),
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Tabela associativa de Transversalidade (Ação evidencia múltiplas Práticas)
CREATE TABLE acoes_praticas (
    acao_id UUID NOT NULL REFERENCES acoes(id) ON DELETE CASCADE,
    pratica_id INT NOT NULL REFERENCES praticas(id) ON DELETE RESTRICT,
    PRIMARY KEY (acao_id, pratica_id)
);
CREATE INDEX idx_acoes_praticas_pratica ON acoes_praticas(pratica_id);

-- Vocabulário controlado de TAGs
CREATE TABLE tags (
    id SERIAL PRIMARY KEY,
    nome VARCHAR(50) NOT NULL UNIQUE
);

-- Associação entre ações e TAGs
CREATE TABLE acoes_tags (
    acao_id UUID NOT NULL REFERENCES acoes(id) ON DELETE CASCADE,
    tag_id INT NOT NULL REFERENCES tags(id) ON DELETE CASCADE,
    PRIMARY KEY (acao_id, tag_id)
);
CREATE INDEX idx_acoes_tags_tag ON acoes_tags(tag_id);
```

### 3.3. Matriz de Influência (Equação [1] - Distância Euclidiana)
```sql
-- Matriz calculada de distância e sinergia entre práticas
CREATE TABLE praticas_influencias (
    pratica_origem_id INT NOT NULL REFERENCES praticas(id) ON DELETE CASCADE,
    pratica_destino_id INT NOT NULL REFERENCES praticas(id) ON DELETE CASCADE,
    distancia_euclidiana NUMERIC(8,4) NOT NULL,
    grau_influencia NUMERIC(5,2) NOT NULL, -- normalizado entre 0 e 1
    ultima_atualizacao TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (pratica_origem_id, pratica_destino_id)
);
```

### 3.4. Empresas Incubadas e os 5 Eixos
```sql
CREATE TABLE empresas (
    id SERIAL PRIMARY KEY,
    nome_fantasia VARCHAR(150) NOT NULL,
    razao_social VARCHAR(150),
    cnpj VARCHAR(18) UNIQUE,
    status VARCHAR(20) NOT NULL CHECK (status IN ('incubada', 'graduada', 'desligada')),
    data_ingresso DATE NOT NULL,
    data_graduacao DATE,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

-- Avaliação periódica multidimensional nos 5 eixos (CERNE 1)
CREATE TABLE empresas_monitoramento (
    id SERIAL PRIMARY KEY,
    empresa_id INT NOT NULL REFERENCES empresas(id) ON DELETE CASCADE,
    data_rodada DATE NOT NULL,
    eixo_empreendedor NUMERIC(4,2) CHECK (eixo_empreendedor BETWEEN 0 AND 10),
    eixo_tecnologia NUMERIC(4,2) CHECK (eixo_tecnologia BETWEEN 0 AND 10),
    eixo_capital NUMERIC(4,2) CHECK (eixo_capital BETWEEN 0 AND 10),
    eixo_mercado NUMERIC(4,2) CHECK (eixo_mercado BETWEEN 0 AND 10),
    eixo_gestao NUMERIC(4,2) CHECK (eixo_gestao BETWEEN 0 AND 10),
    parecer_consultor TEXT,
    created_at TIMESTAMP WITH TIME ZONE DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE acoes_empresas (
    acao_id UUID NOT NULL REFERENCES acoes(id) ON DELETE CASCADE,
    empresa_id INT NOT NULL REFERENCES empresas(id) ON DELETE CASCADE,
    PRIMARY KEY (acao_id, empresa_id)
);
```

---

## 4. Consultas SQL Canônicas de Negócio

### 4.1. Contagem de Evidências por Prática (Garantindo Zero Evidências)
Substitui a consulta Cypher com `OPTIONAL MATCH`, garantindo que todas as práticas do CERNE apareçam no painel visual:

```sql
SELECT 
    p.codigo AS codigo_pratica,
    p.nome AS nome_pratica,
    n.codigo AS nivel_cerne,
    COUNT(ap.acao_id) AS total_acoes,
    COALESCE(COUNT(e.id), 0) AS total_evidencias,
    CASE 
        WHEN COUNT(ap.acao_id) = 0 THEN 'CRITICA_SEM_EVIDENCIA'
        WHEN COUNT(ap.acao_id) < 3 THEN 'ATENCAO_POUCAS_EVIDENCIAS'
        ELSE 'EM_CONFORMIDADE'
    END AS status_conformidade
FROM praticas p
JOIN niveis n ON p.nivel_id = n.id
LEFT JOIN acoes_praticas ap ON p.id = ap.pratica_id
LEFT JOIN acoes a ON ap.acao_id = a.id
LEFT JOIN evidencias e ON a.id = e.acao_id
WHERE p.ativa = TRUE
GROUP BY p.id, p.codigo, p.nome, n.codigo
ORDER BY p.codigo ASC;
```

### 4.2. Detecção e Prevenção de Ações Duplicadas
```sql
SELECT 
    titulo, 
    data_acao, 
    tipo_acao, 
    COUNT(*) AS total_duplicadas
FROM acoes
GROUP BY titulo, data_acao, tipo_acao
HAVING COUNT(*) > 1;
```

### 4.3. Implementação da Equação [1] (Vetor de TAGs por Prática)
A distância euclidiana entre a prática $P_i$ e $P_j$ baseia-se na frequência de coocorrência de cada TAG $k$:
$$d(P_i, P_j) = \sqrt{\sum_{k=1}^{n} (w_{i,k} - w_{j,k})^2}$$

Pode ser calculada periodicamente por um Serviço de Domínio em Python através do seguinte agregador analítico em SQL:
```sql
-- Frequência de cada TAG por Prática
CREATE OR REPLACE VIEW vw_frequencia_tags_pratica AS
SELECT 
    ap.pratica_id,
    at.tag_id,
    COUNT(*)::NUMERIC AS frequencia_tag
FROM acoes_praticas ap
JOIN acoes_tags at ON ap.acao_id = at.acao_id
GROUP BY ap.pratica_id, at.tag_id;
```

---

## 5. Diretriz de Dados Editáveis (Adaptação ao CERNE 2025)

> [!IMPORTANT]
> **Taxonomia Dinâmica no Banco de Dados:** Nenhuma prática, código numérico ou peso de indicador deve ser hardcoded no código Python. As tabelas `niveis`, `processos_chave` e `praticas` são populadas através de scripts de migração (`migrations` / `fixtures`). Havendo alteração na lista de práticas do CERNE 2025 pela Anprotec, a base relacional será atualizada via instrução `INSERT` ou `UPDATE`, mantendo a estabilidade integral do backend e do frontend.

---

## 6. Decisões de Modelagem Pendentes de Homologação

1. **Campos Específicos para Ações de Extensão (CAEX/DICOM):**
   * Avaliar se a tabela `acoes` deve possuir campos dedicados para protocolo CAEX (*Comitê de Atividades de Extensão*) e solicitação de divulgação DICOM (*Divisão de Comunicação*), ou se esses metadados serão persistidos como atributos JSON em coluna `dados_adicionais JSONB`.
2. **Estratégia de Armazenamento de Arquivos no Neon:**
   * O banco Neon gerencia exclusivamente dados relacionais estruturados. A tabela `evidencias` armazenará apenas a URL pública/assinada e o hash de integridade, exigindo a aprovação do serviço de armazenamento em nuvem para os binários dos arquivos.
