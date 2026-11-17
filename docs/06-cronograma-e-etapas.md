# 06 - Cronograma e Etapas de Desenvolvimento

## 1. Visão Temporal Comparativa

O desenvolvimento do projeto divide-se em dois horizontes de tempo formalmente delimitados no checklist:

1. **Ciclo Global Institucional do Projeto (31 Meses – Abril/2026 a Outubro/2028):**
   * Abrange o ciclo completo desde a pesquisa preliminar até os testes de campo expandidos, transferência de tecnologia e o depósito de registro de software junto ao INPI pela UNIFAL-MG.
2. **Ciclo de Desenvolvimento da Equipe de Alunos (3 Meses / 13 Semanas – 19/11/2026 a 19/02/2027):**
   * Concentra-se na entrega de uma **primeira versão funcional** operacional de ponta a ponta (Passos 1 a 9 do checklist).

---

## 2. Cronograma Detalhado da Primeira Versão Funcional (19/11/2026 a 19/02/2027)

| Período | Etapa / Passos | Foco de Trabalho | Entrega da Semana (Marco) |
| :--- | :--- | :--- | :--- |
| **19/11 a 25/11/2026** | **Passos 1 e 2** | Compreensão do modelo CERNE, alinhamento com a coordenação e instalação do ambiente técnico (Git, JDK 21 LTS, IDE, Maven, JavaFX e Neo4j local). | Problema compreendido e ambiente local operacional. |
| **26/11 a 02/12/2026** | **Passo 3** | Aprendizado de Cypher e modelagem da taxonomia do CERNE no banco em grafo ("oceano pacífico"). | Níveis, processos e práticas persistidos e consultáveis no Neo4j. |
| **03/12 a 09/12/2026** | **Passo 4** | Configuração da classe de conexão Java com driver oficial do Neo4j e isolamento de credenciais. | Java conectado ao Neo4j, imprimindo contagem conferida de práticas. |
| **10/12 a 23/12/2026** | **Passo 5** | Concepção do diagrama de classes UML do zero, revisão com orientadora e codificação das classes de domínio. | Diagrama de classes aprovado pela orientadora e classes compiladas. |
| **24/12 a 13/01/2027** | **Passo 6** | *Ritmo ajustado (recesso de festas):* Criação de planilha modelo Google Sheets, exportação CSV e pipeline de ingestão no Java/Neo4j. | Pelo menos 30 ações de teste cadastradas no banco sem nós duplicados. |
| **14/01 a 20/01/2027** | **Passo 7** | Escrita de consultas Cypher para contagem de evidências por prática (com `OPTIONAL MATCH`) e validação analítica. | Contagem do sistema idêntica à conferência manual em planilha. |
| **21/01 a 03/02/2027** | **Passo 8** | Construção da primeira interface JavaFX exibindo a grade de práticas, cores de destaque para lacunas e gráfico do tipo radar. | Primeira tela funcional com dados reais e gráfico radar renderizado. |
| **04/02 a 10/02/2027** | **Passo 9** | Estruturação de vocabulário de TAGs em linguagem simples e motor de regras para classificação determinística de ações. | Classificação assistida por regras sugerindo práticas com alta taxa de acerto. |
| **11/02 a 19/02/2027** | **Finalização** | Bateria de testes de aceitação, consolidação da documentação técnica e apresentação final da versão funcional. | **Versão final funcional entregue e demonstrada.** |

---

## 3. Delimitação Clara: Versão Funcional vs. Etapas Posteriores

Para assegurar o cumprimento rigoroso dos prazos, as entregas ficam categorizadas da seguinte forma:

```
[ PRIMEIRA VERSÃO FUNCIONAL ]                    [ ETAPAS POSTERIORES ]
Prazo limite: 19/02/2027                         Ciclo Estendido (Fases 2, 3 e 4)
---------------------------------------          --------------------------------------
* Banco Neo4j estruturado ("oceano pacífico")    * Treinamento e tuning da Rede Neural
* Pipeline de ingestão via CSV / Sheets          * Análise preditiva histórica (Chen et al.)
* Mapeamento POO (Cerne1 a Cerne4)               * Módulos avançados (Seções 10 e 11)
* Cálculo automático de indicadores              * Módulo completo Laboratório Maker
* Interface JavaFX com lista e gráfico radar     * Piloto expandido no CEFET e NidusTec
* Classificação preliminar baseada em regras     * Depósito de registro de software no INPI
```

---

## 4. Fases Estratégicas do Projeto Global (Mês 1 a Mês 31)

O plano de longo prazo da etapa 1.1 define quatro fases sequenciais:

1. **Fase 1 – Análise e Adaptação:** Revisão de requisitos com a equipe da incubadora, definição do modelo em grafo no Neo4j e back-end Java com pipeline de dados funcionais.
2. **Fase 2 – Aprimoramento dos Algoritmos:** Treinamento da rede neural supervisionada, validação da normalização de pesos e execução de testes de desempenho documentados.
3. **Fase 3 – Versão Beta:** Expansão operacional com implantação e coleta de feedback em campo no CEFET e na NidusTec (UNIFAL-MG).
4. **Fase 4 – Ajustes Finais e Internacionalização:** Incorporação dos requisitos do CERNE IV, preparação do manual do usuário e depósito do registro de software no INPI pela UNIFAL-MG.

---

## 5. Rotina de Trabalho e Boas Práticas da Equipe

* **Reuniões Semanais de Ponto de Controle:** Reuniões curtas focadas em três perguntas: o que foi concluído, o que está travando o avanço e qual o compromisso para a próxima entrega.
* **Governança de Versionamento (Git):** Criação de repositório privado compartilhado com toda a equipe; histórico construído por meio de commits pequenos, frequentes e com mensagens semânticas claras.
* **Atualização Contínua da Documentação:** Manutenção contínua do `README.md` raiz com instruções claras de compilação, instalação e execução da aplicação em máquina local.
* **Política Rígida de Segurança de Dados:** Proibição irrestrita de cometer senhas de banco de dados, chaves de API ou dados cadastrais e confidenciais de empreendedores.
