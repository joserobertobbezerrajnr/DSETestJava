# Teste Técnico - Desenvolvedor Java

A seguir, apresento minhas respostas e soluções para as questões do teste técnico.

---

## 1. Programa Java - Anagramas

Para resolver este problema, minha abordagem foi criar uma classe `AnagramGenerator` que utiliza um método recursivo para encontrar todas as permutações de uma dada string. Dei atenção especial à validação da entrada para garantir que ela seja não-nula, não-vazia, contenha apenas letras e que as letras sejam distintas, conforme os requisitos.

**Classe Principal: AnagramGenerator**

```java
import java.util.ArrayList;
import java.util.List;
import java.util.stream.Collectors;

/**
 * Utilitário para gerar anagramas a partir de um conjunto de letras distintas.
 * A lógica principal usa um algoritmo de permutação recursiva para construir
 * todas as combinações possíveis das letras fornecidas.
 */
public class AnagramGenerator {

    /**
     * Gera todos os anagramas possíveis para uma string de entrada.
     *
     * @param input A string contendo letras distintas para gerar anagramas.
     * @return Uma lista de todas as permutações (anagramas) possíveis.
     * @throws IllegalArgumentException se a entrada for nula, vazia, não contiver apenas letras,
     * ou se contiver letras duplicadas.
     */
    public List<String> generate(String input) {
        validateInput(input);
        List<String> result = new ArrayList<>();
        findPermutations("", input, result);
        return result;
    }

    private void validateInput(String input) {
        if (input == null || input.isEmpty()) {
            throw new IllegalArgumentException("A entrada não pode ser nula ou vazia.");
        }
        if (!input.matches("[a-zA-Z]+")) {
            throw new IllegalArgumentException("A entrada deve conter apenas letras.");
        }
        if (input.chars().distinct().count() != input.length()) {
            throw new IllegalArgumentException("A entrada não deve conter letras duplicadas.");
        }
    }

    private void findPermutations(String prefix, String remaining, List<String> result) {
        int n = remaining.length();
        if (n == 0) {
            result.add(prefix);
        } else {
            for (int i = 0; i < n; i++) {
                findPermutations(
                    prefix + remaining.charAt(i),
                    remaining.substring(0, i) + remaining.substring(i + 1, n),
                    result
                );
            }
        }
    }
}
```

**Testes Unitários: AnagramGeneratorTest**

```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import java.util.List;
import java.util.Arrays;

class AnagramGeneratorTest {

    private final AnagramGenerator generator = new AnagramGenerator();

    @Test
    void testAnagramsForThreeLetters() {
        List<String> expected = Arrays.asList("abc", "acb", "bac", "bca", "cab", "cba");
        List<String> result = generator.generate("abc");
        assertTrue(result.size() == expected.size() && result.containsAll(expected) && expected.containsAll(result));
    }

    @Test
    void testAnagramForSingleLetter() {
        List<String> expected = Arrays.asList("a");
        List<String> result = generator.generate("a");
        assertEquals(expected, result);
    }
    
    @Test
    void testThrowsExceptionForEmptyInput() {
        assertThrows(IllegalArgumentException.class, () -> generator.generate(""));
    }

    @Test
    void testThrowsExceptionForNullInput() {
        assertThrows(IllegalArgumentException.class, () -> generator.generate(null));
    }
    
    @Test
    void testThrowsExceptionForNonLetterInput() {
        assertThrows(IllegalArgumentException.class, () -> generator.generate("a1"));
    }

    @Test
    void testThrowsExceptionForDuplicateLetters() {
        assertThrows(IllegalArgumentException.class, () -> generator.generate("aab"));
    }
}
```

---

## 1. Sobrescrevendo equals() e hashCode()

É necessário sobrescrever o método equals() quando a definição padrão de igualdade (comparação de referências de objetos) não é suficiente. Um cenário comum é em classes de modelo (entidades), onde dois objetos diferentes na memória podem representar a mesma entidade do mundo real.

Cenário de Exemplo:

Considere uma classe Pessoa que possui um id único. Duas instâncias diferentes do objeto Pessoa devem ser consideradas "iguais" se possuírem o mesmo id.

Considerações Chave:

Contrato equals() e hashCode(): A regra fundamental é: se a.equals(b) for true, então a.hashCode() deve ser igual a b.hashCode().

Reflexividade: x.equals(x) deve retornar true.

Simetria: Se x.equals(y) retorna true, então y.equals(x) deve retornar true.

Transitividade: Se x.equals(y) e y.equals(z) são true, então x.equals(z) deve ser true.

Consistência: x.equals(y) deve retornar consistentemente true ou false se as propriedades comparadas não forem modificadas.

Comparação com null: x.equals(null) deve retornar false.


**Exemplo de implementação:**

```java
import java.util.Objects;

public class Pessoa {
    private final long id;
    private String nome;

    public Pessoa(long id, String nome) {
        this.id = id;
        this.nome = nome;
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        Pessoa pessoa = (Pessoa) o;
        return id == pessoa.id;
    }

    @Override
    public int hashCode() {
        return Objects.hash(id);
    }
}
```

