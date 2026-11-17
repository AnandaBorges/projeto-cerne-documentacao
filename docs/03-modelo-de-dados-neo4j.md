# 03 - Modelagem de Dados com Neo4j

## 1. Justificativa do Paradigma em Grafo

O modelo CERNE possui uma estrutura inerentemente relacional e em camadas (Níveis, Processos, Práticas, Ações, Evidências, Eixos e Atores Externos). O uso de um banco de dados baseado em grafos (Neo4j) justifica-se pelos seguintes fatores centrais identificados no checklist do projeto:
* **Transversalidade Natural:** Uma única ação ou evento no cotidiano da incubadora atende e comprova múltiplas práticas de níveis distintos. Em grafos, isso é representado trivialmente como arestas direcionadas da ação para vários nós de prática.
* **Cumulatividade Estrutural:** A dependência rígida entre os níveis de certificação (CERNE 2 herda CERNE 1; CERNE 3 herda CERNE 2, etc.) é modelada explicitamente por relacionamentos entre os próprios níveis.
* **Cálculo de Proximidade e Influência:** A representação em grafo viabiliza o cálculo em tempo real de matrizes de adjacência e distâncias semânticas entre práticas a partir do compartilhamento de TAGs (Equação [1] do projeto).
* **Ausência de Esquema Rígido (Schemaless):** Permite adicionar novas propriedades e entidades conforme novas práticas do CERNE 2025 forem homologadas.

A base de dados local do projeto é formalmente identificada no checklist pelo nome operacional *"oceano pacífico"*.

---

## 2. Nós e Propriedades (Labels)

| Rótulo do Nó | Descrição | Propriedades Principais |
| :--- | :--- | :--- |
| `:Nivel` | Degrau de maturidade do CERNE. | `codigo` (int: 1, 2, 3, 4), `nome` (string: 'CERNE 1', 'CERNE 2', etc.) |
| `:ProcessoChave` | Agrupamento de processos dentro de cada nível. | `codigo` (string: '1.1', '1.2', etc.), `nome` (string: 'Sensibilização e prospecção', etc.) |
| `:Pratica` | Prática-chave e menor unidade contábil (indicador atômico). | `codigo` (string: '1.1.1', '1.1.2', etc.), `nome` (string), `procedimento` (string), `metrica` (string), `aprendizado` (string), `peso` (float) |
| `:Acao` | Ocorrência ou atividade executada na incubadora. | `id` (string/UUID), `titulo` (string), `data` (date), `tipo` (string), `descricao` (string), `status` (string: 'passada', 'presente', 'planejada') |
| `:Evidencia` | Registro comprobatório gerado pela ação. | `tipo` (string: 'ata', 'lista_presenca', 'foto', 'contrato'), `descricao` (string), `caminho_arquivo` (string) |
| `:Empresa` | Startup incubada ou graduada atendida. | `nome` (string), `status` (string: 'incubada', 'graduada'), `cnpj` (string), `data_ingresso` (date), `data_graduacao` (date) |
| `:Tag` | Rótulo semântico de senso comum associado à ação. | `nome` (string: 'palestra', 'mentoria', 'edital', 'maker') |
| `:AtorExterno` | Parceiro, mentor ou entidade governamental. | `nome` (string), `tipo` (string: 'mentor', 'universidade', 'orgao_publico', 'investidor') |

---

## 3. Relacionamentos e Tipologias de Vínculo

```
(:Nivel {codigo: 2}) ---[:EXIGE]---> (:Nivel {codigo: 1})
         ^
         | [:PERTENCE_A]
         |
    (:Pratica) <---[:EVIDENCIA]--- (:Acao) ---[:POSSUI_TAG]---> (:Tag)
         |                             |
         | [:INFLUENCIA]               | [:GERA]
         v                             v
    (:Pratica)                    (:Evidencia)
```

* `(:Nivel)-[:EXIGE]->(:Nivel)`: Exprime a dependência cumulativa entre os níveis da certificação.
* `(:Pratica)-[:PERTENCE_A]->(:Nivel)` (ou `(:Pratica)-[:COMPÕE]->(:ProcessoChave)-[:INTEGRA]->(:Nivel)`): Estrutura a taxonomia do modelo.
* `(:Acao)-[:EVIDENCIA]->(:Pratica)`: Núcleo contábil do sistema. Mapeia a comprovação prática das exigências do CERNE.
* `(:Acao)-[:GERA]->(:Evidencia)`: Vincula os arquivos e artefatos formais à ocorrência.
* `(:Acao)-[:POSSUI_TAG]->(:Tag)`: Conecta o vocabulário textual simples às ações para fins de classificação e cálculo de similaridade.
* `(:Empresa)-[:PARTICIPA_DE]->(:Acao)`: Registra as empresas beneficiadas pela ação (mentorias, capacitações, etc.).
* `(:Pratica)-[:INFLUENCIA {distancia: float, peso: float}]->(:Pratica)`: Vínculo automático calculado dinamicamente no grafo a partir da coocorrência de TAGs e similaridade semântica.

