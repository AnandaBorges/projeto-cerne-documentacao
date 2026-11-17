# 01 - Visão Geral e Levantamento de Requisitos

## 1. Identificação e Contexto do Projeto

* **Título do Projeto:** Sistema Integrado de Gerenciamento das Práticas CERNE para Ambientes de Inovação
* **Coordenação Geral:** Profa. Dra. Izabella Carneiro Bastos (Professora Associada da Universidade Federal de Alfenas – UNIFAL-MG)
* **Situação Atual:** O projeto inicia-se estritamente do zero. Não há código-fonte pré-existente, protótipo funcional, base de dados ou planilhas prontas. Banco de dados, planilhas de coleta, interfaces e dados simulados de teste devem ser construídos integralmente pela equipe técnica.
* **Contexto Institucional:** A incubadora NidusTec (UNIFAL-MG) adotou o modelo CERNE e conquistou a certificação **CERNE 1** em 2022. O sistema é concebido para operacionalizar e consolidar a gestão dessas práticas e apoiar a incubadora na progressão para os níveis subsequentes (CERNE 2, 3 e 4).

## 2. Problema e Justificativa

Atualmente, o controle e monitoramento das práticas nas incubadoras universitárias ocorrem de forma manual e dispersa, apoiando-se em aplicativos de troca de mensagens e diretórios descentralizados no Google Drive. Esse cenário gera:
* **Perda de memória institucional:** Perda de registros e histórico de conformidade com a rotatividade contínua de bolsistas e estagiários.
* **Retrabalho e ineficiência:** Consolidação manual e redundante de dados no momento das auditorias de certificação.
* **Invisibilidade da transversalidade:** Dificuldade em identificar quando uma mesma atividade ou evento comprova simultaneamente múltiplos processos e práticas.
* **Curva de aprendizado íngreme:** Equipes recém-integradas não sabem como classificar ações cotidianas dentro dos indicadores formais do CERNE.

O software visa solucionar esses gargalos fornecendo escalabilidade, eliminação de duplicidades, classificação assistida e análise em tempo real da transversalidade entre práticas.

## 3. Objetivos, Metas e Entregáveis

* **Objetivo Geral:** Construir um sistema gerenciador de banco de dados (SGBD) integrado, com interface amigável e inteligência de classificação, capaz de monitorar automaticamente os indicadores atômicos das práticas-chave do modelo CERNE.
* **Meta de Prontidão Tecnológica:** Atingir o nível **TRL 6**, idealmente **TRL 7** (sistema validado em ambiente operacional real, com testes de homologação previstos na NidusTec/UNIFAL-MG e no CEFET).
* **Entregável Final de Propriedade Intelectual:** Registro de 1 software de computador no INPI sob a especificação *"Software para apoio parcial à gestão em inovação"*, cujo depósito oficial será efetuado pela UNIFAL-MG.
* **Horizonte Temporal:**
  * *Prazo da equipe de desenvolvimento (alunos):* 3 meses (19/11/2026 a 19/02/2027) focado na entrega da primeira versão funcional.
  * *Ciclo global do projeto institucional:* 31 meses (abril de 2026 a outubro de 2028).

---

## 4. Separação de Escopo do Projeto

Para garantir a viabilidade técnica e a execução dentro dos prazos, o escopo do projeto está formalmente dividido em duas fases distintas:

### Escopo da Primeira Versão Funcional (Prazo: 19/02/2027)
Foco nos **Passos 1 a 9** do checklist, assegurando um fluxo operacional de ponta a ponta:
1. Modelagem e persistência no banco em grafo (Neo4j) da hierarquia do CERNE (níveis, processos e práticas como dados editáveis).
2. Conexão do back-end em Java (JDK 21) ao Neo4j via driver oficial.
3. Criação do diagrama de classes e implementação da estrutura básica orientada a objetos (`Pratica`, `Nivel`, `Acao`, `Evidencia`, `Empresa`, `Tag`).
4. Pipeline inicial de coleta via planilha Google Sheets modelo, com importação via arquivo CSV e cadastro de no mínimo 30 ações de teste sem duplicatas.
5. Contagem automática de evidências por prática via consultas Cypher, validada contra conferência manual.
6. Interface gráfica preliminar em JavaFX exibindo a listagem de práticas, alertas visuais coloridos para práticas sem evidência e representação gráfica do tipo radar.
7. Classificação preliminar de ações através de motor determinístico baseado em vocabulário padronizado de TAGs (sem IA na versão inicial).

### Escopo das Etapas Posteriores (Pós-19/02/2027 e Ciclo Global até Mês 31)
Funcionalidades que ficam para etapas posteriores caso o prazo da primeira versão não comporte sua implementação:
1. Treinamento e avaliação da **Rede Neural** supervisionada com abordagem de *grounding* e indexação de rótulos (substituindo ou refinando o motor de regras).
2. Algoritmos preditivos de comportamento de indicadores baseados em séries históricas (Chen et al., 2013).
3. Módulos operacionais avançados do dia a dia da incubadora (Seção 10 do checklist: agendamento com formulários automáticos de inscrição/presença, reserva inteligente de salas com Google Agenda, protocolo e agenda pública, submissões CAEX/DICOM, ofertas e demandas).
4. Módulo completo de prototipagem do Laboratório Maker (MAKERBot) (Seção 11 do checklist: cálculo volumétrico de insumos, tempo de máquina, custeio, bloqueio de sigilo, manutenção preventiva e rotinas de G-code).
5. Adaptação multi-incubadora e protocolo seguro de compartilhamento interinstitucional.
6. Testes beta em campo operacional estendido no CEFET e NidusTec (UNIFAL-MG).
7. Módulo completo de auditoria internacional e integração dos requisitos específicos do CERNE IV.
8. Empacotamento formal e submissão do registro de software no INPI.

