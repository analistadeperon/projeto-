# projeto-

Criando o projeto


Para este projeto vamos usar as seguintes dependências:

Spring Data JPA

H2 Database

Lombok

Acesse o site https://start.spring.io selecionando as dependências conforme abaixo 




Escolha a versão Java que você utiliza e clique em Generate para gerar os arquivos e em seguida faça a importação do arquivo pom.xml para a sua IDE. 


Maven

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.4</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.blog.spring.data</groupId>
    <artifactId>spring-data</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>spring-data</name>
    <description>Demo project for Spring Boot</description>
    <properties>
        <java.version>8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

        <dependency>
            <groupId>com.h2database</groupId>
            <artifactId>h2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <excludes>
                        <exclude>
                            <groupId>org.projectlombok</groupId>
                            <artifactId>lombok</artifactId>
                        </exclude>
                    </excludes>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

Sobre o projeto


Neste projeto vamos trabalhar com base na classe Employee. Faremos uma carga inicial para o H2 que é um banco de dados em memória e em seguida vamos explorar mais o Spring Data criando algumas consultas utilizando apenas uma Interface Java.


Classe Employee


A classe Employee representará o modelo principal do projeto

package com.blog.spring.data.springdata.model;

import lombok.AllArgsConstructor;
import lombok.Data;
import lombok.NoArgsConstructor;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Data
@AllArgsConstructor
@NoArgsConstructor
@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    private String firstName;
    private String lastName;
    private String position;

    @Override
    public String toString() {
        return String.format("Employee [ID: %o," +
                "FistName: %s," +
                "LastName: %s," +
                "Position: %s]",
                getId(), 
                getFirstName(), 
                getLastName(), 
                getPosition());
    }
}
Observações sobre a classe acima:


Anotação @Data

A anotação @Data é um recurso do Lombok  que facilita bastante a criação de modelos deixando o código menos verboso. Perceba que não foi necessário criar os getters e setters das propriedades graças ao uso do Lombok.


@AllArgsConstructor e @NoArgsConstructor

 Também são recursos do Lombok

@AllArgsConstructor:

Possibilita criar um construtor de forma implícita.

@NoArgsConstructor:

Não obriga a passagem de argumentos para o construtor.


@Entity 

Recurso do JPA que possibilita identificar a classe Employee como uma entidade de banco de dados. 


@Id

 Recurso do JPA utilizado para identificar um campo que deverá ser um identificar único no modelo de dados. Em resumo, a classe Employee por ter sido anotada como @Entity representará uma entidade a nível de banco de dados/tabela para a aplicação, e o @Id permite criar o campo id como um identificador único, ou seja, uma chave primária da entidade Employee.


@GeneratedValue(strategy = GenerationType.AUTO)

Recurso que permite controlar a geração dos valores únicos para o campo Id. Perceba que é necessário escolher a estratégia para gerar estes valores. Isso depende de cada engine de banco de dados, neste caso estamos utilizando o H2, mas caso utilizamos Oracle, a estratégia seria GenerationType.SEQUENCE.


Criando o repositório EmployeeRepository


No contexto de projeto Java, utilizamos algumas convenções de nomes para identificar recursos, neste campo vamos criar uma classe que servirá como repositório de dados para a entidade Employee.


Para trabalhar com Spring Data, os repositórios são bem importantes e devem ser criados como Interfaces Java herdando a Interface CrudRepository veja o código abaixo.


package com.blog.spring.data.springdata.repository;

import com.blog.spring.data.springdata.model.Employee;
import org.springframework.data.repository.CrudRepository;
import org.springframework.stereotype.Repository;
import java.util.List;
import java.util.Optional;

@Repository
public interface EmployeeRepository extends CrudRepository<Employee, Long> {

    Optional<Employee> findById(Long id);

    List<Employee> findByFirstName(String firstName);

    List<Employee> findByLastName(String lastName);

    List<Employee> findByPosition(String position);

    List<Employee> findByFirstNameAndPosition(String firstName,
                                              String position);
}
Entendendo em partes:

@Repository

Identifica esta interface como um repositório no contexto do Spring


CrudRepository

Herdar (extends) a interface CrudRepository possibilita utilizar os recursos oferecidos pelo Spring Data. Neste caso é necessário passar como parâmetro a entidade (Employee) e o tipo do identificador único da classe Employee, que seria o campo id (Long)


Métodos

Esta é a parte mais importante deste Post. Perceba que para cada método se inicia com  findBy e em seguida o nome do campo da classe Employee combinada ou não com um operador (AND) e um parâmetro. Todos estes métodos equivale a uma consulta SQL, perceba que não foi necessário criar scripts SQL de forma explicita. Isso é um dos recursos mais poderosos quando se utiliza Spring Data.


