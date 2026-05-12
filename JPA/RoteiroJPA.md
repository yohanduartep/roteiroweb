# Roteiro JPA

Este roteiro mostra como persistir objetos Java em um banco de dados usando Spring Data JPA, sem escrever manualmente todo o SQL de CRUD.

O exemplo será uma livraria com livros, autores, editoras e reviews.

A API web será usada aqui apenas para testar o CRUD. O roteiro de REST API aprofunda os endpoints depois.

#### 1. Criar um novo projeto Spring (https://start.spring.io/)

    Project: Maven Project

    Language: Java

    Spring Boot: 3.4.6

    Group: br.ufscar.dc.dsw

    Artifact: LivrariaJPA

    Name: LivrariaJPA

    Description: LivrariaJPA

    Package name: br.ufscar.dc.dsw

    Packaging: Jar

    Java: 21

    Dependências: Spring Web, Spring Data JPA e Spring Boot DevTools, *Banco de dados à sua escolha
    (Selecione a opção Driver)

#### 2. Baixar e extrair o .zip para abrir o projeto no IntelliJ

#### 3. Em /src/main/resources/application.properties, insira as seguintes linhas, removendo os comentários referentes ao banco de dados escolhido:

    spring.application.name=LivrariaJPA

    # MARIADB
    # spring.datasource.url=jdbc:mariadb://localhost:3306/livrariajpa?createDatabaseIfNotExist=true
    # spring.datasource.driver-class-name=org.mariadb.jdbc.Driver

    # MYSQL
    # spring.datasource.url = jdbc:mysql://localhost:3306/livrariajpa?createDatabaseIfNotExist=true
    # spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

    # POSTGRES
    # spring.datasource.url=jdbc:postgresql://localhost:3306/livrariajpa?createDatabaseIfNotExist=true
    # spring.datasource.driver-class-name=org.postgresql.Driver

    # DERBY
    # spring.datasource.url=jdbc:derby://localhost:3306/livrariajpa
    # spring.datasource.driver-class-name=org.apache.derby.jdbc.ClientDriver

    spring.datasource.username=root
    spring.datasource.password=root

    spring.jpa.hibernate.ddl-auto=update
    spring.jpa.properties.hibernate.jdbc.lob.non_contextual_creation=true
    spring.jpa.show-sql=true
    spring.jpa.open-in-view = true

Essas configurações definem a conexão com o banco e o comportamento do Hibernate:

- `spring.datasource.url`, `driver-class-name`, `username` e `password`: dados de conexão com o banco escolhido;
- `spring.jpa.hibernate.ddl-auto=update`: permite que o Hibernate atualize o schema do banco conforme as entidades;
- `spring.jpa.show-sql=true`: mostra no console os SQLs gerados pelo Hibernate;
- `spring.jpa.open-in-view=true`: mantém a sessão JPA aberta durante a renderização da resposta web (`default = true`).

#### 4. Criar package models onde serão inseridas as classes:

As classes de `models` representam as tabelas. `@Entity` marca a classe como persistente e `@Table` define o nome da tabela.

Serializable permite converter objetos em bytes, sendo usado em entidades JPA por compatibilidade com frameworks e cache.

##### LivroModel.java

```java
package br.ufscar.dc.dsw.models;

import jakarta.persistence.*;
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "TB_LIVRO")
public class LivroModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, unique = true)
    private String titulo;

    @ManyToMany
    @JoinTable(
            name = "tb_livro_autor",
            joinColumns = @JoinColumn(name = "livro_id"),
            inverseJoinColumns = @JoinColumn(name = "autor_id")
    )
    private Set<AutorModel> autores = new HashSet<>();

    @ManyToOne
    @JoinColumn(name = "publisher_id")
    private PublisherModel publisher;

    @OneToOne(mappedBy = "livro", cascade = CascadeType.ALL)
    private ReviewModel review;

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getTitulo() {
        return titulo;
    }

    public void setTitulo(String titulo) {
        this.titulo = titulo;
    }

    public PublisherModel getPublisher() {
        return publisher;
    }

    public void setPublisher(PublisherModel publisher) {
        this.publisher = publisher;
    }

    public Set<AutorModel> getAutores() {
        return autores;
    }

    public void setAutores(Set<AutorModel> autores) {
        this.autores = autores;
    }

    public ReviewModel getReview() {
        return review;
    }

    public void setReview(ReviewModel review) {
        this.review = review;
    }
}
```

##### PublisherModel.java

