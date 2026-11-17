# 04 - Estrutura de Classes em Java

## 1. Diretrizes de Desenvolvimento Orientado a Objetos

O back-end do sistema é desenvolvido em **Java (versão LTS JDK 21)** utilizando o gerenciador de automação e dependências **Apache Maven**. O design de software adota os princípios de Orientação a Objetos (POO), encapsulamento, modularidade e desacoplamento de camadas.

### 1.1. Dependências Maven Essenciais (pom.xml)
Conforme especificado no Passo 2 do checklist do projeto, as dependências nucleares são:

```xml
<dependencies>
    <!-- Controles de Interface Gráfica JavaFX -->
    <dependency>
        <groupId>org.openjfx</groupId>
        <artifactId>javafx-controls</artifactId>
        <version>21</version>
    </dependency>

    <!-- Driver Oficial de Comunicação Java -> Neo4j -->
    <dependency>
        <groupId>org.neo4j.driver</groupId>
        <artifactId>neo4j-java-driver</artifactId>
        <version>5.27.0</version> <!-- Versão 5.x estável do Maven Central -->
    </dependency>
</dependencies>
```

---

## 2. Modelagem da Cumulatividade por Herança de Classes

Uma das regras de negócio fundamentais do modelo CERNE é que a conquista de um nível de maturidade superior exige a manutenção e execução integral de todos os processos dos níveis precedentes. No back-end, essa regra é formalizada estruturalmente por meio de **herança de classes**:

```
+-------------------------------------------------------------+
|                           Cerne1                            |
+-------------------------------------------------------------+
| # praticas: List<Pratica> (14 práticas do CERNE 1)          |
+-------------------------------------------------------------+
                              ^
                              | extends
+-------------------------------------------------------------+
|                           Cerne2                            |
+-------------------------------------------------------------+
| # praticasCerne2: List<Pratica> (+ 6 práticas do CERNE 2)   |
+-------------------------------------------------------------+
                              ^
                              | extends
+-------------------------------------------------------------+
|                           Cerne3                            |
+-------------------------------------------------------------+
| # praticasCerne3: List<Pratica> (+ 7 práticas do CERNE 3)   |
+-------------------------------------------------------------+
                              ^
                              | extends
+-------------------------------------------------------------+
|                           Cerne4                            |
+-------------------------------------------------------------+
| # praticasCerne4: List<Pratica> (+ 2 práticas do CERNE 4)   |
+-------------------------------------------------------------+
```

### Esboço Conceitual de Código (Passo 5):
```java
public class Pratica {
    private String codigo;        // Ex.: "1.1.1"
    private String nome;          // Ex.: "Sensibilização"
    private String procedimento;
    private String metrica;
    private String aprendizado;

    // Getters, Setters e Construtores
}

public class Cerne1 {
    protected List<Pratica> praticas;
    // Métodos para obtenção e validação do CERNE 1 (14 práticas)
}

public class Cerne2 extends Cerne1 {
    // Incorpora por herança as 14 práticas do CERNE 1
    // e adiciona a gestão das 6 práticas próprias do CERNE 2
}

public class Cerne3 extends Cerne2 {
    // Incorpora por herança as 20 práticas de CERNE 1 e 2
    // e adiciona as 7 práticas próprias do CERNE 3
}

public class Cerne4 extends Cerne3 {
    // Incorpora por herança as 27 práticas acumuladas
    // e adiciona as 2 práticas próprias do CERNE 4 (totalizando 29)
}
```

---

## 3. Entidades Básicas Mapeadas

* **`Pratica`:** Representa a unidade contábil de cada processo do CERNE.
* **`Nivel`:** Representa o estágio de maturidade institucional e suas dependências.
* **`Acao`:** Representa o evento, oficina, reunião, publicação de edital ou atendimento executado na incubadora. Mantém coleções de `Pratica` (transversalidade), `Evidencia` gerada e `Tag` associadas.
* **`Evidencia`:** Modela os comprovantes formais da ação (ata de banca, relatório técnico, link para fotos, certificado, contrato).
* **`Empresa`:** Modela as startups atendidas, controlando seu status (`INCUBADA` ou `GRADUADA`) e o acompanhamento métrico nos 5 eixos do empreendimento (Empreendedor, Tecnologia, Capital, Mercado, Gestão).
* **`Tag`:** Modela os descritores semânticos atribuídos às ações para viabilizar as regras de classificação e a matriz de similaridade euclidiana.

---

## 4. Comunicação com o Banco de Dados e Segurança

A conexão do Java com o banco de dados em grafo utiliza o driver oficial do Neo4j sob padrão de bloco *try-with-resources*, garantindo fechamento e desalocação correta de conexões:

```java
import org.neo4j.driver.AuthTokens;
import org.neo4j.driver.GraphDatabase;
import org.neo4j.driver.Driver;

public class ConexaoBanco {
    private static final String URI = "bolt://localhost:7687";
    private static final String USUARIO = "neo4j";

    public static Driver obterDriver() {
        // Leitura obrigatória de senha fora do código-fonte (variável de ambiente)
        String senha = System.getenv("NEO4J_SENHA");
        if (senha == null || senha.isBlank()) {
            throw new IllegalStateException("Variável de ambiente NEO4J_SENHA não configurada.");
        }
        return GraphDatabase.driver(URI, AuthTokens.basic(USUARIO, senha));
    }
}
```

---

## 5. Decisões de Classes Pendentes de Aprovação da Orientadora

Conforme disposto no **Passo 5** do checklist:
> *"Desenhar o diagrama de classes antes de programar (desenhar do zero, usando as Figuras 2 a 5 do projeto só como referência visual). Pronto quando: o diagrama de classes foi revisado pela orientadora e as classes compilam."*

Permanecem como decisões a aprovar antes do desenvolvimento de código:
1. **Aprovação Formal do Diagrama de Classes UML:** A equipe deve submeter o diagrama conceitual completo (com atributos, tipos e métodos) à revisão formal da Profa. Dra. Izabella Carneiro Bastos antes de criar os arquivos `.java`.
2. **Polimorfismo vs. Atributo na Entidade `Empresa`:** Avaliar se o status da empresa deve ser tratado por especialização de subclasses (`public class Incubada extends Empresa` e `public class Graduada extends Empresa`) ou por padrão State/Enum (`StatusEmpresa.INCUBADA`, `StatusEmpresa.GRADUADA`).
3. **Padrão de Camada de Dados (Repository / DAO):** Validação da arquitetura da camada de persistência para as transações Cypher (utilização de classes DAO dedicadas por entidade versus um serviço integrador unificado).
