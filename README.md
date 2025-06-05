# Roteiro JPA

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

#### 3. Criar package models onde serão inseridas as classes:

##### BookModel.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.models;

import jakarta.persistence.*;
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "TB_BOOK")
public class BookModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, unique = true)
    private String title;

    @ManyToOne
    @JoinColumn(name = "publisher_id")
    private PublisherModel publisher;

    //@JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @ManyToMany//(fetch = FetchType.LAZY)
    @JoinTable(
            name = "tb_book_author",
            joinColumns = @JoinColumn(name = "book_id"),
            inverseJoinColumns = @JoinColumn(name = "author_id")
    )
    private Set<AuthorModel> authors = new HashSet<>();

    @OneToOne(mappedBy = "book", cascade = CascadeType.ALL)
    private ReviewModel review;

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }

    public PublisherModel getPublisher() {
        return publisher;
    }

    public void setPublisher(PublisherModel publisher) {
        this.publisher = publisher;
    }

    public Set<AuthorModel> getAuthors() {
        return authors;
    }

    public void setAuthors(Set<AuthorModel> authors) {
        this.authors = authors;
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
package br.ufscar.dc.dsw.LivrariaJPA.models;

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
    private String name;

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @OneToMany(mappedBy = "publisher", fetch = FetchType.LAZY)
    private Set<BookModel> books = new HashSet<>();

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Set<BookModel> getBooks() {
        return books;
    }

    public void setBooks(Set<BookModel> books) {
        this.books = books;
    }
}
```

##### AuthorModel.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.models;

import com.fasterxml.jackson.annotation.JsonProperty;
import jakarta.persistence.*;
import java.io.Serializable;
import java.util.HashSet;
import java.util.Set;
import java.util.UUID;

@Entity
@Table(name = "TB_AUTHOR")
public class AuthorModel implements Serializable {
    private static final long serialVersionUID = 1L;

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private UUID id;

    @Column(nullable = false, unique = true)
    private String name;

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @ManyToMany(mappedBy = "authors", fetch = FetchType.LAZY)
    private Set<BookModel> books = new HashSet<>();

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Set<BookModel> getBooks() {
        return books;
    }

    public void setBooks(Set<BookModel> books) {
        this.books = books;
    }
}
```

##### ReviewModel.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.models;

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
    private String comment;

    @JsonProperty(access = JsonProperty.Access.WRITE_ONLY)
    @OneToOne
    @JoinColumn(name = "book_id")
    private BookModel book;

    public UUID getId() {
        return id;
    }

    public void setId(UUID id) {
        this.id = id;
    }

    public String getComment() {
        return comment;
    }

    public void setComment(String comment) {
        this.comment = comment;
    }

    public BookModel getBook() {
        return book;
    }

    public void setBook(BookModel book) {
        this.book = book;
    }
}
```

#### 4. Criar package repositories onde serão inseridas as classes:

##### BookRepository.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.repositories;

import br.ufscar.dc.dsw.LivrariaJPA.models.BookModel;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import java.util.List;
import java.util.UUID;

public interface BookRepository extends JpaRepository <BookModel, UUID> {
    BookModel findBookModelByTitle(String title);

    @Query(value = "SELECT * FROM tb_book WHERE publisher_id=  :id", nativeQuery = true)
    List<BookModel> findBookModelByPublisherId(@Param("id") UUID id);
}
```

##### PublisherRepository.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.repositories;

import br.ufscar.dc.dsw.LivrariaJPA.models.PublisherModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface PublisherRepository extends JpaRepository<PublisherModel, UUID> {
}
```

##### AuthorRepository.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.repositories;

import br.ufscar.dc.dsw.LivrariaJPA.models.AuthorModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface AuthorRepository extends JpaRepository<AuthorModel, UUID> {
}
```

##### ReviewRepository.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.repositories;

import br.ufscar.dc.dsw.LivrariaJPA.models.ReviewModel;
import org.springframework.data.jpa.repository.JpaRepository;
import java.util.UUID;

