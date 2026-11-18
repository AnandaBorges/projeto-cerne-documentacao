# 06 - Cronograma e Etapas de Desenvolvimento (Scrum)

## 1. Visão Temporal Comparativa

O desenvolvimento do projeto divide-se em dois horizontes temporais formais:

1. **Ciclo Global Institucional do Projeto (31 Meses – Abril/2026 a Outubro/2028):**
   * Abrange o ciclo completo de pesquisa, testes de campo expandidos (NidusTec e CEFET), maturidade TRL 7 e o depósito de registro de software junto ao INPI pela UNIFAL-MG.
2. **Ciclo de Desenvolvimento da Equipe (3 Meses / 13 Semanas – 19/11/2026 a 19/02/2027):**
   * Organizado sob o framework ágil **Scrum**, estruturado em **6 Sprints quinzenais**, focado na entrega da primeira versão funcional completa na nova arquitetura (Next.js + Django REST + PostgreSQL Neon).

---

## 2. Planejamento das Sprints da Primeira Versão Funcional

```
+-----------+    +-----------+    +-----------+    +-----------+    +-----------+    +-----------+
| SPRINT 1  | -> | SPRINT 2  | -> | SPRINT 3  | -> | SPRINT 4  | -> | SPRINT 5  | -> | SPRINT 6  |
| Ambientes |    | Domínio   |    | API REST  |    | Frontend  |    | Radar e   |    | Testes E2E|
| e Neon BD |    | e TDD     |    | e Ingestão|    | Next.js   |    | Equação 1 |    | e Entrega |
+-----------+    +-----------+    +-----------+    +-----------+    +-----------+    +-----------+
```

| Sprint | Período | Objetivo da Sprint (Sprint Goal) | Principais Entregáveis (Incremento Funcional) |
| :---: | :--- | :--- | :--- |
| **Sprint 1** | **19/11 a 02/12/2026** | Setup da infraestrutura em nuvem, arquitetura base Django/Clean Arch e taxonomia CERNE no Neon. | • Banco Neon PostgreSQL configurado com connection pooling.<br>• Estrutura de pastas Clean Architecture no Django.<br>• Suíte inicial Pytest configurada.<br>• DDL e Migrations de Níveis, Processos e Práticas executadas.<br>• Seed inicial com as 29 práticas de referência. |
| **Sprint 2** | **03/12 a 16/12/2026** | Construção do Domínio Rico via TDD e persistência da transversalidade. | • Entidades puras Python (`Acao`, `Pratica`, `Evidencia`, `Empresa`) com testes unitários (>90% cobertura).<br>• Casos de uso de cadastro de ações e associação de evidências.<br>• Repositórios Django ORM implementando as interfaces do domínio.<br>• Suporte estrito à transversalidade $1:N$. |
| **Sprint 3** | **17/12 a 30/12/2026** | Desenvolvimento da API REST, documentação OpenAPI e ingestão de dados. | • Endpoints REST (Django Ninja / DRF) para Ações, Práticas e Indicadores.<br>• Autenticação JWT com controle de perfis (Admin, Operador, Auditor).<br>• Pipeline de importação em lote via CSV com validação de duplicatas.<br>• Pelo menos 30 ações de teste cadastradas e verificadas no Neon. |
| **Sprint 4** | **31/12 a 13/01/2027** | Conexão do Frontend Next.js na Vercel com formulários e listagem de práticas. | • Setup do Next.js 14+ com Tailwind CSS e componentes shadcn/ui na Vercel.<br>• Integração cliente-servidor via HTTPS com o backend no PythonAnywhere.<br>• Formulário web responsivo para cadastro de ações com validação Zod.<br>• Tabela interativa das práticas exibindo total de evidências (incluindo 0). |
| **Sprint 5** | **14/01 a 27/01/2027** | Visualização gráfica interativa (Radar) e cálculo da Equação [1] de similaridade. | • Gráfico do tipo Radar por Nível (Recharts) integrado à interface.<br>• Código de cores visual para práticas deficitárias (sem evidências).<br>• Implementação da Equação [1] (distância euclidiana a partir de TAGs).<br>• Sugestão automática de práticas para novas ações por motor de regras. |
| **Sprint 6** | **28/01 a 19/02/2027** | Bateria de testes de homologação, refinamentos de UX e apresentação final. | • Testes de integração de ponta a ponta (E2E) e testes de carga leve.<br>• Auditoria de conformidade com o checklist de requisitos.<br>• Manual de usuário e documentação técnica consolidada.<br>• Apresentação formal da versão funcional homologada para a orientadora. |

---

## 3. Cerimônias e Ritos da Equipe Scrum

* **Sprint Planning (Quinzenal – 2h):** Definição do Sprint Goal e seleção das histórias do Product Backlog com pontuação em *Story Points*.
* **Daily Scrum (Diária – 15min):** Alinhamento síncrono ou assíncrono: o que realizei ontem, o que realizarei hoje e quais impedimentos bloqueiam o avanço.
* **Sprint Review (Quinzenal – 1h):** Demonstração do incremento funcional de software em funcionamento para a Product Owner (orientadora).
* **Sprint Retrospective (Quinzenal – 45min):** Análise do processo de desenvolvimento, lições aprendidas e melhorias operacionais para a sprint seguinte.

---

## 4. Fases Estratégicas do Projeto Global (Mês 1 a Mês 31)

1. **Fase 1 – Análise e Adaptação (Mês 1 a 10):** Construção da primeira versão funcional web, validação inicial na NidusTec e conformidade com o modelo relacional.
2. **Fase 2 – Aprimoramento e IA (Mês 11 a 20):** Substituição do motor de regras por Rede Neural supervisionada (*grounding*), análise preditiva de séries temporais (Chen et al., 2013) e módulo do Laboratório Maker (MAKERBot).
3. **Fase 3 – Versão Beta e Validação de Campo (Mês 21 a 26):** Piloto operacional estendido no CEFET e NidusTec para atingir TRL 7.
4. **Fase 4 – Ajustes Finais e Internacionalização (Mês 27 a 31):** Módulos do CERNE IV, preparação da documentação e submissão formal do registro de software junto ao INPI pela UNIFAL-MG.