---

## 4. Diretriz Crítica: Modelagem Orientada a Dados Editáveis

> [!IMPORTANT]
> **Desacoplamento de Código e Banco:** As 29 práticas e os processos não devem ser fixados como constantes (*hardcoded*) no código-fonte Java. Eles devem ser mantidos exclusivamente como registros de nós no banco Neo4j. Desta forma, a migração e ajustes decorrentes da publicação definitiva do Manual de Implantação do CERNE 2025 ocorrerão unicamente via scripts de atualização de banco, sem a necessidade de alteração de classes ou recompilação do back-end.

---

## 5. Implementação da Equação [1]: Distância Euclidiana entre Práticas

O checklist define explicitamente a implementação da **Equação [1]** do projeto para mensurar a distância entre práticas no espaço vetorial das TAGs associadas:

$$d(P_i, P_j) = \sqrt{\sum_{k=1}^{n} (w_{i,k} - w_{j,k})^2}$$

* Onde $P_i$ e $P_j$ representam duas práticas distintas.
* $w_{i,k}$ é o peso ou frequência de ocorrência da $k$-ésima TAG associada às ações que evidenciam a prática $P_i$.
* $n$ é o total de TAGs distintas catalogadas no vocabulário.

Com base nessa métrica de distância, o sistema gera dinamicamente arestas `[:INFLUENCIA]` entre práticas, permitindo apontar no painel JavaFX quais práticas compartilham sinergia operacional ou quais apresentam lacunas de atuação conjunta.

---

## 6. Consultas Cypher Canônicas Especificadas

### 6.1. Cadastro dos Níveis e Práticas Iniciais (Passo 3)
```cypher
MERGE (n1:Nivel {codigo: 1, nome: 'CERNE 1'})
MERGE (p:Pratica {codigo: '1.1.1', nome: 'Sensibilização'})
MERGE (p)-[:PERTENCE_A]->(n1)

MERGE (n2:Nivel {codigo: 2, nome: 'CERNE 2'})
MERGE (n2)-[:EXIGE]->(n1);
```

### 6.2. Auditoria de Carga de Práticas
```cypher
MATCH (p:Pratica) 
RETURN count(p) AS total_praticas;
```

### 6.3. Contagem Automática de Evidências por Prática (Passo 7)
Esta consulta utiliza obrigatoriamente `OPTIONAL MATCH` para garantir a contagem e visualização de práticas que ainda possuem zero evidências cadastradas:
```cypher
MATCH (p:Pratica)
OPTIONAL MATCH (a:Acao)-[:EVIDENCIA]->(p)
RETURN p.codigo AS codigo, p.nome AS nome, count(a) AS evidencias
ORDER BY p.codigo;
```

---

## 7. Decisões de Modelagem Pendentes de Aprovação da Orientadora

As seguintes definições de modelagem de dados permanecem em aberto para validação com a Profa. Dra. Izabella Carneiro Bastos:

1. **Atributos Obrigatórios do Nó `:Acao`:**
   * O checklist estipula a realização de reunião inicial para *"definir com a professora quais informações cada ação precisa ter (data, tipo, participantes, práticas, evidências)"*.
   * *Pendente:* Definição dos campos exatos (ex.: tipologia de participantes — alunos, professores, comunidade externa; controle de carga horária; vínculo com projetos de extensão).
2. **Estrutura de Intermediação de Processos-Chave:**
   * *Pendente:* Definição se o grafo conterá formalmente nós do tipo `:ProcessoChave` ligando Práticas a Níveis (`(:Pratica)->[:COMPÕE]->(:ProcessoChave)->[:INTEGRA]->(:Nivel)`) ou se o código numérico da prática (`1.1.1`) será usado para inferir o processo via consulta.
3. **Mapeamento de Versionamento de Modelos:**
   * *Pendente:* Avaliar se as práticas do CERNE 2018 e do CERNE 2025 coexistirão na mesma base local com propriedade de versão (`versao: 2018` vs `versao: 2025`) para fins de histórico e análise comparativa acadêmica, ou se haverá substituição direta da carga de dados.
