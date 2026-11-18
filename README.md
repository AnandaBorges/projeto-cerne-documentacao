# Sistema Integrado de Gerenciamento das Práticas CERNE para Ambientes de Inovação

> **Documento Base:** Checklist do Projeto elaborado pela **Profa. Dra. Izabella Carneiro Bastos**, Professora Associada da Universidade Federal de Alfenas (**UNIFAL-MG**).  
> **Situação Atual:** Início do desenvolvimento integral a partir do zero. Não há protótipo, código-fonte nem dados pré-existentes.  
> **Arquitetura Oficial Mandatória:** Python/Django REST API + Clean Architecture + DDD (Domínio Rico) + TDD + PostgreSQL (Neon) + Next.js / Tailwind CSS / shadcn/ui (Vercel).

---

## 1. Visão Geral do Projeto

O **Sistema Integrado de Gerenciamento das Práticas CERNE** é uma solução de software web moderna concebida para automatizar o monitoramento, a consolidação e a auditoria dos indicadores das práticas-chave do modelo **CERNE** (*Centro de Referência para Apoio a Novos Empreendimentos* / Anprotec) em incubadoras e parques tecnológicos universitários.

O projeto tem como contexto prático a incubadora **NidusTec** (UNIFAL-MG), que conquistou a certificação CERNE 1 em 2022. O sistema substitui o controle manual e fragmentado (planilhas soltas e pastas no Google Drive) por uma plataforma web integrada com banco relacional gerenciado e arquitetura desacoplada, eliminando perdas históricas de dados decorrentes da rotatividade de bolsistas, mitigando retrabalho e permitindo a análise em tempo real da **transversalidade** entre práticas.

### Metas Estruturais
* **Maturidade Tecnológica:** Nível **TRL 6**, com meta de atingir **TRL 7** (validação operacional em ambiente real, incluindo testes com o CEFET e a NidusTec).
* **Propriedade Intelectual:** Registro formal de 1 software no INPI (*"Software para apoio parcial à gestão em inovação"*), sob titularidade da UNIFAL-MG.
* **Topologia Operacional em Nuvem:** Interface web moderna na Vercel consumindo backend REST no PythonAnywhere e banco relacional serverless PostgreSQL no Neon.

---

## 2. Tecnologias Utilizadas (Pilha Oficial)

A solução é construída exclusivamente com tecnologias de código aberto (*open-source*):

| Componente | Tecnologia | Função no Sistema |
| :--- | :--- | :--- |
| **Frontend & UI** | Next.js 14+ (App Router) + Tailwind CSS + shadcn/ui | Interface web responsiva, acessível e intuitiva para cadastro, auditoria e gráficos. |
| **Visualização Gráfica** | Recharts / Chart.js integrado a componentes React | Renderização interativa de gráficos do tipo Radar por nível e alertas visuais. |
| **Backend & API** | Python 3.12+ com Django + API REST (Django Ninja / DRF) | Exposição de endpoints REST, orquestração de casos de uso e autenticação JWT. |
| **Padrão Arquitetural** | Clean Architecture + DDD (*Domain-Driven Design*) | Separação estrita em 4 camadas com Domínio Rico em Python puro (*POPO*). |
| **Estratégia de Testes** | TDD (*Test-Driven Development*) com Pytest e FactoryBoy | Ciclo Red-Green-Refactor com cobertura de testes unitários $\ge 90\%$ no domínio. |
| **Banco de Dados** | PostgreSQL 16+ hospedado no Neon | Persistência relacional serverless com pooling (PgBouncer) e tabelas associativas ($N:N$). |
| **Hospedagem & Deploy** | Vercel (Frontend) + PythonAnywhere (Backend) + Neon (BD) | Infraestrutura distribuída em nuvem com alta disponibilidade e custo zero/mínimo. |

---

## 3. Diretrizes Críticas de Desenvolvimento

1. **Referência Provisória (CERNE 2018) vs. Padrão Definitivo (CERNE 2025):**  
   A relação de 29 práticas e 12 processos detalhada no início deste projeto provém do modelo CERNE 2018 (cuja vigência expirou em junho de 2026). O sistema segue mandatoriamente o **CERNE 2025** (em vigor desde fevereiro de 2026). As 29 práticas atuais constituem uma **referência inicial de estudo**; a taxonomia definitiva do banco depende da solicitação e conferência do *Manual de Implantação do CERNE 2025* junto à Anprotec (Passo 1 do checklist / Sprint 1).
2. **Modelo Dinâmico no Banco de Dados:**  
   Práticas e indicadores são persistidos como dados editáveis no PostgreSQL, e não engessados como constantes fixas no código Python, resguardando a capacidade de atualização normativa imediata sem recompilação ou redeploy.
