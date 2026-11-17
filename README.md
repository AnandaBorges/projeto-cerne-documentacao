# Sistema Integrado de Gerenciamento das Práticas CERNE para Ambientes de Inovação

> **Documento Base:** Checklist do Projeto elaborado pela **Profa. Dra. Izabella Carneiro Bastos**, Professora Associada da Universidade Federal de Alfenas (**UNIFAL-MG**).  
> **Situação Atual:** Início do desenvolvimento integral a partir do zero. Não há protótipo, código-fonte nem dados pré-existentes.

---

## 1. Visão Geral do Projeto

O **Sistema Integrado de Gerenciamento das Práticas CERNE** é uma solução de software concebida para automatizar o monitoramento, a consolidação e a auditoria dos indicadores das práticas-chave do modelo **CERNE** (*Centro de Referência para Apoio a Novos Empreendimentos* / Anprotec) em incubadoras e parques tecnológicos universitários.

O projeto tem como contexto prático a incubadora **NidusTec** (UNIFAL-MG), que obteve a certificação CERNE 1 em 2022. O sistema substitui o controle manual e fragmentado (planilhas soltas e pastas no Google Drive) por um SGBD integrado baseado em grafos, eliminando perdas históricas de dados decorrentes da rotatividade de bolsistas, mitigando retrabalho e permitindo a análise em tempo real da **transversalidade** entre práticas.

### Metas Estruturais
* **Maturidade Tecnológica:** Nível **TRL 6**, com meta de atingir **TRL 7** (validação operacional em ambiente real, incluindo testes com o CEFET e a NidusTec).
* **Propriedade Intelectual:** Registro formal de 1 software no INPI (*"Software para apoio parcial à gestão em inovação"*), sob titularidade da UNIFAL-MG.
* **Topologia Operacional:** Aplicação e banco de dados rodando localmente no mesmo computador, sem custos com servidores em nuvem.

---

## 2. Tecnologias Utilizadas

A solução é construída exclusivamente com ferramentas de código aberto (*open-source*):

| Componente | Tecnologia | Função no Sistema |
| :--- | :--- | :--- |
| **Coleta de Dados** | Google Sheets + Google Apps Script (JS) | Entrada amigável de dados por formulários com validação na origem. |
| **Back-end** | Java SE (JDK 21 LTS) + Apache Maven | Regras de negócio, cálculo de indicadores e orquestração do pipeline. |
| **Banco de Dados** | Neo4j Desktop / Community + Cypher | Armazenamento em grafo da taxonomia CERNE, ações e transversalidade (base *"oceano pacífico"*). |
| **Interface Gráfica** | JavaFX (versão 21) | Exibição da grade de práticas, cores de alerta para lacunas e gráficos tipo radar. |
| **Classificação Inteligente** | TAGs + Motor Heurístico / Rede Neural | Sugestão assistida de práticas para novas ações cadastradas. |

---

## 3. Diretrizes Críticas de Desenvolvimento

1. **Referência Provisória (CERNE 2018) vs. Padrão Definitivo (CERNE 2025):**  
   A relação de 29 práticas e 12 processos detalhada no início deste projeto provém do modelo CERNE 2018 (cuja vigência expirou em junho de 2026). O sistema segue mandatoriamente o **CERNE 2025** (em vigor desde fevereiro de 2026). As 29 práticas atuais constituem uma **referência inicial de estudo**; a taxonomia definitiva do banco depende da solicitação e conferência do *Manual de Implantação do CERNE 2025* junto à Anprotec (Passo 1 do checklist).
2. **Modelo Dinâmico no Grafo:**  
   Práticas e indicadores são persistidos como dados editáveis no Neo4j, e não engessados como constantes fixas no código Java, resguardando a capacidade de atualização normativa imediata.
3. **Cumulatividade e Transversalidade:**  
   A herança do modelo (CERNE 2 exige CERNE 1, etc.) é representada no grafo e nas classes Java. Uma mesma ação prática pode evidenciar múltiplos indicadores simultaneamente (relação $1:N$).

---

## 4. Separação de Escopo e Prazos

