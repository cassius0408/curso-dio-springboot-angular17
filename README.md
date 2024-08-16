
SpringBoot

Enquanto que o framework é baseado no padrão de injeção de dependências, o SpringBoot foca na configuração automática.
Foca na produtividade.

> Antes do SpringBoot
Desafios com a configuração do projeto.
- Dependencia individual
- Verbosidade
- Incompatibilidade de versões
- Complexidade de gestão
- Configurações complexas e repetitivas

Dado que a maior parte das configurações necessárias para o início de um projeto são sempre as mesmas, 
 por que não iniciar um projeto com todas estas configuraçõesjá definidas?


>Starters
São descritores de dependência.

Benefícios:
- Coesão
- Versões compatíveis
- Otimização do tempo
- Configuração simples
- Foco no negócio

Lista de alguns starters mais utilizados

Spring-boot-starter-*
* data-jpa: 	injeção ao banco de dados via JPA - Hibernate.
* data-mongodb: injeção ao banco de dados MongoDB.
* web: 			Inclusão do container Tomcat para aplicações REST.
* web-services: Webservices baseados na arquitetura SOAP.
* batch:		Implementação de JOBs de processos.
* test: 		Disponibilização de recursos para teste unitários como JUnit.
* Openfeign:	Client HTTP baseado em interfaces.
* acuator:		Gerenciamento de monitoramento da aplicação.


=================================================================


> Primeiros passos

Baixar o IDE Spring Tool 4 no site https://spring.io/tools (vai baixar um .jar)
Abrir o Windows Power Shell, como Administrador. Digitar o comando abaixo para descompactar o jar. 
cd 'C:\Desenvolvimento\IDEs\Spring Tool 4'
java -jar .\spring-tool-suite-4-4.24.0.RELEASE-e4.32.0-win32.win32.x86_64.self-extracting.jar


Criar o projeto no Spring initializr.

- Spring initializr
https://start.spring.io/


Quando usar as annotations @Bean e @Component

Usar @Component, quando tiver acesso ao código fonte.

Quando não tem acesso ao código fonte @Bean


Singleton - eu vou ter um único objeto sendo utilizado a cada necessidade.

Prototype - Eu vou ter uma instancia a cada necessidade.


>Properties Value

application.properties
E um arquivo de configuração. Pode manter estados, propriedades de contextos. 
Valores que nao terao mudancas seram centralizados no application.properties.

@Value
anotacao para obter o valor do application.properties..

@ConfiguratioProperties(prefix)
anotacao que no Bean TODOS os valores estaram vindo do application.properties.


> JPA - Java Persistence API

Conceito de ORM e JPA

- ORM 
Object-Reltional Mapping, em portugues, Mapeamento Objeto-Relacional,
 é um recurso para aproximar o paradigma da orientação a objetos ao contexto de banco de dados relacional.
 
O uso de ORM é realizado através do mapeamento de ojeto para uma tabelapor uma biblioteca ou framework.		


- JPA - Java Persistence API
JPA é uma especificação baseadaem interfaces, que através de um framework realiza operação de persistencia objetos em Java.
Exemplos de implementaçôes: 
Hibernate, aclipse link, Oracle TOP LINK e Open JPA.


- Mapeamentos 
Os aspectos das anotaçôes de mapeamento JPA
. Herançãoção
. Definição
. Relacionamento
. Herança
. Persistencia

@Entity
@Table(name="tb_usuario")
public class Usuario { 
	@ID
	@GeneratedValue(strategy=GenerationType.AUTO)
	@Column(name="id_usuario")
	private Long id;
	
	private String nome;
	
	@Column(name="login_usuario")
	private String login;
	
	@Column(name="senha_usuario")	
	private String senha;
}


- EntityManager

Java API - Hikari Implementation
	- Datasource
	- Connection Pool
	- Java API
		- Connection  <--|
		|				 |
		|		|--------|
		-> H2	|			- Hibernate
				|--------------- SessionImpl
					Uses				|			- JPA
										|------------>  EntityManager	<---------|
										Implements						Uses	  |	
																				  |  - Spring Data JPA
																				  |----	SimpleJpaRepository ----------|
																													  |
																													  | Implements
																						- Spring DataJPA			  |
																							- JpaRepository	<---------|


> Spring Data JPA 
O Spring Data JPA adiciona uma camada sobre o JPA. Isso significa que ele usa todos os recursos definidos pela especificação JPA, 
 especialmente os mapeamentos de entidade e os recursos de persistência baseada em interfaces e anotações. 
Por isso, o Spring Data JPA adiciona seus próprios recursos, 
 como uma Impplementação sem código do padrão de repositório e a criação de consultas de banco de dados a partir de nomes de métodos.

A interação com o banco de dados será feita através de Heranças de Interfaces e declaração de métodos com anotações.

Existem algumas interfaces e anotações que são super relevantes de se explorar como: 

Interfaces
 - CrudRepository
 - JpaRepository
 - PagingAndSortingRepository
 
Anotações
 - @Query
 - @Param


O projeto Spring Data Jpa facilita a implementação do padrão Repository através de AOP (Aspect Oriented Programming - programação orientada a aspectos).
Utilizando-se apenas de uma interface, o Spring irá "gerar" dinamicamente a implementação dos métodos de acesso a dados. 
Estender a interface 'JpaRepository' é opcional, mas a vantagem é ela já vem com vários métodos genéricos de CRUD e você não precisa redefinir todos eles.

> Principais métodos que já são disponibilizado pelo framework: 

. save			Insere e atualiza os dados de uma entidade.
. findById		Retorna o objeto localizado pelo ID.
. existsById	Verifica a existencia de um objeto pelo ID informado, retornando um boolean.
. findAll		Retorna uma coleção contendo todos os registros da tabela no banco de dados.
. delete	 	Deleta um registro da respectiva tabela mapeada do banco de dados.
. count      	Retorna a quantidade de registros de uma tabela mapeada do banco de dados. 


> Consultas customizadas
Existem duas maneiras de realizar consultas customizadas, uma é conhecida como 'QueryMethod' a a outro é 'QueryOverride'.

QueryMethod
O Spring Data JPA se encarrega de interpretar a assinatura de um método (nome + parâmetros) paramontar o JQPL coerespondente.
Vanos um exemplo de uma entidade que possui um endereço de e-mail e sobrenome e gostaria de filtrar por esses dois atributos. 

Exemplo:
------------------------------------------------------------------------------------------

public Interface UserRepository extends Repository<User, Long>{
	List<User> findByEmailAddressAndLastName(String emailAddress, String lastName);
}
------------------------------------------------------------------------------------------

Query Override
Vamos imaginar que você precisa montar uma query um tanto avançada mas ficaria inviável utilizar o padrão QueryMethod. 
Como nossos repositórios são interfaces não temos implementação de código,
 é ai que precisa definir a consulta de forma manual através da anotação '@Query'.
 
Os dois métodos realizam a mesma instrução SQL, consultando os usuários pelo seu nome comparando com o operador 'LIKE' do sql. 

------------------------------------------------------------------------------------------

public Interface UserRepository extends Repository<User, Long>{

	// Query Method - Retorna a lista de usuarios contendo parte do nome
	List<User> findByNameContaining(String name);
	
	// Query Override - Retorna a lista de usuarios contendo parte do nome
	@Query("SELECT u FROM User u WHERE u.name LIKE %:name%")
	List<User> filtrarPorNome(@Param("name") String name);
	
	// Query Method - Retorna um usuario pelo campo nome
	User findByUserName(String name);
}

------------------------------------------------------------------------------------------