---

## 5. Levantamento de Requisitos

### 5.1. Requisitos Funcionais (RF)

* **RF01 – Ingestão de Dados por Planilhas:** O sistema deve coletar dados a partir de planilhas padronizadas (Google Sheets), estruturadas conforme as entidades de negócio (Ações, Startups Incubadas, Startups Graduadas, Parceiros).
* **RF02 – Validação na Origem:** O sistema deve contar com scripts de validação em JavaScript (Google Apps Script) para verificação sintática e semântica prévia no ambiente da planilha antes da exportação.
* **RF03 – Rastreabilidade Temporal de Ações:** O sistema deve permitir o registro e recuperação de ações passadas, correntes e planejadas, suportando uma escala operacional superior a 100 ações por ano.
* **RF04 – Mapeamento da Transversalidade:** O sistema deve permitir que um único registro de ação comprove simultaneamente duas ou mais práticas de diferentes níveis ou processos-chave (relação $1:N$).
* **RF05 – Cálculo Automatizado de Indicadores Atômicos:** O sistema deve calcular automaticamente a quantidade e o status dos indicadores correspondentes a cada prática a partir das ações e evidências cadastradas, incluindo explicitamente práticas com zero evidências.
* **RF06 – Cálculo de Similaridade e Interdependência (Equação [1]):** O sistema deve implementar o cálculo da distância euclidiana entre práticas a partir das TAGs associadas (Equação [1] do projeto), mapeando em tempo real como as práticas influenciam-se mutuamente no grafo.
* **RF07 – Ponderação e Normalização Dinâmica:** O sistema deve calcular indicadores aplicando pesos variáveis conforme a hierarquia qualitativa do modelo, permitindo a revisão periódica dessas ponderações em ciclos de 12 meses.
* **RF08 – Classificação Assistida por TAGs:**
  * *Versão Inicial:* Motor de classificação determinístico com regras mapeando TAGs em linguagem simples às práticas correspondentes.
  * *Versão Futura:* Rede neural de classificação supervisionada (grounding), sugerindo a prática e colhendo a confirmação ou ajuste da equipe.
* **RF09 – Visualização Gráfica e Painel de Alertas:** A interface gráfica deve apresentar a listagem de práticas com indicadores quantitativos, destacar visualmente (por código de cores) práticas deficitárias e exibir a projeção de maturidade em gráficos do tipo radar por nível.
* **RF10 – Relatórios Institucionais:** O sistema deve gerar relatórios consolidados alinhados às diretrizes formais de certificação do modelo CERNE.
* **RF11 – Acesso Dedicado a Auditores:** O sistema deve prever permissão de acesso em modo somente leitura (*read-only*) para auditores externos do processo de certificação.

### 5.2. Requisitos Não Funcionais (RNF)

* **RNF01 – Licenciamento Aberto:** O sistema deve ser desenvolvido exclusivamente com ferramentas, linguagens e bibliotecas *open-source*, sem custos de licenciamento proprietário.
* **RNF02 – Execução em Ambiente Local:** O software (banco de dados e aplicação) deve rodar integralmente na máquina local do usuário, sem necessidade de servidores dedicados ou infraestrutura em nuvem nesta etapa.
* **RNF03 – Isolamento e Segurança de Credenciais:** As credenciais de acesso ao banco de dados jamais devem constar no código-fonte ou no repositório de versionamento, devendo ser lidas via arquivo de configuração externo ou variáveis de ambiente.
* **RNF04 – Privacidade e Não Exposição de Dados:** Dados pessoais de empreendedores e informações confidenciais de planos de negócio não devem ser commitados no repositório do projeto.
* **RNF05 – Usabilidade Acessível (Linguagem de Senso Comum):** Os formulários de entrada de dados devem utilizar termos compreensíveis para colaboradores novatos, dispensando conhecimento aprofundado prévio das normas do CERNE para o registro de ações.
* **RNF06 – Flexibilidade Estrutural (Desacoplamento do Modelo):** A representação dos níveis, processos e práticas deve ser tratada como nós e propriedades dinâmicas no banco em grafo, viabilizando a adaptação ao CERNE 2025 e versões futuras sem necessidade de recompilação do código.
* **RNF07 – Integridade de Grafo e Não Duplicação:** As rotinas de ingestão e persistência devem garantir a inexistência de registros, nós ou propriedades redundantes no grafo.
* **RNF08 – Backup e Resiliência Local:** O sistema deve dispor de procedimento e rotina clara de backup local regular e espelhamento da base de dados do Neo4j.