Optional<Employee> findById(Long id)

Retorno: Um objeto do tipo Employee

SQL: select * from Employee where id = ?


List<Employee> findByFirstName(String firstName)

Retorno: Uma lista de objetos do tipo Employee

SQL: select * from Employee where firstName = "?"


List<Employee> findByLastName(String lastName)

Retorno: Uma lista de objetos do tipo Employee

SQL:  select * from Employee where lastName = "?"


List<Employee> findByPosition(String position)

Retorno: Uma lista de objetos do tipo Employee

SQL: select * from Employee where position = "?"


 List<Employee> findByFirstNameAndPosition(String firstName, String position)

Retorno: Uma lista de objetos do tipo Employee

SQL: select * from Employee where firstName = "?" and position = "?"
                                              

Creio que agora está mais claro o funcionamento do Spring Data, agora vamos explorar na prática.


Criando a classe EmployeeService


Nesta classe criaremos a parte negocial onde invocaremos o repositório criado anteriormente.


package com.blog.spring.data.springdata.service;

import com.blog.spring.data.springdata.model.Employee;
import com.blog.spring.data.springdata.repository.EmployeeRepository;
import org.springframework.stereotype.Service;
import java.util.ArrayList;
import java.util.List;

@Service
public class EmployeeService {

    static EmployeeRepository employeeRepository;

    public EmployeeService(EmployeeRepository
                                   employeeRepository){
        this.employeeRepository = employeeRepository;
    }

    public static void populateEmployeeToDataBase(){

        List<Employee> employees = new ArrayList<>();

        employees.add(new Employee(1L,
                "Monica",
                "Silva",
                "Director"));

        employees.add(new Employee(2L,
                "Jessica",
                "Reies",
                "Coordinator"));

        employees.add(new Employee(3L,
                "Milena",
                "Torres",
                "Dev"));


        employees.add(new Employee(4L,
                "Maura",
                "Sanches",
                "Dev"));

        employeeRepository.saveAll(employees);
    }


    public void runQueries(){

        populateEmployeeToDataBase();

        employeeRepository.findById(3L)
                .ifPresent(System.out::println);

        employeeRepository.findByFirstName("Monica")
                .ifPresent(System.out::println);

        employeeRepository
                .findByFirstNameAndPosition(
                        "Maura",
                        "Dev")
                .ifPresent(System.out::println);

        employeeRepository.findByPosition("Dev")
                .ifPresent(System.out::println);

    }
}
A classe EmployeeService será responsável por popular na base uma lista de objetos do tipo Employee e executar os métodos para acesso a base na classe EmployeeRepository.


Executando o código


Para a execução do código, vamos criar a classe principal do projeto chamada SpringDataApplication.java, seguindo os padrões de um projeto Spring Boot.

package com.blog.spring.data.springdata;

import com.blog.spring.data.springdata.service.EmployeeService;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringDataApplication {

    private static EmployeeService employeeService;

    public SpringDataApplication(EmployeeService
                                         employeeService){
        this.employeeService = employeeService;
    }

    public static void main(String[] args) {
        SpringApplication.run(SpringDataApplication.class, args);
        employeeService.runQueries();
    }
}
Esta classe possui 2 particularidades:

A anotação @SpringBootApplication

Esta anotação permite identificar esta classe como a principal para o contexto do Spring Boot e será responsável por iniciar a aplicação 


O método main

Método responsável pela inicialização da aplicação e para este tutorial a invocação do método runQueries que criamos anteriormente.

Resultado da execução


Employee  [ID: 3,FistName: Milena,LastName: Torres,Position: Dev]


[Employee  [ID: 1,FistName: Monica,LastName: Silva,Position: Director]]


[Employee  [ID: 4,FistName: Maura,LastName: Sanches,Position: Dev]]


[Employee  [ID: 3,FistName: Milena,LastName: Torres,Position: Dev], Employee  [ID: 4,FistName: Maura,LastName: Sanches,Position: Dev]]


Todas estas linhas acima são resultados de consultas SQL de forma implícita executadas pelos métodos em que criamos na classe EmployeeRepository.


Percebeu o poder do Spring Data sem a necessidade de criar consultas SQL no código?


Curtiu? Até mais!


JavaSpring BootSpring DataH2JPA
Spring Boot
•
Java
•
SQL
Post não marcado como curtido
Posts recentes
Ver tudo
Criando um projeto Spring Boot em 2 minutos
Post não marcado como curtido
Coffee and Tips

© 2021
 



