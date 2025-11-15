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







