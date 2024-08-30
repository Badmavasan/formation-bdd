# TP 7 - Neo4J 

1. Connecter sur : https://neo4j.com/cloud/platform/aura-graph-database/?ref=neo4j-home-hero (Il faut créer un compte)
2. Choissisez la version gratuite

`Cypher` est un langage d’interrogation de graphes qui est utilisé pour interroger la base de données Neo4j. Tout comme vous utilisez SQL pour interroger une base de données MySQL, vous utilisez Cypher pour interroger la base de données Neo4j.

## Les noeuds :

![image](https://github.com/user-attachments/assets/9897e3a2-58b9-4a07-b541-5ab2471b6a0f)

## Les relaitons

![image](https://github.com/user-attachments/assets/f1562f3a-207a-4dfc-b8a2-d032fc95839f)

## Propriétés des noeuds

![image](https://github.com/user-attachments/assets/9425db3f-4f35-4999-813c-9d93c2c5c43b)

## Propriétés des relations 

![image](https://github.com/user-attachments/assets/4d341bc6-a2b6-4e0a-81ec-426b51e163fe)

## Les Labels

![image](https://github.com/user-attachments/assets/3578b522-f486-429b-88d9-9a0634a18ced)

## Les Chemins (Path)

Un chemin est un ensemble de nœuds et de relations connectés, qui peut être un modèle. Par exemple :
```
(a)->(b)->(c)
(a)->(b)<-(c)
```

Exemple : Afficher tous les acteurs et réalisateurs de tous les films :

```
MATCH (a)-[ :ACTED_IN]->(m)<-[ :DIRECTED]-(d)
RETURN a.name, m.title, d.name
```

Il est également possible de diviser la requête en plusieurs chemins, comme suit :

```
MATCH (a)-[ :ACTED_IN]->(m),
(m)<-[ :DIRECTED]-(d)
RETURN a.name, m.title, d.name
```

ou 

```
MATCH (a)-[ :ACTED_IN]->(m),
(d)-[ :DIRECTED]->(m)
RETURN a.name, m.title, d.name
```

Les résultats des requêtes précédentes sont des tableaux. Il est possible de retourner le chemin entier, avec toutes les propriétés de tous les nœuds et relations concernées.

```
MATCH p=(a)-[:ACTED_IN]->(m)<-[ :DIRECTED]-(d)
RETURN p
```

Si cela représente trop d’informations, vous pouvez simplement sélectionner les noeuds du chemin :

```
MATCH p=(a)-[ :DIRECTED_ID]->(m)<-[ :DIRECTED]-(d)
RETURN nodes(p)
```

... ou seulement des relations :

```
MATCH p=(a)-[ :ACTED_IN]->(m)<-[ :DIRECTED]-(d)
RETURN rels(p)
```

## `Create` : Création des données 

La clause Cypher CREATE est utilisée pour créer des données dans votre graphique. Si vous n'êtes pas familier avec la syntaxe Cypher, vous pouvez essayer le guide Query fundamentals ou consulter le
Manuel Cypher.

Vous allez créer un ensemble de données avec des nœuds Person et Movie et différents types de relations pour les relier.

## Création des contraintes 

Dans Neo4j, les contraintes sont utilisées pour appliquer des règles sur les données de votre graphe. Cela permet d'assurer l'intégrité des données et d'optimiser les requêtes. La commande CREATE CONSTRAINT de Cypher vous permet de définir différents types de contraintes sur les nœuds ou les relations.

### Types de contraintes :

  - Contrainte d'unicité : Garantit qu'une propriété ou une combinaison de propriétés est unique pour tous les nœuds ou relations avec un label spécifique.
  - Contrainte de clé de nœud : Garantit qu'une combinaison de propriétés est unique et présente sur tous les nœuds ayant une étiquette spécifique.
  - Contrainte d'existence : Garantit qu'une propriété existe sur tous les nœuds ou relations ayant une étiquette ou un type spécifique.

Syntax pour la création d'une contrainte d'unicité :

```
CREATE CONSTRAINT constraint_name FOR (n:Label) REQUIRE n.property IS UNIQUE
```

Syntax pour la création d'une contrainte de clé noeud : 

```
CREATE CONSTRAINT constraint_name FOR (n:Label) REQUIRE (n.property1, n.property2, ...) IS NODE KEY
```

Syntax pour la création d'une contrainte d'existence : 

```
CREATE CONSTRAINT constraint_name FOR (n:Label) REQUIRE n.property IS NOT NULL
```

### A faire 

Création d'une contraite pour indiquer que le titre du film est **Unique**

```
CREATE CONSTRAINT movie_title IF NOT EXISTS FOR (m:Movie) REQUIRE m.title IS UNIQUE;
```

Création d'une contrainte pour indiquer sur le nom d'une personne est **Unique**

```
CREATE CONSTRAINT person_name IF NOT EXISTS FOR (p:Person) REQUIRE p.name IS UNIQUE;
```


## Création des noeuds 

Syntax de créaiton d'un noeud :
```
CREATE (n:Label {property1: value1, property2: value2, ...})
```

Dans Neo4j, la clause `MERGE` est utilisée pour s'assurer qu'un modèle spécifique (comme un nœud ou une relation) existe dans le graphe. S'il n'existe pas, `MERGE` le créera. S'il existe déjà, il n'en créera pas un nouveau mais renverra le motif existant. Essentiellement, MERGE est une combinaison de MATCH (pour trouver un motif existant) et de CREATE (pour créer le motif s'il n'existe pas).

- `CREATE`: Crée toujours de nouveaux nœuds ou de nouvelles relations, qu'il existe ou non un modèle similaire.

  ```
  CREATE (n:Person {name: "Alice"})
  ```

  Un nouveau nœud est ainsi créé, même s'il existe déjà un nœud Personne portant le nom « Alice ».

- `MERGE`: Ne crée un nouveau nœud ou une nouvelle relation que si le modèle n'existe pas encore. Si un nœud ayant les propriétés spécifiées existe déjà, MERGE ne créera pas de doublon.
  ```
  MERGE (n:Person {name: "Alice"})
  ```

  Cette opération permet de trouver un nœud existant avec l'étiquette Personne et le nom de propriété : « Alice » ou d'en créer un s'il n'existe pas.

### A faire

Création d'un neoud film Matrix dans la base de données en utilisant MERGE: 

```
MERGE (TheMatrix:Movie {title:'The Matrix'}) ON CREATE SET TheMatrix.released=1999, TheMatrix.tagline='Welcome to the Real World'
```

Création des personnes :

```
MERGE (Keanu:Person {name:'Keanu Reeves'}) ON CREATE SET Keanu.born=1964
MERGE (Carrie:Person {name:'Carrie-Anne Moss'}) ON CREATE SET Carrie.born=1967
MERGE (Laurence:Person {name:'Laurence Fishburne'}) ON CREATE SET Laurence.born=1961
MERGE (Hugo:Person {name:'Hugo Weaving'}) ON CREATE SET Hugo.born=1960
MERGE (LillyW:Person {name:'Lilly Wachowski'}) ON CREATE SET LillyW.born=1967
MERGE (LanaW:Person {name:'Lana Wachowski'}) ON CREATE SET LanaW.born=1965
MERGE (JoelS:Person {name:'Joel Silver'}) ON CREATE SET JoelS.born=1952
```

## Création des relations 

Pour créer des relations entre des nœuds dans Neo4j avec Cypher, vous utilisez la commande `CREATE`. Voici la syntaxe générale pour créer une relation :

```
CREATE (n1:Label1 {property1: value1})-[r:RELATIONSHIP_TYPE {property2: value2}]->(n2:Label2 {property3: value3})
```

- n1 et n2 : Ce sont les variables représentant les nœuds de départ et d'arrivée, respectivement.
- Label1 et Label2 : Ce sont les étiquettes (labels) des nœuds.
- property1, property2, property3 : Ce sont des propriétés des nœuds et de la relation.
- RELATIONSHIP_TYPE : C'est le type de la relation.
- r : C'est la variable représentant la relation.

### Exemple 

Supposons que vous vouliez créer une relation KNOWS entre deux nœuds Person, Alice et Bob :

```
CREATE (a:Person {name: "Alice"})-[r:KNOWS]->(b:Person {name: "Bob"})
```

Dans cet exemple :
- Un nœud Person avec le nom "Alice" est créé.
- Un autre nœud Person avec le nom "Bob" est créé.
- Une relation KNOWS est créée entre Alice et Bob.

Vous pouvez également ajouter des propriétés à la relation. Par exemple, pour indiquer depuis combien de temps Alice connaît Bob :

```
CREATE (a:Person {name: "Alice"})-[r:KNOWS {since: 2020}]->(b:Person {name: "Bob"})
```

Ici, la relation KNOWS a une propriété since avec la valeur 2020.

Si les nœuds existent déjà, vous pouvez les "matcher" d'abord et ensuite créer la relation :

```
MATCH (a:Person {name: "Alice"}), (b:Person {name: "Bob"})
CREATE (a)-[r:KNOWS]->(b)
```

- Le `MATCH` cherche les nœuds existants avec les propriétés spécifiées.
- Le `CREATE` ajoute la relation KNOWS entre Alice et Bob.

### A faire 

Création des liens : 

```
MERGE (Keanu)-[:ACTED_IN {roles:['Neo']}]->(TheMatrix)
MERGE (Carrie)-[:ACTED_IN {roles:['Trinity']}]->(TheMatrix)
MERGE (Laurence)-[:ACTED_IN {roles:['Morpheus']}]->(TheMatrix)
MERGE (Hugo)-[:ACTED_IN {roles:['Agent Smith']}]->(TheMatrix)
MERGE (LillyW)-[:DIRECTED]->(TheMatrix)
MERGE (LanaW)-[:DIRECTED]->(TheMatrix)
MERGE (JoelS)-[:PRODUCED]->(TheMatrix)
```

## Visualisation 

### Voir Tous les Nœuds

Pour récupérer tous les nœuds de la base de données :

```
MATCH (n) RETURN n
```

- MATCH (n) : Ce modèle correspond à tous les nœuds de la base de données.
- RETURN n : Cela retourne tous les nœuds trouvés.

### Voir Toutes les Relations

Pour récupérer toutes les relations de la base de données :

```
MATCH ()-[r]->() RETURN r
```

- MATCH ()-[r]->() : Ce modèle correspond à toutes les relations entre les nœuds. Les parenthèses vides () correspondent à n'importe quel nœud, et [r] correspond à toutes les relations.
- RETURN r : Cela retourne toutes les relations trouvées.

### Voir Tous les Nœuds avec leurs Relations

Pour voir tous les nœuds avec leurs relations dans une même requête :

```
MATCH (n)-[r]->(m) RETURN n, r, m
```

- MATCH (n)-[r]->(m) : Ce modèle correspond à tous les nœuds n qui ont une relation r vers un autre nœud m.
- RETURN n, r, m : Cela retourne tous les nœuds et les relations entre eux.

### Voir Tous les Nœuds et Relations Sans Exigence de Relation

Pour voir tous les nœuds, qu'ils aient ou non des relations, ainsi que toutes les relations existantes :

```
MATCH (n)
OPTIONAL MATCH (n)-[r]->(m)
RETURN n, r, m
```

- OPTIONAL MATCH : Cela permet de retourner aussi les nœuds qui n'ont pas de relations.

### Affichage en Vue Graphique (Dans l'Interface Neo4j)

Si vous utilisez Neo4j Browser, vous pouvez visualiser les résultats sous forme de graphes en ajoutant LIMIT pour éviter une surcharge :

```
MATCH (n)-[r]->(m) RETURN n, r, m LIMIT 100
```

Cela limite l'affichage à 100 résultats pour une vue graphique plus claire.

## Sélection avec des critères 

Trouver et retourner tous les chemins dans un graphe Neo4j qui impliquent l'acteur « Tom Hanks » jouant dans des films et les réalisateurs qui ont réalisé ces films

```
MATCH (person:Person {name: 'Tom Hanks'})
MATCH path = (person)-[:ACTED_IN]->(m)<-[:DIRECTED]-(d)
RETURN path;
```

Pas à Pas 

1. Premier clause `MATCH` 
   ```
   MATCH (person:Person {name: 'Tom Hanks'})
   ```
   Cette partie de la requête permet de trouver un nœud intitulé Person dont la propriété name est égale à « Tom Hanks ». Le résultat est stocké dans la variable person.
2. Seconde clause `MATCH`
   ```
    MATCH path = (person)-[:ACTED_IN]->(m)<-[:DIRECTED]-(d)
   ```
   - Cette partie de la requête recherche un modèle (un chemin) à partir du nœud personne (qui est Tom Hanks).
   - Elle recherche tous les films (m) dans lesquels Tom Hanks a joué, représentés par la relation [:ACTED_IN].
   - Il recherche ensuite les réalisateurs (d) qui ont réalisé ces films, représentés par la relation [:DIRECTED].
   - L'ensemble du modèle (ou chemin) qui comprend la personne, le film m et le réalisateur d est stocké dans la variable path.
3. `RETURN` Clause
    ```
    RETURN path;
    ```
    - Cette opération renvoie le(s) chemin(s) entier(s) trouvé(s) à l'étape précédente.
    - Chaque chemin comprend le nœud Personne (Tom Hanks)

La requête pour trouver tous les personnages du film The Matrix est :

```
MATCH (movie :Mivie)<-[role :ACTED_IN]-(actor :Person)
WHERE movie.title="The Matrix"
RETURN role.roles, actor.name
```

Une autre façon est également possible pour filtrer les données (au lieu du traditionnel `WHERE`), en remplaçant la requête précédente par :

```
MATCH (movie :Movie (title :"The Matrix"))<-[role :ACTED_ID]-(actor :Persone)
RETURN role.roles, actor.name
```

### A faire 

- Donnez, pour chaque film, sa date de sortie
- Quels réalisateurs ont joué dans leurs propres films ?
- Donnez les films dans lesquels Keanu Reeves a joué le rôle de Neo
- Quels acteurs ont déjà joué dans le même film que Tom Hanks, et dans quel film ?
- Quels acteurs ont joué dans le même film avec Tom Hanks, et avec Keanu Reeves ?
- Qui sont les personnages du film "The Matrix" ?
- Ajouter Kevin Bacon comme acteur dans le film "Mystic River" avec le rôle "Sean".

## Indexes

Pour améliorer les performances des requêtes, vous pouvez ajouter des index à vos données. L'ensemble de données que vous créez dans ce guide est très petit, mais c'est une bonne pratique d'utiliser des index car ils peuvent améliorer considérablement les performances des requêtes lorsque les ensembles de données sont plus importants.

### A faire

Exécutez la commande suivante pour voir les index existants dans la base de données :

```
SHOW INDEXES
```

Comme vous pouvez le constater, certains index sont déjà présents, ce qui est le résultat des deux premières lignes de la clause MERGE de l'étape précédente :
(à ne pas réexcuter , vous l'avez déjà fait avant)

```
CREATE CONSTRAINT movie_title IF NOT EXISTS FOR (m:Movie) REQUIRE m.title IS UNIQUE;
CREATE CONSTRAINT person_name IF NOT EXISTS FOR (p:Person) REQUIRE p.name IS UNIQUE;
```

Vous pouvez ajouter des index sur n'importe laquelle de vos propriétés et si vous savez à l'avance quelles sont celles que vous utiliserez le plus, il est logique d'ajouter un index sur celles-ci. Dans l'ensemble de données Films, vous pouvez être intéressé à la fois par l'année de naissance des personnes et par l'année de sortie des films. Exécutez la procédure suivante pour ajouter des index sur ces propriétés :

```
CREATE INDEX person_born IF NOT EXISTS FOR (p:Person) ON (p.born);
CREATE INDEX movie_released IF NOT EXISTS FOR (m:Movie) ON (m.released)
```

## Six degrees of Kevin Bacon

### A faire 

Vous avez peut-être entendu parler de l'idée selon laquelle deux personnes sur Terre sont séparées par six connexions ou moins. À Hollywood, on dit que c'est le cas pour Kevin Bacon et n'importe quel autre acteur.
La requête suivante vous permet de voir tous les films et toutes les personnes qui se trouvent jusqu'à six sauts de Kevin Bacon :

```
MATCH (:Person {name:"Kevin Bacon"})-[*1..6]-(n)
RETURN DISTINCT n
```

Mais est-il vrai que tous les acteurs sont réellement liés à Kevin Bacon ?
Cypher dispose d'un algorithme intégré pour cela, le « chemin le plus court », qui se présente comme suit :

```
MATCH path=shortestPath(
  (:Person {name:"Kevin Bacon"})-[*]-(:Person {name:"Meg Ryan"})
)
RETURN path, length(path) / 2 as distance
```

Vous pouvez essayer de remplacer Meg Ryan par n'importe quel autre acteur de l'ensemble de données et voir si vous pouvez en trouver un à plus de six sauts.

## Suppression des données 

La commande de suppression de tous les données : 

```
MATCH (n:Person|Movie)
DETACH DELETE n
```

