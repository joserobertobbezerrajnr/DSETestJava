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

## 2. Sobrescrevendo equals() e hashCode()

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

## 3. Padrão de Projeto para Desacoplamento

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

## 4. Experiência com Angular

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

## 5. Prevenção de SQL Injection

```java
String sql = "SELECT * FROM users WHERE id = ?";
PreparedStatement statement = connection.prepareStatement(sql);
statement.setString(1, userInput);
ResultSet rs = statement.executeQuery();
```

- Uso de ORMs como Spring Data JPA/Hibernate
- Princípio do menor privilégio
- Validação de entrada no servidor

---

## 6. Diagnóstico e Melhoria de Performance de um Processo Batch

- Logs e monitoramento com timestamps
- Profiling com VisualVM
- Otimização de queries e índices
- Batch processing para leitura/escrita
- Compressão e agrupamento de arquivos para FTP

---

## 7. Queries SQL

**Vendedores sem pedidos com a Samsonic**

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

**Atualizar nomes de vendedores com 2+ pedidos**

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

**Deletar vendedores com pedidos para a cidade de Jackson**

```sql
DELETE FROM Salesperson
WHERE ID IN (
    SELECT DISTINCT o.salesperson_id
    FROM Orders o
    JOIN Customer c ON o.customer_id = c.ID
    WHERE c.City = 'Jackson'
);
```

**Total de vendas por vendedor**

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

## 8. Sistema XYZ - Gerenciamento de Plantas

**User Story:** CRUD de plantas com validação e regras de segurança.

**Regras de Negócio e Suposições:**

- Código único, numérico e obrigatório
- Descrição opcional, max 10 caracteres
- Apenas administradores podem deletar

**Estratégia de Testes:**

- Unidade e integração
- Edge cases: duplicidade, código não numérico, descrição longa, usuário não-admin tentando deletar

---

## 9. Testes da Funcionalidade de Cadastro de Usuário

**Tipos de testes:** Unidade, Integração, E2E\
**Edge Cases:** campos vazios, e-mail inválido, caracteres especiais, acesso não-admin

```java
@Test
Function test_deve_lancar_excecao_ao_salvar_usuario_com_email_duplicado() {
    User usuarioExistente = new User("John Doe", "john.doe@example.com");
    userRepository.save(usuarioExistente);

    User novoUsuarioComEmailIgual = new User("Jane Doe", "john.doe@example.com");
    assertThrows(EmailDuplicadoException.class, () -> {
        userService.salvar(novoUsuarioComEmailIgual);
    });
}
```