3. **Cumulatividade e Transversalidade:**  
   A cumulatividade do modelo (CERNE 2 exige CERNE 1, etc.) é validada por regras de negócio de domínio e integridade referencial. Uma mesma ação prática pode evidenciar múltiplos indicadores simultaneamente (relação $1:N$), persistida na tabela associativa `acoes_praticas`.

---

## 4. Separação de Escopo e Prazos (Framework Scrum)

* **Primeira Versão Funcional (Prazo dos Alunos: 19/11/2026 a 19/02/2027):**  
   Estruturado em **6 Sprints quinzenais**: infraestrutura Neon/Vercel/PythonAnywhere instalada, domínio rico implementado com TDD, API REST documentada, importação de $\ge 30$ ações de teste sem duplicatas, contagem validada com `LEFT JOIN` (incluindo práticas com 0 evidências), interface Next.js com formulários e gráfico radar, e motor de regras para classificação por TAGs com a Equação [1] (distância euclidiana).
* **Etapas Posteriores e Projeto Global (Mês 1 a 31 – Abril/2026 a Outubro/2028):**  
   Treinamento da rede neural supervisionada (*grounding*), análise preditiva de séries temporais (Chen et al., 2013), módulos operacionais avançados do dia a dia (reserva de salas, eventos, CAEX/DICOM, parcerias, mentoria), módulo completo do Laboratório Maker (MAKERBot), testes beta expandidos no CEFET/NidusTec e depósito de patente/registro no INPI.

---

## 5. Índice da Documentação Técnica

A documentação detalhada do projeto está organizada em módulos na pasta [`docs/`](docs/):

1. [**01 - Visão Geral e Levantamento de Requisitos**](docs/01-visao-e-requisitos.md)  
   *Detalhamento do problema, justificativa institucional, requisitos funcionais (RF01 a RF11), requisitos não funcionais atualizados para web/cloud e divisão de escopo Scrum.*
2. [**02 - Arquitetura do Sistema e Tecnologias**](docs/02-arquitetura.md)  
   *Clean Architecture em camadas, Domain-Driven Design (DDD), topologia em nuvem (Vercel, PythonAnywhere, Neon), segurança e decisões pendentes.*
3. [**03 - Modelagem de Dados com PostgreSQL (Neon)**](docs/03-modelo-de-dados-postgresql.md)  
   *Paradigma relacional, DER conceitual, DDL completo no PostgreSQL, tabelas associativas para transversalidade ($N:N$), implementação da Equação [1] e queries SQL analíticas.*
4. [**04 - Design de Domínio Rico, DDD e Clean Architecture**](docs/04-design-dominio-ddd-clean-architecture.md)  
   *Estrutura de pacotes no backend Python, entidades e agregados ricos em comportamento, desacoplamento do Django ORM, casos de uso e contratos de repositório.*
5. [**05 - Regras de Negócio e Modelo CERNE**](docs/05-regras-de-negocio-cerne.md)  
   *Fundamentos do modelo CERNE, a tríade de elementos-chave (Procedimento, Métrica, Aprendizado), os 5 eixos do empreendimento, o ciclo PDCA e a tabela completa das 29 práticas.*
6. [**06 - Cronograma e Etapas de Desenvolvimento (Scrum)**](docs/06-cronograma-e-etapas.md)  
   *Planejamento ágil em 6 Sprints quinzenais para os 3 meses de desenvolvimento da equipe, ritos Scrum e cronograma global de 31 meses da UNIFAL-MG.*
7. [**07 - Plano de Testes e Estratégia de Qualidade (TDD)**](docs/07-plano-de-testes.md)  
   *Metodologia TDD (Red-Green-Refactor), testes unitários em Python puro com Pytest, testes de integração com Django e Neon, testes de interface Next.js e protocolo TRL 6/7.*
8. [**08 - Pendências, Decisões a Aprovar e Atualização CERNE 2025**](docs/08-pendencias-e-cerne-2025.md)  
   *Quadro formal de decisões normativas e arquiteturais pendentes de aprovação da Profa. Dra. Izabella Carneiro Bastos e do professor responsável.*

---

## 6. Governança e Decisões Pendentes de Validação

Nenhuma linha de código ou modificação estrutural definitiva deve ser implementada sem a convalidação prévia dos seguintes tópicos:
* Homologação do *Manual de Implantação do CERNE 2025* obtido com a Anprotec (Ação bloqueante da Sprint 1).
* Definição dos atributos canônicos e obrigatórios da entidade `Acao`.
* Homologação do framework REST do backend (Django REST Framework vs. Django Ninja).
* Resolução da conectividade externa do PythonAnywhere com a porta do Neon PostgreSQL.
* Definição da estratégia primária de coleta de dados (Formulários web no Next.js vs. Google Sheets legado).
* Definição do serviço de armazenamento em nuvem para binários de evidências (Cloudflare R2 / AWS S3 vs. Links externos).
