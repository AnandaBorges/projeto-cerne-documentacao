# 04 - Design de Domínio Rico, DDD e Clean Architecture (Python)

## 1. Diretrizes de Clean Architecture no Backend Python

O backend é estruturado em **Python 3.12+** sob as regras estritas da **Clean Architecture** (Arquitetura Limpa). O princípio orientador é a **Regra de Dependência**: dependências apontam unicamente para o interior (em direção às regras de negócio de domínio).

O framework web **Django** é posicionado na camada mais externa (Infraestrutura/Interfaces), atuando como mero mecanismo de entrega HTTP e de mapeamento relacional. O núcleo de regras do negócio CERNE é puramente escrito em Python nativo (*Plain Old Python Objects - POPO*), sem nenhuma importação de pacotes do Django ou de dependências de banco de dados.

```
                  +----------------------------------------------+
                  |         INTERFACES / API (Camada Externa)    |
                  |     Django Ninja / DRF Routers & Schemas     |
                  +----------------------------------------------+
                                         |
                                         v
                  +----------------------------------------------+
                  |              APLICAÇÃO (Use Cases)           |
                  |      CadastrarAcaoUseCase, CalcularRadar     |
                  +----------------------------------------------+
                                         |
                                         v
                  +----------------------------------------------+
                  |          DOMÍNIO RICO (Regras Centrais)      |
                  |   Entidades, Agregados, VOs, Domain Services |
                  |          Interfaces de Repositório           |
                  +----------------------------------------------+
                                         ^
                                         | Inversão de Controle
                  +----------------------------------------------+
                  |         INFRAESTRUTURA (Camada Externa)      |
                  |    Django ORM Models, Postgres Repositories  |
                  +----------------------------------------------+
```

---

## 2. Estrutura de Diretórios do Projeto Backend

```
backend/
├── manage.py
├── pyproject.toml
├── core/                               # Configuração Django (settings, wsgi, asgi)
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py              # Ajustado para PythonAnywhere + Neon
│   └── urls.py
├── src/
│   ├── domain/                         # CAMADA DE DOMÍNIO (Pura, Zero Django)
│   │   ├── entities/
│   │   │   ├── acao.py
│   │   │   ├── pratica.py
│   │   │   ├── evidencia.py
│   │   │   ├── empresa.py
│   │   │   └── nivel.py
│   │   ├── value_objects/
│   │   │   ├── codigo_pratica.py
│   │   │   ├── status_acao.py
│   │   │   └── eixos_avaliacao.py
│   │   ├── repositories/               # Interfaces / Contratos Abstratos
│   │   │   ├── i_acao_repository.py
│   │   │   ├── i_pratica_repository.py
│   │   │   └── i_empresa_repository.py
│   │   ├── services/                   # Lógicas de domínio entre múltiplos agregados
│   │   │   ├── calculadora_distancia_euclidiana.py
│   │   │   └── avaliador_conformidade_cerne.py
│   │   └── exceptions.py
│   │
│   ├── application/                    # CAMADA DE APLICAÇÃO (Casos de Uso e DTOs)
│   │   ├── use_cases/
│   │   │   ├── cadastrar_acao.py
│   │   │   ├── calcular_indicadores.py
│   │   │   ├── importar_csv_acoes.py
│   │   │   └── registrar_monitoramento_empresa.py
│   │   └── dtos/
│   │       ├── acao_dto.py
│   │       └── indicador_dto.py
│   │
│   ├── infrastructure/                 # CAMADA DE INFRAESTRUTURA
│   │   ├── persistence/
│   │   │   ├── models/                 # Modelos Django ORM (PostgreSQL)
│   │   │   │   ├── acao_model.py
│   │   │   │   └── pratica_model.py
│   │   │   ├── repositories/           # Implementação Concreta das Interfaces
│   │   │   │   ├── django_acao_repository.py
│   │   │   │   └── django_pratica_repository.py
│   │   │   └── mappers/                # Converte Django Model <-> Entidade de Domínio
│   │   └── storage/
│   │       └── s3_file_storage.py
│   │
│   └── interfaces/                     # CAMADA DE INTERFACES / API REST
│       └── api/
│           ├── v1/
│           │   ├── routers/
│           │   │   ├── acoes.py
│           │   │   ├── praticas.py
│           │   │   └── radar.py
│           │   └── schemas/            # Schemas de validação e serialização
│           └── middleware/
└── tests/                              # TESTES (TDD)
    ├── unit/                           # Testes de Domínio e Casos de Uso (sem BD)
    │   ├── test_acao_entity.py
    │   ├── test_pratica_entity.py
    │   └── test_calculadora_distancia.py
    └── integration/                    # Testes com PostgreSQL (Neon) e API
        ├── test_django_repositories.py
        └── test_api_endpoints.py
```

