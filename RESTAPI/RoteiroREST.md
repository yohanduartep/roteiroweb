# Roteiro REST API

Este roteiro usa o Postman para testar os endpoints do `LivroController`: criar, listar, atualizar e remover livros.

#### 1. Acesse o site [https://www.postman.com/downloads/](https://www.postman.com/downloads/) para fazer download do Postman.

#### 2. Abra e execute o projeto LivrariaJPA, realizado no [Roteiro JPA](https://github.com/yohanduartep/roteiroweb/blob/main/JPA/RoteiroJPA.md).

#### 3. Altere pom.xml para incluir a dependência do Springdoc dentro da tag `<dependencies>`:

```xml
<dependency>
    <groupId>org.springdoc</groupId>
    <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
    <version>3.0.0</version>
</dependency>
```

Apos inserir, pressione Ctrl Shift I para baixar a dependência.

#### 4. Em seguida, configure o caminho do Swagger UI acessando o arquivo application.properties e inserindo a seguinte linha:

```
springdoc.swagger-ui.path=/swagger-ui.html
```

#### 5. Agora a documentação da API pode ser acessada em:

```
http://localhost:8080/swagger-ui.html
http://localhost:8080/v3/api-docs
```

#### 6. Abra a classe LivroController.java e verifique o @RequestMapping utilizado

```java
...
@RestController
@RequestMapping("/livrariajpa/livros")
...
```

#### 7. Em seu Postman, adicione uma nova Coleção em branco, renomeie-a para API Livraria, clique com o botão direito na Coleção ou nos três pontos e adicione um Request.

#### 8. Selecione a Request criada e altere-a para Post. Para isso, clique no menu dropdown e selecione POST e na url insira:

```
http://localhost:8080/livrariajpa/livros
```

#### 9. Após isso, clique na aba Body e insira os seguintes dados:

```
{
    "titulo": "exemplo de titulo de livro",
    "publisherId": "id da sua publisher no banco de dados",
    "autoresIds": ["id de um ou mais autores no banco de dados"],
    "reviewComentario": "exemplo de comentario"
}
```

#### 10. Altere os valores dentro dos campos titulo e reviewComentario como preferir. Para publisherId e autoresIds, podemos descobrir os IDs gerados pelo banco executando as seguintes linhas:

```sql
select * from tb_autor;
select * from tb_publisher;
```

#### 11. Ou podemos utilizar o método GET para obter a lista de livros, que contem autores e publishers. Para isso, crie uma nova Request, altere o método para GET e insira a seguinte url:

```
http://localhost:8080/livrariajpa/livros
```

#### 12. Após obtermos os IDs, clique em Send para submeter um novo livro no metodo POST do passo 8 e 9. Verifique se o livro foi inserido corretamente pelo retorno do método ou pelo método GET criado no passo 11.

#### 13. Copie o ID do livro (pode ser obtido pelo método GET ou no banco de dados), realize os passos 7 e 8, alterando o método para DELETE. Na url insira:

```
http://localhost:8080/livrariajpa/livros/*seu_id_aqui*
```

#### 14. Após inserir a url e colocar o ID do livro, clique em Send para deletá-lo. Realize o passo 11 e verifique se a deleção ocorreu.

#### 15. Insira um novo livro (Utilize o método POST) e anote o ID.

#### 16. Duplique o método POST, e no menu dropdown, selecione PUT e insira na url, juntamente com o ID obtido no passo anterior:

```
http://localhost:8080/livrariajpa/livros/*seu_id_aqui*
```

#### 17. Na aba Body, realize alguma alteração em um dos campos (caso queira alterar algum ID lembre-se de usar um ID existente):

```
{
    "titulo": "um novo exemplo de titulo de livro",
    "publisherId": "id da sua publisher no banco de dados",
    "autoresIds": ["id de um ou mais autores no banco de dados"],
    "reviewComentario": "um novo exemplo de comentario"
}
```

#### 18. Clique em Send e verifique se o livro foi alterado pelo retorno do método ou pelo método GET.
