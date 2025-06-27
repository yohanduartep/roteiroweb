# Roteiro REST API

#### 1. Acesse o site [https://www.postman.com/downloads/](https://www.postman.com/downloads/) para fazer download do Postman.

#### 2. Abra e execute o projeto LivrariaJPA, realizado no [Roteiro JPA](https://github.com/yohanduartep/roteiroweb/blob/main/JPA/RoteiroJPA.md).

#### 3. Popule o banco de dados com alguns autores e publishers:
```sql
insert into tb_autor values(gen_random_uuid(), 'Autor 1');
insert into tb_autor values(gen_random_uuid(), 'Autor 2');
insert into tb_publisher values(gen_random_uuid(), 'Publisher A');
insert into tb_publisher values(gen_random_uuid(), 'Publisher B');
//caso gen_random_uuid() não funcione em seu banco de dados, utilize UUID()
```

#### 4. Abra a clase LivroController.java e verifique o @RequestMapping utilizado
```java
...
@RestController
@RequestMapping("/livrariajpa/livros")
...
```

#### 5. Em seu postman, adicione uma nova Coleção em branco, renomeie-a para API Livraria, clique com o botão direito na Coleção ou nos três pontos e adicione um Request.

#### 6. Selecione a Request criada e altere-a para Post. Para isso, clique no menu dropdown e selecione POST e na url insira:
```
http://localhost:8080/livrariajpa/livros
```

#### 7. Após isso, clique na aba Body e insira os seguintes dados:
```
{
    "titulo": "exemplo de titulo de livro",
    "publisherId": "id da sua publisher no banco de dados",
    "autoresIds": ["id de um ou mais autores no banco de dados"], 
    "reviewComentario": "exemplo de comentario"
}
```

#### 8. Altere os valores dentro dos campos titulo e reviewComentario como preferir. Parar publisherId e autoresIds, descubra os IDs gerados pelo banco executando as seguintes linhas:
```sql
select * from tb_autor;
select * from tb_publisher;
```

#### 9. Após isso, clique em Send para submeter um novo livro.

#### 10. Realize os passos 5 e 6 novamente, alterando o método para GET, e clique em Send para obter a lista de livros criada.

#### 11. Copie o ID do livro (pode ser obtido pelo método GET ou no banco de dados), realize os passos 5 e 6, alterando o método para DELETE. Na url insira:
```
http://localhost:8080/livrariajpa/livros/*seu_id_aqui*
```

#### 12. Após inserir a url e colocar o ID do livro, clique em Send para deletá-lo. Verifique no método GET se a deleção realmente ocorreu.

#### 13. Insira um novo livro (Utilize o método POST) e anote o ID.

#### 14. Duplique o método POST, e no menu dropdown, selecione PUT e insira na url, juntamente com o ID obtido no passo anterior:
```
http://localhost:8080/livrariajpa/livros/*seu_id_aqui*
```

#### 15. Na aba Body, realize alguma alteração em um dos campos (caso queira alterar algum ID lembre-se de usar um ID existente):
```
{
    "titulo": "um novo exemplo de titulo de livro",
    "publisherId": "id da sua publisher no banco de dados",
    "autoresIds": ["id de um ou mais autores no banco de dados"], 
    "reviewComentario": "um novo exemplo de comentario"
}
```

#### 16. Clique em Send e verifique se o livro foi alterado pelo retorno do método ou pelo método GET.