---

## 3. Design de Domínio Rico (Modelagem em Python Puro)

Ao contrário de modelos anêmicos (onde dados e comportamento são separados), o **Domínio Rico** concentra validações, transições de estado e cálculos dentro das próprias entidades e agregados:

### 3.1. Objeto de Valor (Value Object): `CodigoPratica`
```python
from dataclasses import dataclass
import re

@dataclass(frozen=True)
class CodigoPratica:
    valor: str

    def __post_init__(self):
        # Valida formato canônico do código CERNE: Nível.Processo.Prática (ex: 1.1.1, 2.3.2)
        if not re.match(r"^\d\.\d\.\d+$", self.valor):
            raise ValueError(f"Formato inválido de código CERNE: '{self.valor}'. Esperado: 'X.Y.Z'")

    @property
    def nivel_codigo(self) -> int:
        return int(self.valor.split(".")[0])
```

### 3.2. Entidade Rica: `Pratica` (Tríade de Conformidade)
```python
from dataclasses import dataclass, field
from typing import List, Optional
from .codigo_pratica import CodigoPratica

@dataclass
class Evidencia:
    id: str
    tipo_documento: str
    descricao: str
    url_arquivo: str

@dataclass
class Pratica:
    id: int
    codigo: CodigoPratica
    nome: str
    procedimento: Optional[str]
    metrica: Optional[str]
    aprendizado: Optional[str]
    peso: float = 1.0
    evidencias: List[Evidencia] = field(default_factory=list)

    def possui_triade_completa(self) -> bool:
        """Verifica se a prática cumpre o requisito de ter Procedimento, Métrica e Aprendizado."""
        return bool(
            self.procedimento and self.procedimento.strip() and
            self.metrica and self.metrica.strip() and
            self.aprendizado and self.aprendizado.strip()
        )

    def adicionar_evidencia(self, evidencia: Evidencia) -> None:
        if any(e.id == evidencia.id for e in self.evidencias):
            raise ValueError(f"Evidência {evidencia.id} já vinculada a esta prática.")
        self.evidencias.append(evidencia)

    @property
    def total_evidencias(self) -> int:
        return len(self.evidencias)

    @property
    def em_conformidade(self) -> bool:
        return self.possui_triade_completa() and self.total_evidencias > 0
```

### 3.3. Raiz de Agregado: `Acao` (Garantia de Transversalidade e Não Duplicação)
```python
from dataclasses import dataclass, field
from datetime import date
from typing import Set, List
from uuid import UUID, uuid4

@dataclass
class Acao:
    titulo: str
    data_acao: date
    tipo_acao: str
    descricao: str
    status_temporal: str # 'passada', 'presente', 'planejada'
    id: UUID = field(default_factory=uuid4)
    praticas_ids: Set[int] = field(default_factory=set) # Transversalidade 1:N
    evidencias: List[Evidencia] = field(default_factory=list)
    tags: Set[str] = field(default_factory=set)

    def associar_pratica(self, pratica_id: int) -> None:
        """Associa a ação a uma prática-chave (suporta transversalidade)."""
        self.praticas_ids.add(pratica_id)

    def associar_tag(self, tag: str) -> None:
        tag_normalizada = tag.strip().lower()
        if tag_normalizada:
            self.tags.add(tag_normalizada)

    def validar_transversalidade(self) -> bool:
        """Valida se a ação atende a múltiplos indicadores transversais."""
        return len(self.praticas_ids) > 1
```