---

## 2. Padrão de Projeto para Desacoplamento
Para desacoplar o código de uma biblioteca de terceiros, o padrão de projeto 

Adapter (Adaptador) é uma excelente escolha.  Ele atua como um invólucro (wrapper) em torno da biblioteca, expondo uma interface estável e controlada para o restante da aplicação.

Vantagens:

Isolamento: A aplicação depende da interface do adaptador, não da biblioteca. Se a biblioteca for trocada, apenas a implementação do adaptador precisa ser modificada.

Flexibilidade: Permite integrar bibliotecas com interfaces incompatíveis sem alterar o código existente da aplicação.

Código Limpo: Mantém o código da aplicação focado na lógica de negócio, sem detalhes de implementação de bibliotecas de terceiros.

Limitações:

Complexidade Adicional: Introduz uma camada extra de abstração, o que pode aumentar ligeiramente a complexidade do projeto.

**Exemplo: Adapter Pattern**

```java
public interface UploaderDeArquivos {
    void upload(String arquivo, String destino);
}

public class AwsS3Uploader {
    public void transferir(String bucket, String nomeArquivo) {
        System.out.println("Arquivo " + nomeArquivo + " enviado para o bucket " + bucket);
    }
}

public class S3UploaderAdapter implements UploaderDeArquivos {
    private final AwsS3Uploader s3Uploader = new AwsS3Uploader();

    @Override
    public void upload(String arquivo, String destino) {
        s3Uploader.transferir(destino, arquivo);
    }
}
```

---

## 3. Experiência com Angular
Possuo experiência prática no desenvolvimento de Single-Page Applications (SPAs) com Angular, principalmente em painéis de administração e sistemas de gestão. Os recursos que mais utilizo e valorizo no framework são:

Arquitetura Baseada em Componentes: Para criar UIs modulares e reutilizáveis.

Injeção de Dependência: Essencial para escrever um código limpo, desacoplado e altamente testável.

Two-way Data Binding: Para agilizar o desenvolvimento de formulários complexos.

TypeScript: Pela segurança e manutenibilidade que a tipagem estática traz a projetos grandes.

Exemplo de Aplicação Prática
Em um projeto de e-commerce, utilizei um serviço (ProductService) para encapsular as chamadas a uma API REST de produtos. Esse serviço foi injetado em um componente (ProductListComponent), que por sua vez usou o data binding para exibir a lista de produtos na tela.

**Exemplo prático:**

```typescript
// product.service.ts
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
import { Observable } from 'rxjs';

@Injectable({ providedIn: 'root' })
export class ProductService {
  private apiUrl = 'https://api.example.com/products';
  constructor(private http: HttpClient) { }

  getProducts(): Observable<any[]> {
    return this.http.get<any[]>(this.apiUrl);
  }
}
```

```typescript
// product-list.component.ts
import { Component, OnInit } from '@angular/core';
import { ProductService } from './product.service';

@Component({
  selector: 'app-product-list',
  templateUrl: './product-list.component.html'
})
export class ProductListComponent implements OnInit {
  products: any[] = [];

  constructor(private productService: ProductService) { }

  ngOnInit(): void {
    this.productService.getProducts().subscribe(data => this.products = data);
  }
}
```

```html
<!-- product-list.component.html -->
<h2>Lista de Produtos</h2>
<ul>
  <li *ngFor="let product of products">
    {{ product.name }} - {{ product.price | currency }}
  </li>
</ul>
```

---

## 4. Prevenção de SQL Injection
Para prevenir ataques de SQL injection, minha principal estratégia é nunca concatenar entradas de usuário diretamente em strings de SQL. As técnicas que aplico são:

1. Queries Parametrizadas (Prepared Statements): É a forma mais segura e a que eu sempre priorizo. A entrada do usuário é tratada como um valor literal, e não como parte do comando SQL, eliminando o risco de injeção.

```java
String sql = "SELECT * FROM users WHERE id = ?";
PreparedStatement statement = connection.prepareStatement(sql);
statement.setString(1, userInput);
ResultSet rs = statement.executeQuery();
```

Uso de ORMs (Object-Relational Mapping): Em projetos com frameworks como Spring, eu utilizo o Spring Data JPA/Hibernate. ORMs geram queries seguras por padrão, abstraindo a construção manual de SQL e prevenindo injeções de forma nativa. 
Medidas Adicionais
Princípio do Menor Privilégio: Configuro o usuário do banco de dados da aplicação com o mínimo de permissões necessárias.

Validação de Entrada no Servidor: Valido o formato, tipo e tamanho de todos os dados recebidos antes de qualquer processamento.

---

## 5. Diagnóstico e Melhoria de Performance de um Processo Batch

Para diagnosticar e otimizar um processo batch, eu seguiria estes passos:

a. Análise e Monitoramento: Começaria adicionando logs detalhados com timestamps para medir a duração de cada etapa-chave (leitura do DB, processamento, envio FTP). Em seguida, usaria um profiler (como o VisualVM) para identificar gargalos de CPU e memória.