* **Primeira Versão Funcional (Prazo dos Alunos: 19/11/2026 a 19/02/2027):**  
  Foco na entrega dos **Passos 1 a 9** do checklist: ambiente instalado, taxonomia inicial no Neo4j, conexão Java via driver oficial, classes de domínio básicas compilando, importação de >= 30 ações de teste via CSV sem duplicações, consultas Cypher com contagem validada contra conferência manual, primeira tela JavaFX com gráfico radar e classificação de ações por TAGs via motor de regras determinístico.
* **Etapas Posteriores e Projeto Global (Mês 1 a 31 – Abril/2026 a Outubro/2028):**  
  Treinamento da rede neural supervisionada (*grounding*), análise preditiva de séries temporais (Chen et al., 2013), módulos operacionais avançados do dia a dia (reserva de salas, eventos, CAEX/DICOM, parcerias, mentoria), módulo completo do Laboratório Maker (MAKERBot), testes beta expandidos no CEFET/NidusTec e depósito de patente/registro no INPI.

---

## 5. Índice da Documentação Técnica

A documentação detalhada do projeto está organizada em módulos na pasta [`docs/`](docs/):

1. [**01 - Visão Geral e Levantamento de Requisitos**](docs/01-visao-e-requisitos.md)  
   *Detalhamento do problema, justificativa institucional, requisitos funcionais (RF01 a RF12), requisitos não funcionais (RNF01 a RNF08) e separação estrita de escopo.*
2. [**02 - Arquitetura do Sistema e Tecnologias**](docs/02-arquitetura.md)  
   *Visão arquitetural em 6 camadas, fluxo de dados, modelo de implantação monolítica local e decisões arquiteturais pendentes de aprovação da orientadora.*
3. [**03 - Modelagem de Dados com Neo4j**](docs/03-modelo-de-dados-neo4j.md)  
   *Paradigma em grafo, catálogo de nós e relacionamentos, modelagem orientada a dados editáveis, formulação matemática da Equação [1] (distância euclidiana) e consultas Cypher canônicas.*
4. [**04 - Estrutura de Classes em Java**](docs/04-estrutura-classes-java.md)  
   *Padrões de POO com JDK 21 LTS e Maven, modelagem da cumulatividade dos níveis por herança (`Cerne1` a `Cerne4`), entidades do domínio e isolamento seguro de credenciais.*
5. [**05 - Regras de Negócio e Modelo CERNE**](docs/05-regras-de-negocio-cerne.md)  
   *Fundamentos do modelo CERNE, a tríade de elementos-chave (Procedimento, Métrica, Aprendizado), os 5 eixos do empreendimento, o ciclo PDCA e a tabela completa das 29 práticas de referência inicial com evidências auditáveis.*
6. [**06 - Cronograma e Etapas de Desenvolvimento**](docs/06-cronograma-e-etapas.md)  
   *Planejamento temporal comparativo (ciclo institucional de 31 meses vs. cronograma dos alunos de 13 semanas), marcos semanais e rotina ágil de trabalho da equipe.*
7. [**07 - Plano de Testes e Critérios de Aceitação**](docs/07-plano-de-testes.md)  
   *Critérios de aceitação objetivos ("Pronto quando" dos Passos 1 a 9), testes de integridade de dados (ausência de duplicatas), testes de consultas `OPTIONAL MATCH` e protocolo para TRL 6/7.*
8. [**08 - Pendências, Decisões a Aprovar e Atualização CERNE 2025**](docs/08-pendencias-e-cerne-2025.md)  
   *Quadro formal de decisões que dependem de aprovação da Profa. Dra. Izabella Carneiro Bastos, protocolo de solicitação do Manual CERNE 2025 à Anprotec e novas diretrizes de governança (ESG, PDCA, processos próprios).*

---

## 6. Governança e Decisões Pendentes de Validação

Nenhuma linha de código ou modificação estrutural definitiva deve ser implementada sem a convalidação prévia dos seguintes tópicos com a orientadora:
* Homologação do *Manual de Implantação do CERNE 2025* obtido com a Anprotec.
* Definição dos atributos canônicos e obrigatórios do nó `:Acao`.
* Revisão formal e aprovação do Diagrama de Classes UML (Passo 5).
* Homologação do componente de renderização para o Gráfico Radar no JavaFX.
* Estratégia definitiva de automação do Google Sheets (API direta vs. Apps Script).
* Formulação dos pesos variáveis e regras de normalização dos indicadores.
