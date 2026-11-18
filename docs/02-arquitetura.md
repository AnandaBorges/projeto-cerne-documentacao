# 02 - Arquitetura do Sistema e Tecnologias

## 1. Visão Arquitetural Geral: Clean Architecture & Topologia em Nuvem

A arquitetura do sistema adota o paradigma **Clean Architecture** (Arquitetura Limpa) aliado aos princípios do **Domain-Driven Design (DDD)** no backend, operando em uma topologia distribuída moderna em nuvem pública (Vercel, PythonAnywhere e Neon).

```
+-----------------------------------------------------------------------------------+
|                           CAMADA DE APRESENTAÇÃO (FRONTEND)                       |
|          Next.js (App Router / React) + Tailwind CSS + shadcn/ui (Radix UI)       |
|               Hospedado na Vercel (Edge Network / CDN Global)                     |
+-----------------------------------------------------------------------------------+
                                          |
                                          | Chamadas HTTP/REST (JSON via HTTPS + CORS)
                                          v
+-----------------------------------------------------------------------------------+
|                        CAMADA DE INTERFACES / API (BACKEND)                       |
|               Django REST API (Views / Controllers / Serializers DTOs)            |
|                  Hospedado no PythonAnywhere (WSGI Web Server)                    |
+-----------------------------------------------------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|                     CAMADA DE APLICAÇÃO (USE CASES / SERVIÇOS)                    |
|             Comandos e Consultas (CQRS Leve) | Orquestração do Negócio             |
|                  Casos de Uso Independentes de Framework Web                     |
+-----------------------------------------------------------------------------------+
                                          |
                                          v
+-----------------------------------------------------------------------------------+
|                         CAMADA DE DOMÍNIO (DOMÍNIO RICO - DDD)                    |
|           Entidades Puras Python (POPO) | Agregados | Objetos de Valor            |
|           Regras de Negócio Invariantes | Serviços de Domínio | Equação [1]       |
|                      Interfaces de Repositório (Contratos)                        |
+-----------------------------------------------------------------------------------+
                                          ^
                                          | Implementa Contratos (Inversão de Dependência)
+-----------------------------------------------------------------------------------+
|                           CAMADA DE INFRAESTRUTURA                                |
|        Django ORM (Models & Migrations) | Repositórios Concretos PostgreSQL       |
|             Armazenamento de Evidências | Integração Externa (CSV/Sheets)         |
+-----------------------------------------------------------------------------------+
                                          |
                                          | Conexão TLS com Pooling (PgBouncer)
                                          v
+-----------------------------------------------------------------------------------+
|                         BANCO DE DADOS RELACIONAL SERVERLESS                      |
|                  PostgreSQL 16+ Hospedado no Neon (Cloud Database)                |
|               Tabelas Relacionais, Chaves Estrangeiras, Índices e Views           |
+-----------------------------------------------------------------------------------+
```

---

## 2. Princípios de Engenharia de Software Adotados

### 2.1. Clean Architecture (Regra de Dependência)
* **Independência de Framework:** O núcleo do negócio (regras do CERNE) não depende de bibliotecas de terceiros nem do Django. O Django é tratado estritamente como um detalhe de entrega/infraestrutura.
* **Testabilidade Isolada:** Todo o domínio de regras de negócio pode ser testado via testes unitários instantâneos, sem necessidade de banco de dados, servidor web ou UI.
* **Inversão de Dependência:** O domínio define interfaces abstratas de repositório (`IPraticaRepository`, `IAcaoRepository`), e a camada de infraestrutura provê a implementação concreta que fala com o Django ORM e o PostgreSQL.