b. Otimização de Banco de Dados: Analisaria as queries lentas com EXPLAIN PLAN. Verificaria a necessidade de novos índices nas colunas de busca e junção. Implementaria a leitura e escrita em lotes (batching) para reduzir o I/O do banco.

c. Otimização da Lógica: Refatoraria o código em busca de algoritmos ineficientes, como consultas ao banco dentro de loops, substituindo-os por uma única consulta massiva.

d. Otimização da Transferência FTP: Para reduzir o tempo de rede, eu comprimiria os arquivos (.zip, .gz) antes do envio. Se fossem muitos arquivos pequenos, eu os agruparia em um único pacote (.tar) para diminuir o overhead de múltiplas conexões.

---

## 7. Queries SQL
As queries a seguir foram escritas para atender aos requisitos.


**a. Vendedores sem pedidos com a Samsonic**

```sql
SELECT s.Name
FROM Salesperson s
WHERE s.ID NOT IN (
    SELECT o.salesperson_id
    FROM Orders o
    JOIN Customer c ON o.customer_id = c.ID
    WHERE c.Name = 'Samsonic'
);
```

**b. Atualizar nomes de vendedores com 2+ pedidos**

```sql
UPDATE Salesperson
SET Name = Name || '*'
WHERE ID IN (
    SELECT salesperson_id
    FROM Orders
    GROUP BY salesperson_id
    HAVING COUNT(*) >= 2
);
```

**c. Deletar vendedores com pedidos para a cidade de Jackson**

```sql
DELETE FROM Salesperson
WHERE ID IN (
    SELECT DISTINCT o.salesperson_id
    FROM Orders o
    JOIN Customer c ON o.customer_id = c.ID
    WHERE c.City = 'Jackson'
);
```

**d. Total de vendas por vendedor**

```sql
SELECT
    s.Name,
    COALESCE(SUM(o.Amount), 0) AS TotalSalesAmount
FROM
    Salesperson s
LEFT JOIN
    Orders o ON s.ID = o.salesperson_id
GROUP BY
    s.ID, s.Name;
```

---

## 7. Sistema XYZ - Gerenciamento de Plantas

User Story
Como um usuário administrador, eu quero poder criar, ler, atualizar e deletar (CRUD) plantas no sistema, para que os dados mestres estejam sempre corretos e disponíveis para as fases futuras do projeto.

Regras de Negócio e Suposições
- RN01: O código da planta deve ser único.

- RN02: O código é numérico e obrigatório.

- RN03: A descrição é opcional, com no máximo 10 caracteres.

- RN04: Apenas administradores podem excluir plantas.

Suposição: Existe um sistema de autenticação e autorização que diferencia usuários "administradores" de outros perfis.

Validações e Medidas de Segurança
- Validação (Backend): Implementaria validações para garantir que o Code é numérico e único (consulta prévia ao banco) e que a Description não excede 10 caracteres.

- Segurança (RBAC): Protegeria o endpoint de exclusão (DELETE /api/plants/{id}) para que apenas usuários com a role ADMIN possam acessá-lo.

Estratégia de Testes
- Testes de Unidade para as funções de validação.

- Testes de Integração para as operações CRUD com o banco.

- Testaria os seguintes casos de borda:

- Tentar criar planta com código duplicado.

- Tentar criar planta com código não numérico.

- Tentar salvar descrição com mais de 10 caracteres.

- Tentar deletar uma planta como usuário não-admin (esperando um erro 403 Forbidden).

---

## 9. Testes da Funcionalidade de Cadastro de Usuário

**Tipos de Testes e Cenários**
- Testes de Unidade: Foco em lógicas pequenas e isoladas.

    - Cenário: Testar a função de validação de formato de e-mail.

- Testes de Integração: Foco na interação entre camadas (serviço e banco de dados).

    - Cenário: Tentar salvar um usuário com e-mail duplicado e verificar se a exceção correta é lançada.

- Testes End-to-End (E2E): Foco no fluxo completo do usuário.

    - Cenário: Simular um admin deletando um usuário e verificar se ele some da lista na UI.

**Edge Cases que eu testaria**
- Tentativa de cadastro com campos obrigatórios vazios.

- Entrada de e-mail em formato inválido.

- Entrada de dados com caracteres especiais para testar contra XSS.

- Tentativa de um usuário não-admin de acessar a funcionalidade de exclusão via API.

```java
// Teste de Integração para a regra de unicidade de e-mail

@Test
Function test_deve_lancar_excecao_ao_salvar_usuario_com_email_duplicado() {
    // Arrange: Salva um usuário inicial no banco de teste.
    User usuarioExistente = new User("John Doe", "john.doe@example.com");
    userRepository.save(usuarioExistente);

    User novoUsuarioComEmailIgual = new User("Jane Doe", "john.doe@example.com");

    // Act & Assert: Verifica se a tentativa de salvar o segundo usuário
    // com o mesmo e-mail lança a exceção esperada.
    assertThrows(EmailDuplicadoException.class, () -> {
        userService.salvar(novoUsuarioComEmailIgual);
    });
}
```
