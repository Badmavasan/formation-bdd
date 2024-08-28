# TP 5 : Node JS avec SQL

## Étape 1 : Initialiser le projet Node.js

Commencez par initialiser un nouveau projet Node.js et installez les dépendances nécessaires.

```
mkdir movie-api
cd movie-api
npm init -y
npm install express pg sequelize sequelize-cli
```

## Étape 2 : Configurer Sequelize et PostgreSQL

Nous allons configurer Sequelize pour se connecter à PostgreSQL.
  1. Initialisez Sequelize CLI
     ```
     npx sequelize-cli init
     ```

  2. Modifier le fichier config/config.json pour la configuration de la base de données.
     ```
      {
        "development": {
          "username": "postgres",
          "password": "your_password",
          "database": "movie_db",
          "host": "127.0.0.1",
          "dialect": "postgres"
        }
      }
      ```
  3. Créez la base de données
     ```
     npx sequelize-cli db:create
     ```

## Étape 3 : Créer les modèles

Nous allons maintenant créer les modèles Sequelize pour Film, Review, et User.

  1. Film modèle :
  ```
  npx sequelize-cli model:generate --name Film --attributes title:string,description:text,releaseDate:date
  ```

  2. Review modèle :
  ```
  npx sequelize-cli model:generate --name Review --attributes content:text,rating:integer,filmId:integer,userId:integer
  ```

  3. User modèle :
  ```
  npx sequelize-cli model:generate --name User --attributes name:string,email:string
  ```

## Étape 4 : Définir les associations

Modifiez les fichiers de modèles pour définir les relations entre les entités.

  1. Film associations (One-to-Many avec Review) dans la méthode : ` static associate(models)` :
     ```
      Film.hasMany(models.Review, { foreignKey: 'filmId', as: 'reviews' });
     ```
  2. Review associations (Many-to-One avec Film et User) dans la méthode : ` static associate(models)` :
     ```
      Review.belongsTo(models.Film, { foreignKey: 'filmId', as: 'film' });
      Review.belongsTo(models.User, { foreignKey: 'userId', as: 'user' });
     ```
  3. User associations (One-to-Many avec Review) dans la méthode : ` static associate(models)` :
     ```
      User.hasMany(models.Review, { foreignKey: 'userId', as: 'reviews' });
     ```

## Étape 5 : Créer les migrations et synchroniser la base de données

Appliquez les migrations pour créer les tables dans la base de données.

```
npx sequelize-cli db:migrate
```

## Étape 6 : Créer l'API avec Express

Créez les routes pour gérer les films, les utilisateurs et les critiques.

  1. Créez un fichier `routes/routes.js` pour configurer Express.
  ```
  const express = require('express');
  const { Film, Review, User } = require('./models');
  
  const router = express.Router();
  
  // Routes pour les Films
  router.get('/films', async (req, res) => {
    try {
      const films = await Film.findAll({ include: 'reviews' });
      res.json(films);
    } catch (error) {
      res.status(500).json({ error: error.message });
    }
  });

  module.exports = router;
  ```

2. Configuration du fichier app.js
```
// app.js
const express = require('express');
const bodyParser = require('body-parser');
const routes = require('./routes');

const app = express();

app.use(bodyParser.json());
app.use('/api', routes); // Utilisation des routes avec un préfixe '/api'

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`);
});
```

3. Strucutre du projet
```
movie-api/
│
├── config/
│   └── config.json
├── migrations/
├── models/
│   ├── film.js
│   ├── review.js
│   ├── user.js
│   └── index.js
├── seeders/
├── routes/
|   └── routes.js  # Contient toutes les routes
└── app.js         # Point d'entrée principal de l'application
```

## Étape 7 : requêtes à faire : 

| **Endpoint**                                          | **Méthode HTTP** | **URL**                            | **Commande Sequelize**            | **Description**                                                                 |
|-------------------------------------------------------|-----------------|------------------------------------|-----------------------------------|---------------------------------------------------------------------------------|
| **Créer un film**                                     | POST            | `/api/films`                      | `Film.create(data)`               | Crée un nouveau film dans la base de données.                                   |
| **Récupérer tous les films**                          | GET             | `/api/films`                      | `Film.findAll()`                   | Récupère tous les films de la base de données.                                  |
| **Récupérer un film par ID**                          | GET             | `/api/films/:id`                  | `Film.findByPk(id)`                | Récupère un film spécifique par son ID.                                         |
| **Mettre à jour un film**                             | PUT             | `/api/films/:id`                  | `Film.update(data, { where: { id } })` | Met à jour les informations d'un film existant par son ID.                    |
| **Supprimer un film**                                 | DELETE          | `/api/films/:id`                  | `Film.destroy({ where: { id } })`  | Supprime un film spécifique de la base de données.                              |
| **Créer une critique (review)**                       | POST            | `/api/reviews`                    | `Review.create(data)`              | Crée une nouvelle critique pour un film.                                        |
| **Récupérer toutes les critiques pour un film**       | GET             | `/api/films/:id/reviews`          | `Review.findAll({ where: { filmId: id } })` | Récupère toutes les critiques associées à un film spécifique.        |
| **Récupérer toutes les critiques d'un utilisateur**   | GET             | `/api/users/:id/reviews`          | `Review.findAll({ where: { userId: id } })` | Récupère toutes les critiques faites par un utilisateur spécifique.   |
| **Supprimer une critique**                            | DELETE          | `/api/reviews/:id`                | `Review.destroy({ where: { id } })` | Supprime une critique spécifique de la base de données.                       |
| **Créer un utilisateur**                              | POST            | `/api/users`                      | `User.create(data)`                | Crée un nouvel utilisateur dans la base de données.                             |
| **Récupérer tous les utilisateurs**                   | GET             | `/api/users`                      | `User.findAll()`                   | Récupère tous les utilisateurs de la base de données.                           |
| **Récupérer un utilisateur par ID**                   | GET             | `/api/users/:id`                  | `User.findByPk(id)`                | Récupère un utilisateur spécifique par son ID.                                  |
| **Mettre à jour un utilisateur**                      | PUT             | `/api/users/:id`                  | `User.update(data, { where: { id } })` | Met à jour les informations d'un utilisateur existant par son ID.            |
| **Supprimer un utilisateur**                          | DELETE          | `/api/users/:id`                  | `User.destroy({ where: { id } })`  | Supprime un utilisateur spécifique de la base de données.                       |