### 2.2. Domain-Driven Design (DDD) e Domínio Rico
* **Linguagem Ubíqua (Ubiquitous Language):** O código utiliza rigorosamente os termos oficiais da metodologia CERNE (*Nível, Processo-Chave, Prática-Chave, Indicador Atômico, Evidência, Transversalidade, Eixos de Empreendimento, PDCA*).
* **Domínio Rico vs. Modelo Anêmico:** As entidades não são simples estruturas de dados com *getters/setters*. Métodos como `acao.associar_pratica(pratica)`, `pratica.adicionar_evidencia(evidencia)`, `pratica.verificar_conformidade_triade()` concentram a validação dos estados e invariantes de negócio.
* **Bounded Contexts (Contextos Delimitados):**
  1. *Contexto de Certificação e Auditoria CERNE:* Gerencia a taxonomia dos 4 níveis, cálculo de conformidade, transversalidade e indicadores atômicos.
  2. *Contexto de Empreendimentos e Incubação:* Gerencia as startups incubadas/graduadas, planos de desenvolvimento (PDE) e monitoramento nos 5 eixos.
  3. *Contexto Operacional e MAKERBot:* Gestão de eventos, reservas de salas, agendamento de máquinas maker, controle de insumos e sigilo.

### 2.3. Test-Driven Development (TDD)
* Desenvolvimento baseado no ciclo **Red $\rightarrow$ Green $\rightarrow$ Refactor**:
  1. **Red:** Escreve-se primeiro o teste unitário que define o comportamento esperado do domínio ou caso de uso, executando-o para confirmar sua falha.
  2. **Green:** Escreve-se a implementação mínima necessária para o teste passar com sucesso.
  3. **Refactor:** Limpa-se o código, eliminando redundâncias e aprimorando o design, mantendo os testes 100% verdes.

---

## 3. Detalhamento das Camadas do Sistema

### 3.1. Camada de Apresentação (Frontend em Next.js)
* **Framework:** Next.js (App Router, React Server & Client Components).
* **Estilização e Design System:** Tailwind CSS configurado com a biblioteca de componentes **shadcn/ui** (baseada em primitivos acessíveis Radix UI e ícones Lucide React).
* **Componentes Chave:**
  * Painel de Indicadores com cartões de status e métricas em tempo real.
  * Tabela interativa de 29 Práticas com filtros por Nível e código de cores para alertas (vermelho: 0 evidências; amarelo: atenção; verde: em conformidade).
  * Componente de Gráfico Radar por Nível (utilizando `recharts` integrado ao Tailwind).
  * Formulários dinâmicos com validação de cliente via `react-hook-form` e `zod`.
* **Deploy:** Plataforma **Vercel**, usufruindo de CDN global, compilação otimizada e certificados SSL/TLS automáticos.

### 3.2. Camada de Interfaces / API (Django REST no Backend)
* **Framework:** Python 3.12+ com Django e camada de API REST (Django REST Framework ou Django Ninja).
* **Responsabilidades:**
  * Roteamento de endpoints HTTP (`/api/v1/...`).
  * Autenticação e autorização via JWT (*JSON Web Tokens*), controlando permissões por papel (Administrador, Gestor, Auditor *read-only*).
  * Serialização e desserialização de DTOs (*Data Transfer Objects*).
  * Tratamento de exceções e retorno de códigos HTTP padronizados (200, 201, 400, 404, 422, 500).
* **Deploy:** **PythonAnywhere**, servido como aplicação WSGI com suporte a tarefas agendadas e HTTPS.

### 3.3. Camada de Aplicação (Use Cases)
* Casos de uso atômicos que representam as ações do usuário:
  * `CadastrarAcaoUseCase`: Valida a ação, vincula práticas (transversalidade), associa evidências e persiste.
  * `CalcularIndicadoresCerneUseCase`: Executa a agregação quantitativa e qualitativa por prática e nível.
  * `CalcularDistanciaEuclidianaUseCase`: Aplica a Equação [1] entre pares de práticas a partir das TAGs.
  * `ImportarAcoesCsvUseCase`: Processa arquivos em lote com garantia de idempotência (eliminação de duplicatas).

