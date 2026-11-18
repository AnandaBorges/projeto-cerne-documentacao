# 08 - Pendências, Decisões a Aprovar e Atualização CERNE 2025

## 1. Contexto da Transição Normativa: CERNE 2018 para CERNE 2025

De acordo com as diretrizes oficiais da Anprotec detalhadas no checklist original do projeto:
* **Vigência do CERNE 2018:** Encerrou-se formalmente em **junho de 2026**.
* **Entrada em Vigor do CERNE 2025:** O novo marco regulatório entrou em vigor em **fevereiro de 2026**.
* **Decisão Mandatória do Projeto:** O sistema tem como diretriz mandatória seguir o **CERNE 2025**. A relação de 29 práticas e 12 processos constantes na documentação inicial serve unicamente como **referência inicial de estudo e concepção**.
* **Impacto na Modelagem:** O detalhamento integral das práticas do CERNE 2025 não foi publicado abertamente e consta no *Manual de Implantação*. Como as práticas são tratadas como dados editáveis no PostgreSQL (e não constantes rígidas no código Python), o banco suportará atualização imediata assim que o manual for disponibilizado pela Anprotec.

---

## 2. Ação Prioritária Bloqueante

| Ação Prioritária | Responsáveis | Canal de Contato | Meta / Desbloqueio |
| :--- | :--- | :--- | :--- |
| **Solicitação Formal do Manual de Implantação do CERNE 2025** | Equipe de Alunos com mediação da Profa. Dra. Izabella Carneiro Bastos | Portal oficial da Anprotec ou Central Telefônica: **(61) 3202-1555** | Obtenção do documento oficial para conferir e atualizar a lista definitiva de níveis, processos, práticas e indicadores antes da carga final no PostgreSQL. |

---

## 3. Modificações Conhecidas do Modelo CERNE 2025

Informações já consolidadas pela Anprotec (divulgadas por meio de editais e do programa do curso oficial de capacitação) que devem ser absorvidas pelo sistema:

### 3.1. Estrutura dos Níveis
* **CERNE 1:** Sensibilização, prospecção, seleção, desenvolvimento e graduação de empreendimentos, além de gerenciamento básico (mantém a lógica do modelo anterior).
* **CERNE 2:** Gestão estratégica orientada a resultados, ampliação da atuação do mecanismo e avaliação sistemática do mecanismo de inovação (mantém a lógica do modelo anterior).
* **CERNE 3:** Relacionamento institucional, articulação em rede, desenvolvimento colaborativo em ecossistema e responsabilidade social e ambiental (mantém a lógica do modelo anterior).
* **CERNE 4:** Atuação internacional, inovação em processos próprios e maturidade do sistema de gestão da inovação. Como o CERNE 4 ganha temas novos além da internacionalização, **o total de práticas pode sofrer alteração quantitativa**.

### 3.2. Nova Geração de Indicadores
* **Impacto dos Empreendimentos:** Geração de empregos, investimento captado, adoção de práticas ESG (*Environmental, Social and Governance*), exportações e inserção internacional.
* **Efetividade da Gestão:** Metas estratégicas atingidas, satisfação dos empreendedores, tempo de execução dos processos e ciclo PDCA contínuo.
* **Articulação em Rede:** Grau de integração com universidades, ICTs e parceiros do ecossistema.
* **Sustentabilidade e Inclusão:** Uso consciente de recursos, projetos de diversidade e negócios de impacto social.

---

## 4. Matriz Formal de Decisões Técnicas e de Negócio Pendentes de Aprovação

O quadro abaixo discrimina as decisões normativas e de engenharia que dependem de validação formal com a orientadora (Profa. Dra. Izabella Carneiro Bastos) e com o professor responsável pela disciplina:

