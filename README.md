<h1> Etape 1 Microservice : Customer-Service</h1>

<p>
  Le microservice <strong>customer-service</strong> permet de gérer les clients dans une architecture microservices.
  Il expose des endpoints REST via <strong>Spring Data REST</strong>, utilise une base de données 
  <strong>H2</strong> et charge des clients par défaut au démarrage.
</p>

<hr/> 


<h2> 1. Package <code>entities</code></h2>

<p>Ce package contient les classes métier. L’entité principale est <strong>Customer</strong>.</p>

<h3>✔ Customer.java</h3>

<pre>
@Entity
@Data @AllArgsConstructor @NoArgsConstructor
public class Customer {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;
}
</pre>

<p>
   Représente un client dans la base de données.<br/>
   Lombok génère automatiquement getters, setters et constructeurs.
</p>

<hr/>

<h2> 2. Package <code>repositories</code></h2>

<p>
  Ce package contient l’interface JPA qui permet d’accéder aux données.
  Spring Data REST expose automatiquement les endpoints REST.
</p>


<p>Endpoints exposés automatiquement :</p>

<ul>
  <li><code>GET /api/customers</code></li>
  <li><code>GET /api/customers/{id}</code></li>
  <li><code>POST /api/customers</code></li>
  <li><code>DELETE /api/customers/{id}</code></li>
</ul>

<hr/>

<h2> 3. Package <code>config</code></h2>

<p>
  Par défaut, Spring Data REST masque les IDs dans le JSON. <br/>
  Ce package contient la configuration qui permet de <strong>rendre les IDs visibles</strong>.
</p>

<h3> RepositoryConfig.java</h3>



<p> Cela permet d'afficher les identifiants dans les réponses JSON :</p>

<pre>
{
  "id": 1,
  "name": "Mohamed",
  "email": "med@gmail.com"
}
</pre>

<hr/>

<h2> Test de l’API</h2>

<p>Une fois le microservice lancé :</p>

<ul>
  <li><strong>URL principale :</strong> <code>http://localhost:8081/api/customers</code></li>
  <li>Affiche la liste complète des clients avec leurs IDs visibles.</li>
</ul>

<hr/>

![Liste clients](images/1service.png)

![h2 database](images/2.png)


<h1> Etape 2 Microservice : Inventory-Service</h1>


![Liste clients](images/2service.png)

<p>H2 database products-db </p> 

![3](images/3.png)

<p>L'etat du service avec (health) </p>

![4](images/4.png)

<p>Tous les ends points que generent Actuator </p>

![5](images/5.png)


<h2> Test de l’API</h2>

<p>Une fois le microservice lancé :</p>

<ul>
  <li><strong>URL principale :</strong> <code>http://localhost:8082/api/products</code></li>
  <li>Affiche tous les produits disponibles.</li>
</ul>

<p>Exemple d'appel avec <strong>curl</strong> :</p>

<pre>
curl http://localhost:8082/api/products
</pre>

<hr/>

<h1> Etape 3 Microservice : Gateway-Service</h1>

<p>
  Le microservice <strong>gateway-service</strong> joue le rôle de point d’entrée unique dans l’architecture microservices.
  Il utilise <strong>Spring Cloud Gateway MVC</strong> pour router les requêtes et <strong>Eureka Discovery</strong> pour
  détecter automatiquement les microservices actifs.
</p>

<ul>
  <li>Centralisation des appels API</li>
  <li>Redirection vers les microservices internes</li>
  <li>Simplification de l’accès pour le frontend</li>
  <li>Possibilité d'ajouter sécurité, CORS, authentification</li>
</ul>

<hr/>

<h2>1. Configuration <code>application.yml</code></h2>

<p>La Gateway écoute sur le port <strong>8888</strong> et redirige les requêtes selon leur chemin.</p>

<pre>
server:
  port: 8888

