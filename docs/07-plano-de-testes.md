# 07 - Plano de Testes e Critérios de Aceitação

## 1. Estratégia de Qualidade e Homologação

A validação do sistema orienta-se pelo cumprimento rigoroso dos critérios objetivos de conclusão definidos no checklist (*"Pronto quando"*). Cada passo do desenvolvimento possui um marco mensurável que deve ser comprovado antes do início da etapa subsequente.

Adicionalmente, para atender à meta de maturidade tecnológica **TRL 6 / TRL 7**, o plano contempla testes de usabilidade operacional com operadores reais da incubadora NidusTec (UNIFAL-MG) e parceiros externos (CEFET).

---

## 2. Matriz de Critérios de Aceitação ("Pronto quando")

| Etapa | Procedimento de Verificação | Critério Formal de Aceitação ("Pronto quando") | Tipo de Teste |
| :---: | :--- | :--- | :--- |
| **Passo 1** | Arguição conceitual entre os membros e redação do fluxo de uma ação real do evento à comprovação. | Cada aluno da equipe consegue explicar com clareza o que é uma prática-chave, o que constitui uma evidência e como funciona a transversalidade. | Qualitativo / Conceitual |
| **Passo 2** | Execução da aplicação Java básica e inicialização do serviço local do banco. | Uma janela vazia do JavaFX é renderizada na tela e a interface web do Neo4j Browser abre com sucesso em `http://localhost:7474`. | Teste de Ambiente / Fumaça (*Smoke Test*) |
| **Passo 3** | Execução de script Cypher de carga da taxonomia do CERNE no Neo4j Browser. | A consulta Cypher `MATCH (p:Pratica) RETURN count(p)` devolve com exatidão o número total de práticas cadastradas. | Validação de Banco de Dados |
| **Passo 4** | Execução do método `main` da classe Java de integração com o driver Bolt. | O programa Java conecta ao banco e imprime no console exatamente o mesmo total de práticas verificado no banco de dados. | Teste de Integração (Java ↔ Neo4j) |
| **Passo 5** | Apresentação do diagrama UML para a coordenação e compilação do projeto com `mvn clean compile`. | O diagrama de classes foi formalmente revisado pela orientadora e todas as classes do modelo compilam sem erros de dependência. | Revisão de Design e Compilação |
| **Passo 6** | Execução do script de importação da planilha modelo com ações simuladas. | Pelo menos 30 ações de teste estão registradas e consultáveis no banco, com verificação de zero nós `:Acao` duplicados. | Ingestão e Integridade de Dados |
| **Passo 7** | Execução da consulta de contagem ponderada de evidências confrontada com contagem manual em planilha. | A contagem devolvida pela consulta Cypher (`OPTIONAL MATCH`) bate com 100% de exatidão em relação à contagem manual. | Teste de Regra de Negócio / Acurácia |
| **Passo 8** | Apresentação da tela JavaFX de indicadores e radar para colaboradores da incubadora. | A equipe da incubadora abre a interface gráfica e compreende imediatamente, sem explicações técnicas, quais práticas estão deficientes. | Teste de Usabilidade (UI/UX) |
| **Passo 9** | Inserção de novas ações com TAGs e comparação entre sugestão do sistema e intenção do usuário. | O sistema sugere automaticamente a classificação da ação em práticas-chave e a equipe concorda na maioria das vezes. | Teste de Acurácia do Motor de Regras |

---

## 3. Protocolos de Teste Detalhados

### 3.1. Teste de Integridade e Ausência de Duplicatas (Passo 6)
Para garantir que a ingestão de dados não gere redundâncias no grafo "oceano pacífico", deve ser executada a consulta de verificação de duplicidade:

```cypher
MATCH (a:Acao)
WITH a.id AS idAcao, count(*) AS totalOcorrencias
WHERE totalOcorrencias > 1
RETURN idAcao, totalOcorrencias;
```
*Critério de Sucesso:* A consulta deve retornar conjunto vazio de resultados.

### 3.2. Teste de Conformidade de Contagem com Zero Evidências (Passo 7)
O sistema deve assegurar que práticas sem nenhuma ação associada não sejam omitidas dos relatórios e do cálculo do radar:

```cypher
MATCH (p:Pratica)
OPTIONAL MATCH (a:Acao)-[:EVIDENCIA]->(p)
WITH p, count(a) AS totalEvidencias
RETURN p.codigo, p.nome, totalEvidencias
ORDER BY totalEvidencias ASC;
```
*Critério de Sucesso:* Todas as práticas cadastradas devem figurar na listagem; aquelas sem apontamentos devem exibir valor `0` de forma íntegra.

### 3.3. Testes em Ambiente Operacional Real (TRL 6 e TRL 7)
Previstos para as fases subsequentes de consolidação do sistema:
* **Validação Operacional na NidusTec (UNIFAL-MG):** Execução do sistema com o histórico real dos anos de operação da incubadora e avaliação com os gestores locais.
* **Validação Cruzada no CEFET:** Teste de aderência e portabilidade do software em ambiente de incubação parceiro, certificando que a parametrização do sistema atende a diferentes realidades de inovação.