### 3.4. Camada de Domínio (Pure Python Domain)
* **Entidades e Agregados:**
  * `Nivel` (Entidade / Raiz de Agregado): Gerencia a cumulatividade de práticas e regras de transição.
  * `Pratica` (Entidade): Mantém atributos da tríade (*Procedimento, Métrica, Aprendizado*), peso qualitativo e status.
  * `Acao` (Raiz de Agregado): Encapsula data, descrição, status temporal (*passada, presente, planejada*), coleção de evidências, lista de TAGs e vínculos de transversalidade.
  * `Evidencia` (Entidade / Value Object): Registros comprobatórios com tipologia, hash de arquivo e metadados.
  * `Empresa` (Entidade): Startup atendida com métricas nos 5 eixos (Empreendedor, Tecnologia, Capital, Mercado, Gestão).
  * `Tag` (Value Object): Rótulo textual padronizado.
* **Serviços de Domínio:** Lógicas complexas que envolvem múltiplos agregados, como o cálculo da matriz euclidiana de similaridade entre práticas.

### 3.5. Camada de Infraestrutura e Banco de Dados (PostgreSQL no Neon)
* **Banco de Dados:** PostgreSQL 16+ gerenciado no **Neon**.
* **ORM:** Django ORM utilizado estritamente como mecanismo de mapeamento objeto-relacional na infraestrutura, convertendo entre os modelos de banco de dados (`django.db.models.Model`) e as entidades ricas do domínio Python.
* **Connection Pooling:** Conexão direta com pooling do Neon (PgBouncer) via string de conexão segura TLS (`postgresql://...`).

---

## 4. Topologia de Infraestrutura e Segurança

* **Comunicação Segura:** Todo o tráfego externo ocorre via HTTPS com TLS 1.3 obrigatório.
* **CORS (Cross-Origin Resource Sharing):** Configuração no Django (`django-cors-headers`) autorizando exclusivamente a URL de produção na Vercel e o ambiente de desenvolvimento local.
* **Isolamento de Credenciais:** As chaves de acesso ao Neon (`DATABASE_URL`), segredos do Django (`SECRET_KEY`), parâmetros de autenticação JWT e credenciais de APIs externas são fornecidas via variáveis de ambiente nos painéis de controle da Vercel e do PythonAnywhere, sem qualquer exposição no repositório Git (`.gitignore`).

---

## 5. Decisões Arquiteturais Pendentes de Homologação

1. **Framework de API REST: Django REST Framework vs. Django Ninja:**
   * Avaliar a adoção do **Django Ninja**, que utiliza *Pydantic* para validação de esquemas de dados de forma nativa e assíncrona, oferecendo maior facilidade de integração com os DTOs de Clean Architecture e documentação Swagger automática.
2. **Conectividade de Saída no PythonAnywhere Free:**
   * A conta gratuita do PythonAnywhere bloqueia conexões TCP diretas na porta 5432 (porta padrão do Postgres). É necessário confirmar:
     * (a) Adesão ao plano *Hacker* da PythonAnywhere (\$5/mês, desbloqueia TCP irrestrito); ou
     * (b) Utilização do driver HTTP Serverless do Neon para operações de leitura/escrita via requisições HTTP permitidas; ou
     * (c) Alternativa de hospedagem backend com suporte a TCP gratuito (ex: Render/Railway).
3. **Mecanismo de Armazenamento de Arquivos Comprobatórios (Evidências):**
   * Definição se os arquivos comprobatórios (atas, fotos, PDFs de contratos) serão armazenados em *Object Storage* compatível com S3 (AWS S3, Cloudflare R2 ou Supabase Storage) com URLs assinadas salvas no Postgres, ou se na fase inicial serão aceitos apenas links externos (Google Drive).
4. **Política de Autenticação para Auditores:**
   * Confirmação da exigência de token específico para o perfil `Auditor`, permitindo visualização de relatórios e painéis de conformidade com bloqueio sistêmico para qualquer endpoint com métodos `POST`, `PUT`, `PATCH` ou `DELETE`.
