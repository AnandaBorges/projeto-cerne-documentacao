# 08 - Pendências, Decisões a Aprovar e Atualização CERNE 2025

## 1. Contexto da Transição Normativa: CERNE 2018 para CERNE 2025

De acordo com as diretrizes oficiais da Anprotec detalhadas no checklist do projeto:
* **Vigência do CERNE 2018:** Encerrou-se formalmente em **junho de 2026**.
* **Entrada em Vigor do CERNE 2025:** O novo marco regulatório entrou em vigor em **fevereiro de 2026**.
* **Decisão do Projeto:** O sistema tem como diretriz mandatória seguir o **CERNE 2025**. A relação de 29 práticas e 12 processos constantes na documentação inicial serve unicamente como **referência inicial de estudo e concepção**.
* **Impacto no Escopo:** Como o detalhamento integral das práticas do CERNE 2025 não foi publicado abertamente e consta no *Manual de Implantação*, a taxonomia definitiva do banco de dados depende da disponibilização desse manual pela Anprotec antes do carregamento final do Passo 3.

---

## 2. Ação Imediata Pendente (Bloqueante para o Passo 3)

| Ação Prioritária | Responsáveis | Canal de Contato | Meta / Desbloqueio |
| :--- | :--- | :--- | :--- |
| **Solicitação Formal do Manual de Implantação do CERNE 2025** | Equipe de Alunos com mediação da Profa. Dra. Izabella Carneiro Bastos | Portal oficial da Anprotec ou Central Telefônica: **(61) 3202-1555** | Obtenção do documento oficial para conferir e atualizar a lista definitiva de níveis, processos, práticas e indicadores antes do cadastro no Neo4j. |

---

## 3. Modificações Conhecidas do Modelo CERNE 2025

Informações já consolidadas pela Anprotec (divulgadas por meio de editais e do programa do curso oficial de capacitação) que devem ser absorvidas pelo sistema:

### 3.1. Estrutura dos Níveis
* **CERNE 1:** Sensibilização, prospecção, seleção, desenvolvimento e graduação de empreendimentos, além de gerenciamento básico (mantém a lógica do modelo anterior).
* **CERNE 2:** Gestão estratégica orientada a resultados, ampliação da atuação do mecanismo e avaliação sistemática do mecanismo de inovação (mantém a lógica do modelo anterior).
* **CERNE 3:** Relacionamento institucional, articulação em rede, desenvolvimento colaborativo em ecossistema e responsabilidade social e ambiental (mantém a lógica do modelo anterior).
* **CERNE 4:** Atuação internacional, inovação em processos próprios e maturidade do sistema de gestão da inovação. Conforme pontuado textualmente no checklist original, como o CERNE 4 ganha temas novos além da internacionalização, **o total de 29 práticas pode mudar** (trata-se de uma possibilidade que carece de conferência direta no manual, não de uma certeza prévia de alteração quantitativa).

### 3.2. Nova Geração de Indicadores
* **Impacto dos Empreendimentos:** Geração de empregos, investimento captado, adoção de práticas ESG (*Environmental, Social and Governance*), exportações e internacionalização.
* **Efetividade da Gestão:** Metas estratégicas atingidas, satisfação dos empreendedores, tempo de execução dos processos e evidências de melhoria contínua (adotando explicitamente o ciclo PDCA).
* **Articulação em Rede:** Grau de integração com universidades, ICTs e parceiros do ecossistema de inovação.
* **Sustentabilidade e Impacto:** Uso consciente de recursos, projetos de diversidade, inclusão e negócios de impacto social.

---

## 4. Matriz de Decisões: Requisitos do Checklist vs. Alternativas Técnicas Propostas

Para assegurar o rigor conceitual e a governança do projeto, o quadro abaixo discrimina:
1. **Requisitos e Pendências do Checklist:** Obrigações, marcos e alinhamentos explicitamente demandados no checklist original elaborado pela orientadora.
2. **Alternativas Técnicas Propostas:** Hipóteses e opções de engenharia levantadas na documentação preliminar como possíveis caminhos de implementação para deliberação da orientadora.