```java
package br.ufscar.dc.dsw.models;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.persistence.*;
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "TB_PUBLISHER")
public class PublisherModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, unique = true)
    private String nome;

    //@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    //@OneToMany(mappedBy = "publisher", fetch = FetchType.LAZY)
    @OneToMany(mappedBy = "publisher")
    private Set<LivroModel> livros = new HashSet<>();

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public Set<LivroModel> getLivros() {
        return livros;
    }

    public void setLivros(Set<LivroModel> livros) {
        this.livros = livros;
    }
}
```

##### AutorModel.java

```java
package br.ufscar.dc.dsw.models;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.persistence.*;
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "TB_AUTOR")
public class AutorModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, unique = true)
    private String nome;

    //@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    //@ManyToMany(mappedBy = "autores", fetch = FetchType.LAZY)
    @ManyToMany(mappedBy = "autores")
    private Set<LivroModel> livros = new HashSet<>();

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getNome() {
        return nome;
    }

    public void setNome(String nome) {
        this.nome = nome;
    }

    public Set<LivroModel> getLivros() {
        return livros;
    }

    public void setLivros(Set<LivroModel> livros) {
        this.livros = livros;
    }
}
```

##### ReviewModel.java

```java
package br.ufscar.dc.dsw.models;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.persistence.*;
import java.io.Serializable;
import java.util.UUID;

@Entity
@Table(name = "TB_REVIEW")
public class ReviewModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false)
    private String comentario;

    //@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @OneToOne
    @JoinColumn(name = "livro_id")
    private LivroModel livro;

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getComentario() {
        return comentario;
    }

    public void setComentario(String comentario) {
        this.comentario = comentario;
    }

    public LivroModel getLivro() {
        return livro;
    }

    public void setLivro(LivroModel livro) {
        this.livro = livro;
    }
}
```

Observações sobre os relacionamentos:

- `LivroModel.publisher`: `@ManyToOne`; a chave estrangeira `publisher_id` fica em `TB_LIVRO`.
- `PublisherModel.livros`: `@OneToMany(mappedBy = "publisher")`; lado inverso, não cria nova chave estrangeira.
- `LivroModel.autores`: `@ManyToMany`; `@JoinTable` cria a tabela intermediária `tb_livro_autor`.
- `AutorModel.livros`: `@ManyToMany(mappedBy = "autores")`; lado inverso do relacionamento.
- `ReviewModel.livro`: `@OneToOne` com `@JoinColumn(name = "livro_id")`; a chave estrangeira fica em `TB_REVIEW`.
- `LivroModel.review`: `@OneToOne(mappedBy = "livro")`; lado inverso do relacionamento.
- Defaults: Por padrão, @ManyToOne e @OneToOne usam EAGER, enquanto @OneToMany e @ManyToMany usam LAZY.

#### 5. Criar package repositories onde serão inseridas as classes:

Os repositórios centralizam acesso ao banco. Ao estender `JpaRepository`, a interface já recebe métodos prontos, como `save`, `findById`, `findAll`, `deleteById`, `existsById` e `count`.
Por isso, mesmo interfaces vazias como `AutorRepository` já conseguem executar operações básicas de CRUD.

##### LivroRepository.java

```java
package br.ufscar.dc.dsw.repositories;

import br.ufscar.dc.dsw.models.LivroModel;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import java.util.List;
import java.util.UUID;

public interface LivroRepository extends JpaRepository <LivroModel, UUID> {
    LivroModel findLivroModelByTitulo(String titulo);

    @Query(value = "SELECT * FROM tb_livro WHERE publisher_id=  :id", nativeQuery = true)
    List<LivroModel> findLivroModelByPublisherId(@Param("id") UUID id);
}
```

##### PublisherRepository.java

```java
package br.ufscar.dc.dsw.repositories;

import br.ufscar.dc.dsw.models.PublisherModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface PublisherRepository extends JpaRepository<PublisherModel, UUID> {
}
```

##### AutorRepository.java

```java
package br.ufscar.dc.dsw.repositories;

import br.ufscar.dc.dsw.models.AutorModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface AutorRepository extends JpaRepository<AutorModel, UUID> {
}
```

##### ReviewRepository.java

```java
package br.ufscar.dc.dsw.repositories;

import br.ufscar.dc.dsw.models.ReviewModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface ReviewRepository extends JpaRepository<ReviewModel, UUID> {
}
```

#### 6. Criar package dtos e inserir a classe LivroRecordDto.java

O DTO representa dados recebidos pela API e será usado para criar o `LivroModel`.

```java
package br.ufscar.dc.dsw.dtos;

import java.util.Set;
import java.util.UUID;

public record LivroRecordDto(String titulo,
                            UUID publisherId,
                            Set<UUID> autoresIds,
                            String reviewComentario) {
}
```

#### 7. Criar package services e inserir a classe LivroService.java

O serviço busca editora/autores, cria o review e salva o livro.

