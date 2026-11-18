# 01 - Visão Geral e Levantamento de Requisitos

## 1. Identificação e Contexto do Projeto

* **Título do Projeto:** Sistema Integrado de Gerenciamento das Práticas CERNE para Ambientes de Inovação
* **Coordenação Geral:** Profa. Dra. Izabella Carneiro Bastos (Professora Associada da Universidade Federal de Alfenas – UNIFAL-MG)
* **Situação Atual:** O projeto inicia-se estritamente do zero. Não há código-fonte pré-existente, protótipo funcional, base de dados ou formulários operacionais. Banco de dados relacional, formulários web, componentes de interface, endpoints REST e dados simulados de teste devem ser construídos integralmente pela equipe técnica.
* **Contexto Institucional:** A incubadora NidusTec (UNIFAL-MG) adotou o modelo CERNE e conquistou a certificação **CERNE 1** em 2022. O sistema é concebido para operacionalizar, automatizar e consolidar a gestão dessas práticas e apoiar a incubadora na progressão para os níveis subsequentes (CERNE 2, 3 e 4).

## 2. Problema e Justificativa

Atualmente, o controle e monitoramento das práticas nas incubadoras universitárias ocorrem de forma manual e dispersa, apoiando-se em aplicativos de troca de mensagens e diretórios descentralizados no Google Drive. Esse cenário gera:
* **Perda de memória institucional:** Perda de registros e histórico de conformidade com a rotatividade contínua de bolsistas e estagiários.
* **Retrabalho e ineficiência:** Consolidação manual e redundante de dados no momento das auditorias de certificação.
* **Invisibilidade da transversalidade:** Dificuldade em identificar quando uma mesma atividade ou evento comprova simultaneamente múltiplos processos e práticas.
* **Curva de aprendizado íngreme:** Equipes recém-integradas não sabem como classificar ações cotidianas dentro dos indicadores formais do CERNE.

O software visa solucionar esses gargalos fornecendo escalabilidade, eliminação de duplicidades, classificação assistida e análise em tempo real da transversalidade entre práticas através de uma arquitetura web moderna, robusta e orientada a domínio.

## 3. Objetivos, Metas e Entregáveis

* **Objetivo Geral:** Construir um sistema web completo e desacoplado, com backend em **Python + Django (API REST)** estruturado sob **Clean Architecture, DDD (Domain-Driven Design), Domínio Rico e TDD**, banco de dados **PostgreSQL hospedado no Neon**, e interface web em **Next.js, Tailwind CSS e shadcn/ui** com deploy na **Vercel**, capaz de monitorar automaticamente os 29 indicadores atômicos das práticas-chave do modelo CERNE.
* **Meta de Prontidão Tecnológica:** Atingir o nível **TRL 6**, idealmente **TRL 7** (sistema validado em ambiente operacional real, com testes de homologação previstos na NidusTec/UNIFAL-MG e no CEFET).
* **Entregável Final de Propriedade Intelectual:** Registro de 1 software de computador no INPI sob a especificação *"Software para apoio parcial à gestão em inovação"*, cujo depósito oficial será efetuado pela UNIFAL-MG.
* **Horizonte Temporal:**
  * *Prazo da equipe de desenvolvimento (alunos):* 3 meses (19/11/2026 a 19/02/2027) estruturado no framework ágil **Scrum** (6 Sprints quinzenais) focado na entrega da primeira versão funcional.
  * *Ciclo global do projeto institucional:* 31 meses (abril de 2026 a outubro de 2028).

---

## 4. Separação de Escopo do Projeto (Visão Scrum)

Para assegurar a viabilidade técnica e a entrega contínua de valor, o escopo está organizado em dois grandes horizontes:

### 4.1. Escopo da Primeira Versão Funcional (Prazo: 19/02/2027 – Sprints 1 a 6)
Foco nos requisitos essenciais que garantem um fluxo operacional de ponta a ponta:
1. Modelagem relacional completa e persistência no **PostgreSQL (Neon)** da taxonomia do CERNE (níveis, processos e práticas como dados editáveis).
2. Construção do núcleo de **Domínio Rico** em Python puro (entidades, agregados, objetos de valor e serviços de domínio) guiado estritamente por **TDD** (*Test-Driven Development*).
3. Implementação dos Casos de Uso na camada de Aplicação e Repositórios na camada de Infraestrutura integrados ao ORM do Django.
4. Exposição de API REST documentada (OpenAPI/Swagger) no Django com deploy no **PythonAnywhere**.
5. Interface web responsiva em **Next.js, Tailwind CSS e shadcn/ui** com deploy na **Vercel**, contendo formulários de cadastro e importação em lote via CSV.
6. Cadastro e validação de no mínimo 30 ações de teste sem duplicatas.
7. Contagem automática de evidências por prática via consultas relacionais otimizadas (garantindo a contabilização de práticas com zero evidências).
8. Visualização gráfica interativa com destaque para práticas deficitárias e renderização do gráfico do tipo radar por nível.
9. Motor heurístico preliminar de classificação assistida baseado em vocabulário padronizado de TAGs.

### 4.2. Escopo das Etapas Posteriores (Ciclo Estendido até Mês 31)
Funcionalidades planejadas para a evolução pós-homologação da primeira versão:
1. Treinamento e avaliação da **Rede Neural** supervisionada com abordagem de *grounding* e indexação de rótulos semânticos.
2. Algoritmos preditivos de comportamento de indicadores baseados em séries temporais históricas (Chen et al., 2013).
3. Módulos operacionais avançados do dia a dia da incubadora (Seção 10 do checklist: agendamento com formulários automáticos de inscrição/presença, integração com Google Agenda, protocolo e agenda pública, submissões CAEX/DICOM, ofertas e demandas).
4. Módulo completo de prototipagem do Laboratório Maker (MAKERBot) (Seção 11 do checklist: cálculo volumétrico de insumos, tempo de máquina, custeio, bloqueio de sigilo, manutenção preventiva e rotinas de G-code).
5. Adaptação multi-incubadora e protocolo seguro de federação interinstitucional.
6. Testes beta em campo operacional estendido no CEFET e NidusTec (UNIFAL-MG).
7. Módulo completo de auditoria internacional e integração dos requisitos específicos do CERNE IV.
8. Empacotamento formal e submissão do registro de software no INPI.

