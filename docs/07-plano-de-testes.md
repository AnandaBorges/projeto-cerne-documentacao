# 07 - Plano de Testes e Estratégia de Qualidade (TDD)

## 1. Diretrizes de Engenharia de Qualidade e Metodologia TDD

A qualidade do software orienta-se pela adoção estrita do **Test-Driven Development (TDD)** e pela pirâmide de testes de software. Nenhuma funcionalidade de domínio ou caso de uso é implementada antes da escrita de seu respectivo teste automatizado.

### 1.1. O Ciclo Red-Green-Refactor no Backend Python
1. **Red (Escrever o teste com falha):** Cria-se um teste no `pytest` definindo o comportamento esperado da entidade de domínio ou caso de uso. O teste deve falhar pelas razões corretas.
2. **Green (Fazer o teste passar):** Escreve-se a implementação mais simples possível no código de produção para que o teste seja aprovado.
3. **Refactor (Aprimorar o código):** Reestrutura-se o código, eliminando duplicações, aplicando padrões de projeto da Clean Architecture e garantindo que todos os testes permaneçam 100% verdes.

---

## 2. Pirâmide de Testes do Sistema

```
                  / \
                 / E2E \           Playwright (Fluxos de Ponta a Ponta)
                /-------\
               / Integração\       pytest-django + Banco Neon (API e Repositórios)
              /-------------\
             / Testes Unitários\   Pytest Puro (Domínio Rico e Casos de Uso)
            /-------------------\
```

### 2.1. Testes Unitários de Domínio (Pure Python + Pytest)
* **Escopo:** Entidades (`Acao`, `Pratica`, `Evidencia`, `Empresa`), Agregados, Objetos de Valor e Serviços de Domínio.
* **Características:** Executam em milissegundos sem tocar em banco de dados, rede ou dependências do Django.
* **Meta de Cobertura:** $\ge 90\%$ de cobertura de linhas e branches na camada `src/domain/`.

### 2.2. Testes de Integração (pytest-django + Neon PostgreSQL)
* **Escopo:** Repositórios concretos (`DjangoAcaoRepository`, `DjangoPraticaRepository`), migrações de banco, consultas SQL analíticas e endpoints da API REST.
* **Isolamento:** Execução com banco de dados de teste isolado (usando *database branching* no Neon ou SQLite em memória para testes rápidos locais em CI/CD).

### 2.3. Testes de Interface e Ponta a Ponta (Next.js + Jest + Playwright)
* **Escopo:** Validação de componentes React, formulários com Zod no shadcn/ui, renderização correta do gráfico Radar com Recharts e fluxos completos de usuário.

---

## 3. Matriz de Critérios de Aceitação das Sprints ("Pronto Quando")

| Sprint | Procedimento de Verificação | Critério Formal de Aceitação ("Pronto Quando") | Tipo de Teste |
| :---: | :--- | :--- | :--- |
| **Sprint 1** | Execução de script de conexão com Neon e migrations iniciais. | Todas as tabelas (`niveis`, `processos_chave`, `praticas`) criadas no Neon e 29 práticas iniciais consultáveis. | Teste de Infraestrutura e Banco |
| **Sprint 2** | Execução da suíte `pytest tests/unit/`. | 100% dos testes unitários de domínio e casos de uso passando, cobrindo regras de transversalidade e invariantes de prática. | Teste Unitário (TDD) |
| **Sprint 3** | Execução de testes de API e script de carga CSV com dados simulados. | Endpoints REST respondendo com status 200/201, documentação Swagger ativa e pelo menos 30 ações de teste gravadas sem duplicações. | Teste de Integração / API |
| **Sprint 4** | Testes de formulários web e renderização da tabela no Next.js. | Usuário preenche formulário no Next.js, ação é gravada no backend e tabela exibe contagem correta (inclusive práticas com 0 evidências). | Teste de Integração Frontend/Backend |
| **Sprint 5** | Comparação da Equação [1] calculada e exibição do Gráfico Radar. | Distância euclidiana calculada com precisão matemática conferida e gráfico radar renderizado com código de cores sem erros de console. | Teste de Algoritmo e UI/UX |
| **Sprint 6** | Teste operacional de ponta a ponta com gestores da NidusTec. | Equipe da incubadora opera o sistema em ambiente de produção (Vercel + PythonAnywhere) e aprova o fluxo sem necessidade de suporte técnico. | Teste de Aceitação de Usuário (TRL 6) |

---

## 4. Protocolos Específicos de Teste Automatizado

### 4.1. Teste Unitário de Transversalidade ($1:N$)
```python
# tests/unit/test_acao_entity.py
import pytest
from datetime import date
from src.domain.entities.acao import Acao

def test_acao_deve_permitir_associacao_transversal_a_multiplas_praticas():
    # Arrange
    acao = Acao(
        titulo="Edital de Seleção e Incubação 2026",
        data_acao=date(2026, 3, 1),
        tipo_acao="edital",
        descricao="Processo seletivo de novos empreendimentos.",
        status_temporal="passada"
    )

    # Act
    acao.associar_pratica(pratica_id=1) # 1.2.1 Recepção de Propostas
    acao.associar_pratica(pratica_id=2) # 1.2.2 Avaliação
    acao.associar_pratica(pratica_id=3) # 1.2.3 Contratação

    # Assert
    assert len(acao.praticas_ids) == 3
    assert acao.validar_transversalidade() is True
```

### 4.2. Teste da Equação [1] (Distância Euclidiana entre Práticas)
```python
# tests/unit/test_calculadora_distancia.py
from src.domain.services.calculadora_distancia_euclidiana import CalculadoraDistanciaEuclidianaService

def test_calculo_distancia_euclidiana_entre_vetores_de_tags():
    # Arrange
    vetor_pratica_1 = {"mentoria": 4.0, "edital": 2.0, "palestra": 0.0}
    vetor_pratica_2 = {"mentoria": 1.0, "edital": 2.0, "palestra": 4.0}
    # d = sqrt((4-1)^2 + (2-2)^2 + (0-4)^2) = sqrt(9 + 0 + 16) = sqrt(25) = 5.0

    # Act
    distancia = CalculadoraDistanciaEuclidianaService.calcular_distancia(vetor_pratica_1, vetor_pratica_2)

    # Assert
    assert distancia == 5.0
```

### 4.3. Teste de Conformidade de Contagem com Zero Evidências (SQL Relacional)
```python
# tests/integration/test_django_repositories.py
import pytest
from src.infrastructure.persistence.repositories.django_pratica_repository import DjangoPraticaRepository

@pytest.mark.django_db
def test_contagem_deve_incluir_praticas_sem_nenhuma_evidencia():
    repo = DjangoPraticaRepository()
    indicadores = repo.obter_indicadores_consolidados()

    # Todas as 29 práticas devem estar presentes
    assert len(indicadores) >= 29
    
    # Práticas sem ações associadas devem retornar total_evidencias == 0
    praticas_vazias = [ind for ind in indicadores if ind.total_evidencias == 0]
    assert len(praticas_vazias) > 0
    for p in praticas_vazias:
        assert p.status_conformidade == "CRITICA_SEM_EVIDENCIA"
```

---

## 5. Testes em Ambiente Operacional Real (TRL 6 e TRL 7)

* **Validação Operacional na NidusTec (UNIFAL-MG):** Inserção do histórico real das rodadas de monitoramento dos anos de 2022 a 2026, conferindo se os relatórios gerados atendem às exigências de auditoria da Anprotec.
* **Validação Cruzada no CEFET:** Avaliação externa de usabilidade e integridade para atestar que o software é genérico e replicável para qualquer incubadora nacional.