| Item | Área | Requisito / Pendência Formal do Checklist | Alternativa Técnica Proposta na Documentação | Impacto no Projeto e Próximo Passo |
| :---: | :--- | :--- | :--- | :--- |
| **D01** | Negócio / CERNE | **Conferência da taxonomia oficial do CERNE 2025** antes do Passo 3, atualizando o que mudou em relação às 29 práticas de 2018 (Passo 1 e 3). | Adotar temporariamente as 29 práticas de 2018 como carga provisória de testes versus aguardar o retorno da Anprotec para realizar a primeira carga do banco. | Define a carga definitiva da taxonomia no Neo4j para validação com a orientadora. |
| **D02** | Modelagem de Dados | **Definição dos dados da entidade Ação:** *"Definir com a professora quais informações cada ação precisa ter (data, tipo, participantes, práticas, evidências)"* (Passo 1). | Proposta de propriedades no nó `:Acao`: título, data, tipo, status temporal (passada, presente, planejada), público participante e vínculos com evidências. | Parametriza o esquema de propriedades do nó `:Acao` e as colunas das planilhas de coleta. |
| **D03** | Arquitetura Java | **Revisão e aprovação formal do Diagrama de Classes UML:** *"Pronto quando: o diagrama de classes foi revisado pela orientadora e as classes compilam"* (Passo 5). | Modelagem da cumulatividade por herança estrutural de classes (`Cerne1` a `Cerne4`) e segregação das entidades `Pratica`, `Acao`, `Evidencia`, `Empresa` e `Tag`. | Condição obrigatória e bloqueante para o início da programação orientada a objetos no Passo 5. |
| **D04** | Interface (JavaFX) | **Renderização do Gráfico Radar por Nível:** *"O JavaFX não tem gráfico radar pronto: desenhar com Canvas ou usar uma biblioteca open-source"* (Passo 8). | **Opção A:** Desenhar o componente vetorial do radar do zero utilizando `javafx.scene.canvas.Canvas`.<br>**Opção B:** Adotar uma biblioteca externa open-source de gráficos compatível com JavaFX 21. | Define se haverá dependência externa adicional no `pom.xml` ou implementação própria no Passo 8. |
| **D05** | Pipeline de Ingestão | **Automação da leitura de dados das planilhas:** *"Depois de funcionar com CSV, automatizar a leitura direto do Google Sheets (Apps Script ou API do Google Sheets)"* (Passo 6). | **Opção A:** Consumo direto da API do Google Sheets via SDK em Java com credenciais de serviço.<br>**Opção B:** Criação de script de envio/exportação automatizado em JavaScript no Google Apps Script. | Define a arquitetura definitiva do pipeline de importação após a conclusão do teste com CSV. |
| **D06** | Algoritmos e Métricas | **Ponderação e ciclos de ajuste:** *"Normalização e pesos variáveis, respeitando a hierarquia qualitativa mapeada"* e *"Ajuste periódico dos pesos... ciclos de 12 meses"* (Item 4). | Formulação matemática de normalização e cálculo da matriz de influência baseada na **Equação [1]** (distância euclidiana a partir das TAGs). | Calibra a sensibilidade dos cálculos de maturidade e os vínculos dinâmicos entre práticas no grafo. |
| **D07** | Operação e Segurança | **Backup e Perfil de Auditoria:** *"Fazer backup do banco regularmente"*, espelhamento local e *"Acesso somente leitura para auditores CERNE"* (Passo 2 e Item 9). | **Opção A (Backup):** Script local de exportação (dump Cypher) versus cópia do diretório de dados.<br>**Opção B (Auditor):** Restrição lógica na interface JavaFX versus criação de usuário *read-only* nativo no Neo4j. | Assegura a integridade da base local *"oceano pacífico"* e atende aos requisitos de auditoria externa. |