---

## 5. Levantamento de Requisitos

### 5.1. Requisitos Funcionais (RF)

* **RF01 – Ingestão de Dados e Formulários Web:** O sistema deve fornecer formulários web intuitivos em Next.js para o cadastro direto de ações, startups, parceiros e evidências, mantendo funcionalidade de importação em lote via arquivos tabulares (CSV/Planilhas).
* **RF02 – Validação na Origem:** O sistema deve executar validações sintáticas e semânticas tanto no cliente (React Hook Form + Zod) quanto na API backend (DTOs/Serializers e Domínio Rico) antes da persistência.
* **RF03 – Rastreabilidade Temporal de Ações:** O sistema deve permitir o registro e recuperação de ações passadas, correntes e planejadas, suportando uma escala operacional superior a 100 ações por ano.
* **RF04 – Mapeamento da Transversalidade ($1:N$):** O sistema deve permitir que um único registro de ação comprove simultaneamente duas ou mais práticas de diferentes níveis ou processos-chave através de tabela associativa relacional.
* **RF05 – Cálculo Automatizado de Indicadores Atômicos:** O sistema deve calcular automaticamente a quantidade e o status dos indicadores correspondentes a cada prática a partir das ações e evidências cadastradas, incluindo explicitamente práticas com zero evidências (via `LEFT JOIN` / agregação).
* **RF06 – Cálculo de Similaridade e Interdependência (Equação [1]):** O sistema deve implementar o cálculo da distância euclidiana entre práticas a partir das TAGs associadas (Equação [1] do projeto), atualizando a matriz de influência e sinergia entre práticas.
* **RF07 – Ponderação e Normalização Dinâmica:** O sistema deve calcular indicadores aplicando pesos variáveis conforme a hierarquia qualitativa do modelo, permitindo a parametrização dessas ponderações.
* **RF08 – Classificação Assistida por TAGs:**
  * *Versão Inicial:* Motor de classificação determinístico com regras mapeando TAGs em linguagem simples às práticas correspondentes.
  * *Versão Futura:* Rede neural de classificação supervisionada (*grounding*), sugerindo a prática e colhendo a confirmação ou ajuste da equipe.
* **RF09 – Visualização Gráfica e Painel de Alertas:** A interface web deve apresentar a listagem de práticas com indicadores quantitativos, destacar visualmente (código de cores) práticas deficitárias e exibir a projeção de maturidade em gráficos do tipo radar interativos por nível.
* **RF10 – Relatórios Institucionais:** O sistema deve gerar relatórios consolidados em formatos padrão (PDF/CSV) alinhados às diretrizes formais de certificação do modelo CERNE.
* **RF11 – Acesso Dedicado a Auditores:** O sistema deve prever perfil de controle de acesso (RBAC) com permissão de somente leitura (*read-only*) para auditores externos do processo de certificação.

### 5.2. Requisitos Não Funcionais (RNF)

* **RNF01 – Licenciamento Aberto:** O sistema deve ser desenvolvido exclusivamente com ferramentas, linguagens e bibliotecas *open-source* (Python, Django, PostgreSQL, Next.js, Tailwind, shadcn/ui), sem custos de licenciamento proprietário.
* **RNF02 – Topologia Web Distribuída em Nuvem:** A solução deve operar desacoplada na nuvem: interface web na Vercel, backend REST no PythonAnywhere e banco de dados gerenciado no Neon PostgreSQL, permitindo acesso multiusuário via navegadores modernos.
* **RNF03 – Arquitetura Limpa e Independência de Framework:** A arquitetura do backend deve seguir estritamente os princípios da **Clean Architecture**, mantendo a camada de Domínio pura e desacoplada do Django e do banco de dados.
* **RNF04 – Design Orientado a Domínio (DDD) e Domínio Rico:** As entidades centrais do negócio devem encapsular suas próprias regras de validação, cálculos e estados, evitando o antipadrão de modelo de domínio anêmico.
* **RNF05 – Metodologia TDD e Cobertura de Testes:** O desenvolvimento do domínio e dos casos de uso deve seguir o ciclo *Test-Driven Development*, mantendo cobertura mínima de testes unitários superior a 90% na camada de domínio.
* **RNF06 – Segurança e Gestão de Segredos:** Credenciais de banco de dados (Neon connection string), chaves de API e variáveis de ambiente secretas jamais devem ser versionadas no código, sendo injetadas por variáveis de ambiente seguras na Vercel e no PythonAnywhere.
* **RNF07 – Performance e Latência:** As respostas dos endpoints da API REST no PythonAnywhere devem responder em até 500ms para consultas operacionais típicas, utilizando connection pooling eficiente no Neon.
* **RNF08 – Usabilidade Acessível (Linguagem de Senso Comum):** A interface desenvolvida com componentes shadcn/ui deve utilizar termos compreensíveis para colaboradores novatos, dispensando conhecimento aprofundado prévio das normas do CERNE para o registro de ações.
* **RNF09 – Integridade Relacional e Não Duplicação:** As rotinas de ingestão e persistência devem garantir a inexistência de registros redundantes por meio de restrições de unicidade (`UNIQUE constraints`) e índices no PostgreSQL.