---

## 4. Implementação da Cumulatividade sem Herança Rígida

No modelo conceitual anterior em Java, a cumulatividade era modelada por herança de classes estática (`class Cerne2 extends Cerne1`). No paradigma de Domínio Rico e DDD, a cumulatividade é tratada por **composição e validação de regras de pré-requisitos**, conferindo total dinamicidade:

```python
from dataclasses import dataclass
from typing import List

@dataclass
class NivelMaturidade:
    codigo: int # 1, 2, 3 ou 4
    nome: str
    praticas: List[Pratica]

    def verificar_maturidade(self, niveis_anteriores: List['NivelMaturidade']) -> bool:
        """
        Para este nível estar conquistado, todos os níveis anteriores 
        devem estar em conformidade estrita e 100% de suas próprias práticas devem ser atendidas.
        """
        # Validação da cumulatividade
        for nivel in niveis_anteriores:
            if not nivel.esta_homologado():
                return False
        return self.esta_homologado()

    def esta_homologado(self) -> bool:
        return all(p.em_conformidade for p in self.praticas)
```

---

## 5. Implementação da Equação [1] via Serviço de Domínio (TDD)

```python
import math
from typing import Dict

class CalculadoraDistanciaEuclidianaService:
    @staticmethod
    def calcular_distancia(vetor_a: Dict[str, float], vetor_b: Dict[str, float]) -> float:
        """
        Calcula a Equação [1] do CERNE entre duas práticas:
        d(Pi, Pj) = sqrt(sum((w_ik - w_jk)^2))
        """
        todas_tags = set(vetor_a.keys()).union(set(vetor_b.keys()))
        soma_quadrados = 0.0
        
        for tag in todas_tags:
            peso_a = vetor_a.get(tag, 0.0)
            peso_b = vetor_b.get(tag, 0.0)
            soma_quadrados += (peso_a - peso_b) ** 2
            
        return math.sqrt(soma_quadrados)
```

---

## 6. Camada de Aplicação: Caso de Uso Exemplar

```python
from typing import List
from ..domain.entities.acao import Acao
from ..domain.repositories.i_acao_repository import IAcaoRepository
from ..domain.repositories.i_pratica_repository import IPraticaRepository

class CadastrarAcaoComTransversalidadeUseCase:
    def __init__(self, acao_repo: IAcaoRepository, pratica_repo: IPraticaRepository):
        self.acao_repo = acao_repo
        self.pratica_repo = pratica_repo

    def execute(self, titulo: str, data_acao, tipo_acao: str, descricao: str, status_temporal: str, codigos_praticas: List[str], tags: List[str]) -> Acao:
        acao = Acao(
            titulo=titulo,
            data_acao=data_acao,
            tipo_acao=tipo_acao,
            descricao=descricao,
            status_temporal=status_temporal
        )

        for codigo in codigos_praticas:
            pratica = self.pratica_repo.obter_por_codigo(codigo)
            if not pratica:
                raise ValueError(f"Prática com código {codigo} não encontrada.")
            acao.associar_pratica(pratica.id)

        for tag in tags:
            acao.associar_tag(tag)

        return self.acao_repo.salvar(acao)
```

---

## 7. Decisões de Design Pendentes de Homologação

1. **Estratégia de Mapeamento ORM:** Adoção de padrão *Data Mapper* explícito (convertendo `AcaoModel` do Django em entidade `Acao` de domínio puro) para garantir 100% de desacoplamento do Django no domínio.
2. **Gerenciamento de Transações Atômicas:** Confirmação se as operações que envolvem agregação e cálculo de transversalidade serão orquestradas com a anotação `transaction.atomic()` do Django na camada de infraestrutura.
