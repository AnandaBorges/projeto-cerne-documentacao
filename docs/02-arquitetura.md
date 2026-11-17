# 02 - Arquitetura do Sistema e Tecnologias

## 1. Visão Arquitetural dos 6 Componentes

O sistema é concebido como uma solução modular multicamadas fundamentada exclusivamente em tecnologias de código aberto (*open-source*), integrando ferramentas de produtividade cotidianas com um ecossistema de processamento e persistência baseado em grafos.

```
+-------------------------------------------------------------------------+
|                              CAMADA DE COLETA                           |
|      Planilhas Google Sheets + Scripts de Validação (Apps Script / JS)  |
+-------------------------------------------------------------------------+
                                    | (Exportação CSV / API)
                                    v
+-------------------------------------------------------------------------+
|                             CAMADA DE BACK-END                          |
|             Java SE (JDK 21 LTS) - Arquitetura Modular POO              |
|        Driver Oficial Neo4j Java (5.x) | Lógica e Motor de Regras       |
+-------------------------------------------------------------------------+
         |                                                 ^
         | (Consultas Cypher / Transações)                 | (Metas / Dados)
         v                                                 v
+-------------------------------------+   +-------------------------------+
|       BANCO DE DADOS EM GRAFO       |   |       INTERFACE GRÁFICA       |
| Neo4j Desktop/Community (Base Local |   |            JavaFX 21          |
|         "Oceano Pacífico")          |   |  Gráficos Radar, Painéis e    |
|   Nós, Relacionamentos e Cypher     |   |          Relatórios           |
+-------------------------------------+   +-------------------------------+
         ^
         | (Indexação semântica e similaridade)
+-------------------------------------------------------------------------+
|                        CAMADA DE INTELIGÊNCIA                           |
|       Versão Inicial: Classificação Determinística por TAGs             |
|       Versão Futura: Rede Neural Supervisionada (Grounding)             |
+-------------------------------------------------------------------------+
```

---

## 2. Detalhamento dos Componentes e Tecnologias

### 2.1. Camada de Coleta (Google Sheets + Google Apps Script)
* **Tecnologia:** Google Sheets, Google Apps Script (JavaScript moderno).
* **Finalidade:** Servir como porta de entrada simplificada para os operadores da incubadora. Reduz a barreira técnica de entrada através do uso de vocabulário de senso comum nos formulários de cadastro.
* **Responsabilidade:** Validação de tipos de campos (datas, formatos numéricos, campos obrigatórios) antes de liberar os dados para consumo pelo back-end.

### 2.2. Camada de Back-end (Java 21 LTS)
* **Tecnologia:** Java Development Kit 21 (versão LTS com estabilidade e longo suporte), gerenciador de dependências Apache Maven.
* **Componente de Conexão:** `org.neo4j.driver:neo4j-java-driver` (versão 5.x mais recente do repositório Maven Central).
* **Responsabilidade:** Orquestração do pipeline de dados, execução da lógica de negócios, cálculo das métricas quantitativas e qualitativas, aplicação da Equação [1] de distância euclidiana, e coordenação das chamadas ao banco via Cypher.

### 2.3. Camada de Banco de Dados em Grafo (Neo4j)
* **Tecnologia:** Neo4j Desktop / Neo4j Community Edition local; linguagem declarativa de consulta Cypher.
* **Identificação:** Base de dados local apelidada no planejamento como *"oceano pacífico"*.
* **Responsabilidade:** Armazenamento natural das inter-relações complexas, transversalidade das ações, herança estrutural entre níveis do CERNE e cálculos dinâmicos de proximidade em tempo real.

### 2.4. Camada de Inteligência e Classificação
* **Tecnologia:** Motor de regras heurísticas baseado em vocabulário padronizado de TAGs (etapa inicial dos alunos) com projeção de expansão para Rede Neural supervisionada (técnica de *grounding*).
* **Fundamentação:** Incorporação futura de modelos de análise preditiva orientada a séries históricas (referenciando Chen et al., 2013).
* **Responsabilidade:** Sugerir automaticamente as práticas-chave correspondentes a uma ação recém-cadastrada a partir dos rótulos e descrição fornecidos.

### 2.5. Camada de Interface com o Usuário (JavaFX)
* **Tecnologia:** JavaFX Controls (versão 21 LTS).
* **Responsabilidade:** Exibição da listagem de práticas com volumetria de evidências, marcação visual colorida de práticas deficitárias ou com zero comprovações, e renderização de diagramas do tipo radar agrupados por nível de maturidade.

### 2.6. Pipeline de Integração e Ingestão
* **Tecnologia:** Parser interno em Java de arquivos CSV (etapa inicial) com evolução planejada para consumo direto via chamadas autenticadas na API do Google Sheets.
* **Responsabilidade:** Transformação de linhas tabulares de formulários em nós estruturados e relacionamentos de grafo no Neo4j, assegurando a idempotência (eliminação de duplicações).

---

## 3. Modelo de Implantação e Infraestrutura

* **Topologia Monolítica Local:** Tanto a aplicação compilada em Java quanto a instância local do Neo4j residem obrigatoriamente na mesma máquina física do operador (`localhost:7687` para conexão Bolt e `http://localhost:7474` para o Neo4j Browser).
* **Restrição de Custo e Infraestrutura:** Ausência intencional de servidores em nuvem, bancos gerenciados ou plataformas pagas, mantendo o custo de hospedagem e manutenção em zero.
* **Segurança de Acesso e Isolamento Local:**
  * As credenciais do Neo4j são parametrizadas por variável de ambiente (`NEO4J_SENHA`) ou em arquivo de configuração mantido fora da árvore do Git (`.gitignore`).
  * Nenhum dado confidencial ou dado pessoal protegido pela LGPD deve trafegar em canais abertos ou ser enviado para repositórios remotos.

---

## 4. Decisões de Arquitetura Pendentes de Aprovação da Orientadora

Conforme estipulado no planejamento, as seguintes definições de arquitetura e tecnologia encontram-se em aberto e exigem validação prévia com a Profa. Dra. Izabella Carneiro Bastos:

1. **Mecanismo de Gráfico Radar no JavaFX:**
   * *Contexto:* O JavaFX 21 não disponibiliza componente nativo para gráficos de teia/radar (*spider/radar chart*).
   * *Decisão a aprovar:* Definição entre implementação manual de um componente vetorial desenhado via `javafx.scene.canvas.Canvas` ou incorporação de biblioteca de terceiros de código aberto compatível com JavaFX.
2. **Estratégia de Integração do Pipeline Google Sheets:**
   * *Contexto:* O Passo 6 inicia-se via exportação e leitura de arquivos `.csv`.
   * *Decisão a aprovar:* Definição sobre qual caminho de automação definitiva será adotado: (a) consumo direto da API do Google Sheets via credenciais de serviço, ou (b) disparo de webhook/exportador automatizado desenvolvido em Google Apps Script.
3. **Estratégia de Resiliência e Backup do Neo4j:**
   * *Contexto:* Como o sistema roda localmente, falhas de disco ou corrupção de banco acarretam perda de dados.
   * *Decisão a aprovar:* Formalização do protocolo de backup (rotina manual de cópia da pasta de dados do Neo4j versus script de automação de dump via ferramentas CLI do Neo4j).
4. **Arquitetura de Isolamento para Auditores:**
   * *Contexto:* O checklist estipula acesso do tipo *"somente leitura"* para avaliadores do CERNE.
   * *Decisão a aprovar:* Validação se este perfil de auditor será controlado por permissões nativas de usuário do Neo4j Enterprise/Community ou controlado logicamente pela interface do JavaFX.