spring:
  application:
    name: gateway-service

  cloud:
    gateway:
      mvc:
        routes:
          - id: customer-service
            uri: http://localhost:8081
            predicates:
              - Path=/api/customers/**

          - id: inventory-service
            uri: http://localhost:8082
            predicates:
              - Path=/api/products/**
</pre>

<p>
  Chaque route correspond à un microservice interne.  
  La Gateway fait le lien entre les URL externes et les APIs internes.
</p>

<hr/>

<h2>2. Fonctionnement de l’API Gateway</h2>

<p>
  Grâce à la Gateway, le frontend ou les clients externes n'ont plus besoin de connaître les ports internes.
  Ils accèdent à tous les services via un seul point : <strong>http://localhost:8888</strong>
</p>

<ul>
  <li><code>http://localhost:8888/api/products</code> → Inventory-Service (8082)</li>
  <li><code>http://localhost:8888/api/customers</code> → Customer-Service (8081)</li>
</ul>

<p>
  Cela rend l'architecture plus flexible, plus sécurisée et beaucoup plus simple à consommer.
</p>

<hr/>

<h2>3. Avantages de l'API Gateway</h2>

<ul>
  <li><strong>Point d’entrée unique</strong> pour tout le système</li>
  <li><strong>Découplage</strong> entre frontend et microservices</li>
  <li><strong>Routage intelligent</strong> (basé sur les paths)</li>
  <li><strong>Support d’Eureka</strong> pour détecter les services</li>
  <li><strong>Extensible</strong> (sécurité, rate limiting, monitoring…)</li>
</ul>

<hr/>

<h2>4. Test de la Gateway</h2>

<p>Une fois la Gateway lancée :</p>

<ul>
  <li><strong>Produits via Gateway :</strong> <code>http://localhost:8888/api/products</code></li>
  <li><strong>Clients via Gateway :</strong> <code>http://localhost:8888/api/customers</code></li>
</ul>

<p>La Gateway se charge automatiquement du routage vers les microservices internes.</p>

<hr/>

<h2>Aperçu des résultats via Gateway</h2>

<p><strong>1. Liste des produits via Gateway :</strong></p>
<img src="images/7.png" alt="Liste produits Gateway"/>

<p><strong>2. Liste des clients via Gateway :</strong></p>
<img src="images/6.png" alt="Liste clients Gateway"/>

<hr/>


<h1> Étape 4 — Discovery-Service (Eureka Server)</h1>

<p>
Le microservice <strong>discovery-service</strong> joue le rôle de 
<strong>registre de services</strong> (Service Registry).  
Il permet :
</p>

<ul>
  <li>La <strong>découverte dynamique</strong> des microservices</li>
  <li>L'<strong>enregistrement automatique</strong> de Customer-Service, Inventory-Service et Gateway</li>
  <li>Le <strong>load balancing</strong> avec Eureka + Gateway</li>
  <li>Un dashboard web pour monitorer les services</li>
</ul>

<hr/>

<h2> 1. Configuration Eureka</h2>

<h3>application.properties</h3>

<pre>
spring.application.name=discovery-service
server.port=8761

eureka.client.fetch-registry=false
eureka.client.register-with-eureka=false
</pre>

<hr/>

<h2> 2. Classe principale</h2>

<pre>
@SpringBootApplication
@EnableEurekaServer
public class DiscoveryServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(DiscoveryServiceApplication.class, args);
    }
}
</pre>

<hr/>

<h2> 3. Lancement d’Eureka</h2>

<p>Une fois démarré :</p>

<ul>
  <li>Dashboard disponible ici :  
      <strong>http://localhost:8761</strong>
  </li>
</ul>


<p><strong>1. Dashboard de Eureka avec les nos micro-services  :</strong></p>
<img src="images/8.png" alt="Liste produits Gateway"/>

<p><strong>2. Liste des clients avec routage dynamique  :</strong></p>
<img src="images/9.png" alt="Liste produits Gateway"/>

<p><strong>3. Liste des produits avec routage dynamique :</strong></p>
<img src="images/99.png" alt="Liste produits Gateway"/>


<p>
On constate que :</p>
<ul>
  <li><strong>CUSTOMER-SERVICE</strong> est UP</li>
  <li><strong>INVENTORY-SERVICE</strong> est UP</li>
  <li><strong>GATEWAY-SERVICE</strong> est UP</li>
</ul>

<hr/>

<h2> Architecture finale </h2>

<pre>
   Gateway-Service (8888)
          |
   -------------------------
   |                       |
Customer-Service      Inventory-Service
     8081                  8082
          \               /
            Eureka Server (8761)
</pre>

<hr/>

<h2>  Projet Microservices Fonctionnel </h2>
<p>
Tous les services communiquent correctement via Eureka et la Gateway.  
Les endpoints sont accessibles de manière centralisée sur :
</p>

<ul>
  <li><strong>http://localhost:8888/customer-service/api/customers</strong></li>
  <li><strong>http://localhost:8888/product-service/api/products</strong></li>
</ul>

<p><strong>Architecture totalement opérationnelle </strong></p>