public interface ReviewRepository extends JpaRepository<ReviewModel, UUID> {
}
```

#### 5. Criar package dtos e inserir a classe BookRecordDto.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.dtos;

import java.util.Set;
import java.util.UUID;

public record BookRecordDto(String title,
                            UUID publisherId,
                            Set<UUID> authorIds,
                            String reviewComment) {
}
```

#### 6. Criar package services e inserir a classe BookService.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.services;

import br.ufscar.dc.dsw.LivrariaJPA.dtos.BookRecordDto;
import br.ufscar.dc.dsw.LivrariaJPA.models.BookModel;
import br.ufscar.dc.dsw.LivrariaJPA.models.ReviewModel;
import br.ufscar.dc.dsw.LivrariaJPA.repositories.AuthorRepository;
import br.ufscar.dc.dsw.LivrariaJPA.repositories.BookRepository;
import br.ufscar.dc.dsw.LivrariaJPA.repositories.PublisherRepository;
import jakarta.transaction.Transactional;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.UUID;
import java.util.stream.Collectors;

@Service
public class BookService {

     private final BookRepository bookRepository;
     private final AuthorRepository authorRepository;
     private final PublisherRepository publisherRepository;

    public BookService(BookRepository bookRepository, AuthorRepository authorRepository, PublisherRepository publisherRepository) {
        this.bookRepository = bookRepository;
        this.authorRepository = authorRepository;
        this.publisherRepository = publisherRepository;
    }

    public List<BookModel> getAllBooks() {
        return bookRepository.findAll();
    }

    @Transactional
    public BookModel saveBook(BookRecordDto bookRecordDto) {
        BookModel book = new BookModel();
        book.setTitle(bookRecordDto.title());
        book.setPublisher(publisherRepository.findById(bookRecordDto.publisherId()).get());
        book.setAuthors(authorRepository.findAllById(bookRecordDto.authorIds()).stream().collect(Collectors.toSet()));

        ReviewModel reviewModel = new ReviewModel();
        reviewModel.setComment(bookRecordDto.reviewComment());
        reviewModel.setBook(book);
        book.setReview(reviewModel);

        return bookRepository.save(book);
    }

    @Transactional
    public void deleteBook(UUID id) {
        bookRepository.deleteById(id);
    }

}
```

#### 7. Criar package controllers e inserir a classe BookController.java

```java
package br.ufscar.dc.dsw.LivrariaJPA.controllers;

import br.ufscar.dc.dsw.LivrariaJPA.dtos.BookRecordDto;
import br.ufscar.dc.dsw.LivrariaJPA.models.BookModel;
import br.ufscar.dc.dsw.LivrariaJPA.services.BookService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import java.util.List;
import java.util.UUID;

@RestController
@RequestMapping("/livrariajpa/livros")
public class BookController {
    private final BookService bookService;
    public BookController(BookService bookService) {
        this.bookService = bookService;
    }

    @GetMapping
    public ResponseEntity<List<BookModel>> getAllBooks() {
        return ResponseEntity.status(HttpStatus.OK).body(bookService.getAllBooks());
    }

    @PostMapping
    public ResponseEntity<BookModel> saveBook(@RequestBody BookRecordDto bookRecordDto) {
        return ResponseEntity.status(HttpStatus.CREATED).body(bookService.saveBook(bookRecordDto));
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<String> deleteBook(@PathVariable UUID id) {
        bookService.deleteBook(id);
        return ResponseEntity.status(HttpStatus.OK).body("Book with id " + id + " was deleted");
    }
}
```

#### 8. Inserir uns valores no banco de dados

```
insert into tb_author values(UUID(), 'Harvey Deitel');
insert into tb_author values(UUID(), 'Paul Deitel');
insert into tb_publisher values(UUID(), 'Alta Books');
insert into tb_publisher values(UUID(), 'Pearson');
//caso usando mysql ou postegres, substitua UUID() por gen_random_uuid()
```