```java
package br.ufscar.dc.dsw.services;

import br.ufscar.dc.dsw.dtos.LivroRecordDto;
import br.ufscar.dc.dsw.models.LivroModel;
import br.ufscar.dc.dsw.models.ReviewModel;
import br.ufscar.dc.dsw.repositories.AutorRepository;
import br.ufscar.dc.dsw.repositories.LivroRepository;
import br.ufscar.dc.dsw.repositories.PublisherRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;

@Service
public class LivroService {

     private final LivroRepository livroRepository;
     private final AutorRepository autorRepository;
     private final PublisherRepository publisherRepository;

    public LivroService(LivroRepository livroRepository, AutorRepository autorRepository, PublisherRepository publisherRepository) {
        this.livroRepository = livroRepository;
        this.autorRepository = autorRepository;
        this.publisherRepository = publisherRepository;
    }

    public List<LivroModel> getAllLivros() {
        return livroRepository.findAll();
    }

    @Transactional
    public LivroModel saveLivro(LivroRecordDto livroRecordDto) {
        LivroModel livro = new LivroModel();
        livro.setTitulo(livroRecordDto.titulo());
        livro.setPublisher(publisherRepository.findById(livroRecordDto.publisherId()).get());
        livro.setAutores(autorRepository.findAllById(livroRecordDto.autoresIds()).stream().collect(Collectors.toSet()));

        ReviewModel reviewModel = new ReviewModel();
        reviewModel.setComentario(livroRecordDto.reviewComentario());
        reviewModel.setLivro(livro);
        livro.setReview(reviewModel);

        return livroRepository.save(livro);
    }

    @Transactional
    public LivroModel updateLivro(UUID id, LivroRecordDto livroRecordDto) {
        LivroModel livro = livroRepository.findById(id).get();
        livro.setTitulo(livroRecordDto.titulo());
        livro.setPublisher(publisherRepository.findById(livroRecordDto.publisherId()).get());
        livro.setAutores(autorRepository.findAllById(livroRecordDto.autoresIds()).stream().collect(Collectors.toSet()));

        ReviewModel review = livro.getReview();
        if (review == null) {
            review = new ReviewModel();
            review.setLivro(livro);
            livro.setReview(review);
        }
        review.setComentario(livroRecordDto.reviewComentario());

        return livroRepository.save(livro);
    }

    @Transactional
    public void deleteLivro(UUID id) {
        livroRepository.deleteById(id);
    }

}
```

Neste trecho:

```java
autorRepository.findAllById(livroRecordDto.autoresIds()).stream().collect(Collectors.toSet())
```

`findAllById` retorna os autores encontrados. O `stream()` percorre essa coleção e `collect(Collectors.toSet())` converte o resultado para `Set<AutorModel>`, o tipo usado em `LivroModel.autores`.

#### 8. Criar package controllers e inserir a classe LivroController.java

O controller expõe os endpoints HTTP usados para testar a persistência. O uso com Postman fica para o roteiro de REST API.

```java
package br.ufscar.dc.dsw.controllers;

import br.ufscar.dc.dsw.dtos.LivroRecordDto;
import br.ufscar.dc.dsw.models.LivroModel;
import br.ufscar.dc.dsw.services.LivroService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.UUID;

@RestController
@RequestMapping("/livrariajpa/livros")
public class LivroController {
    private final LivroService livroService;
    public LivroController(LivroService livroService) {
        this.livroService = livroService;
    }

    @GetMapping
    public ResponseEntity<List<LivroModel>> getAllLivros() {
        return ResponseEntity.status(HttpStatus.OK).body(livroService.getAllLivros());
    }

    @PostMapping
    public ResponseEntity<LivroModel> saveLivro(@RequestBody LivroRecordDto livroRecordDto) {
        return ResponseEntity.status(HttpStatus.CREATED).body(livroService.saveLivro(livroRecordDto));
    }

    @PutMapping("/{id}")
    public ResponseEntity<LivroModel> updateLivro(@PathVariable UUID id, @RequestBody LivroRecordDto livroRecordDto) {
        return ResponseEntity.status(HttpStatus.OK).body(livroService.updateLivro(id, livroRecordDto));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteLivro(@PathVariable UUID id) {
        livroService.deleteLivro(id);
        return ResponseEntity.status(HttpStatus.OK).body("Livro com id " + id + " foi deletado com sucesso.");
    }
}
```

#### 9. Inserir alguns valores no banco de dados

```
insert into tb_autor values(UUID(), 'Autor 1');
insert into tb_autor values(UUID(), 'Autor 2');
insert into tb_publisher values(UUID(), 'Publisher A');
insert into tb_publisher values(UUID(), 'Publisher B');
//caso esteja usando PostgreSQL, substitua UUID() por gen_random_uuid()
```