| ID | Dimensão | Decisão a Aprovar | Alternativas Técnicas Avaliadas | Recomendação Técnica | Impacto no Projeto |
| :---: | :--- | :--- | :--- | :--- | :--- |
| **D01** | Negócio / CERNE | **Conferência da taxonomia definitiva do CERNE 2025** antes do fechamento da Sprint 1. | **Opção A:** Utilizar as 29 práticas de 2018 como carga provisória de testes.<br>**Opção B:** Aguardar a liberação formal do Manual 2025 pela Anprotec. | **Opção A** (Carga provisória de 2018, viabilizando o avanço de desenvolvimento e testes sem bloqueio de cronograma). | Parametrização da fixture/seed inicial do banco de dados relacional. |
| **D02** | Negócio / Dados | **Definição dos atributos canônicos da entidade Ação:** Informações obrigatórias de cada evento ou ocorrência. | **Opção A:** Campos mínimos: título, data, tipo, status temporal, descrição, participantes e evidências.<br>**Opção B:** Inclusão de campos de extensão universitária (protocolo CAEX, comunicação DICOM, bolsas). | **Opção A** no núcleo com extensão via coluna JSONB flexível para metadados acadêmicos. | Define o schema da tabela `acoes` no PostgreSQL e validações no formulário Next.js. |
| **D03** | Backend / API | **Framework REST no Django:** DRF (*Django REST Framework*) vs. *Django Ninja*. | **Opção A:** DRF (Framework clássico, serializers declarativos).<br>**Opção B:** Django Ninja (Moderno, baseado em Pydantic, tipagem estática, OpenAPI nativo e alta performance). | **Opção B** (Django Ninja pela clareza na separação de DTOs da Clean Architecture). | Define a estrutura de roteamento e serialização da camada de interfaces da API. |
| **D04** | Infra / Nuvem | **Restrição de Conexão Externa no PythonAnywhere Free vs. Neon PostgreSQL.** | **Opção A:** Aquisição de conta *Hacker* (\$5/mês) no PythonAnywhere para liberação de portas TCP de saída.<br>**Opção B:** Acesso ao Neon via Neon Serverless HTTP API.<br>**Opção C:** Deploy alternativo do backend em PaaS gratuito com saída TCP irrestrita (ex: Render/Railway). | **Opção A** ou **Opção C** para manter conexão padrão Postgres (`psycopg3`/pooling). | Garante a viabilidade de comunicação direta entre backend e Neon. |
| **D05** | Frontend / UX | **Estratégia Primária de Coleta de Dados:** Formulários Web vs. Google Sheets. | **Opção A:** Manter o preenchimento manual em Google Sheets como canal primário com sincronização periódica.<br>**Opção B:** Utilizar formulários web em Next.js/shadcn/ui como canal primário, mantendo upload de CSV/Excel para importações em lote. | **Opção B** (Formulários web modernos oferecem validação imediata e eliminam perda de planilhas). | Define o fluxo operacional cotidiano dos operadores e bolsistas da incubadora. |
| **D06** | Armazenamento | **Armazenamento de Binários de Evidências (Arquivos Comprobatórios).** | **Opção A:** Salvar apenas links externos compartilhados (Google Drive / OneDrive da incubadora).<br>**Opção B:** Integração com armazenamento de objetos em nuvem (Cloudflare R2 / AWS S3) via URLs pré-assinadas. | **Opção A** na Fase 1 (sem custos de storage), migrando para **Opção B** na Fase 2. | Define a infraestrutura da tabela `evidencias` no PostgreSQL. |
| **D07** | Segurança / RBAC | **Perfil de Acesso Dedicado para Auditores da Certificação CERNE.** | **Opção A:** Controle lógico via interface (usuário com perfil `Auditor` só visualiza telas de consulta).<br>**Opção B:** Controle estrito via middleware no Django bloqueando requisições com métodos que alterem estado (`POST`, `PUT`, `DELETE`). | **Opção B** (Garante conformidade com o RNF de segurança e isolamento para auditores externos). | Homologa o padrão de segurança para o processo de certificação oficial. |
| **D08** | Algoritmo / Métricas| **Calibração dos Pesos da Equação [1] (Distância Euclidiana):** Frequência de ocorrência vs. Ponderação qualitativa. | **Opção A:** Frequência absoluta de TAGs por prática.<br>**Opção B:** Frequência normalizada com pesos qualitativos definidos pelos gestores em ciclos anuais. | **Opção B** (Permite calibração de acordo com as prioridades estratégicas da NidusTec). | Calibra a sensibilidade dos cálculos da matriz de influência e do gráfico radar. |
