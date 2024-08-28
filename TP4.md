# TP: Utilisation de MongoDB pour la Gestion de Films

## Objectif
Dans ce TP, vous allez apprendre à utiliser MongoDB avec Node.js pour créer une API REST permettant de gérer une base de données de films. Vous allez implémenter les fonctionnalités CRUD (Create, Read, Update, Delete) pour manipuler les données des films.

## Prérequis
- Avoir Node.js (https://nodejs.org/en/download/package-manager/current) et npm (https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) installés sur votre machine.
- Avoir MongoDB installé et en cours d'exécution.
- Avoir Postman installé (https://www.postman.com/downloads/)

## Étape 1: Initialisation du Projet

### 1.1 Créez et initialisez un nouveau projet Node.js

Dans un terminal, exécutez les commandes suivantes :

```bash
mkdir film-api
cd film-api
npm init -y
```

### 1.2 Installez les dépendances nécessaires

Installez Express, Mongoose et Body-parser avec la commande suivante :

```
npm install express mongoose body-parser
```

### 1.3 Structurez le projet

Créez la structure suivante pour votre projet :

```
film-api/
├── models/
│   └── film.js
├── routes/
│   └── films.js
├── app.js
└── package.json
```

## Étape 2: Définir le Schéma MongoDB

Dans le fichier models/film.js, définissez le schéma MongoDB pour les films, y compris les sous-documents pour les acteurs, les réalisateurs et les évaluations.

```
const mongoose = require('mongoose');

const ratingSchema = new mongoose.Schema({
    score: { type: Number, required: true },
    reviewer: { type: String }
});

const filmSchema = new mongoose.Schema({
    title: { type: String, required: true, unique: true },
    releaseYear: { type: Number },
    genre: { type: String },
    actors: [{ name: String }],
    directors: [{ name: String }],
    ratings: [ratingSchema]
});

const Film = mongoose.model('Film', filmSchema);

module.exports = Film;
```

## Étape 3: Créer les Routes

Dans le fichier routes/films.js, définissez les routes (endpoints) pour interagir avec la base de données de films.

### 3.1 Ajouter un nouveau film

Créez une route POST pour ajouter un nouveau film :

```
const express = require('express');
const Film = require('../models/film');
const router = express.Router();

// 1. Ajouter un nouveau film
router.post('/films', async (req, res) => {
    try {
        const film = new Film(req.body);
        await film.save();
        res.status(201).send(film);
    } catch (error) {
        res.status(400).send(error);
    }
});

module.exports = router;
```

## Étape 4: Construire les Endpoints RESTful (à faire)

1. POST `/api/films` - Ajouter un nouveau film
2. GET `/api/films` - Récupérer tous les films -> `find()`
3. GET `/api/films/:title` - Récupérer un film par son titre -> `findOne()`
4. PATCH `/api/films/:title` - Mettre à jour un film existant par son titre -> `findOneAndUpdate()`
5. DELETE `/api/films/:title` - Supprimer un film par son titre -> `findOneAndDelete`
6. GET `/api/films/director/:name` - Récupérer les films par le nom du réalisateur -> `find()`
7. GET `/api/films/actor/:name` - Récupérer les films par le nom de l'acteur -> `find()`
8. GET `/api/films/ratings/above6` - Récupérer les films avec une note moyenne supérieure à 6 -> `aggregate()`
9. GET `/api/films/year/:year` - Récupérer les films sortis dans une année spécifique -> `find()`
10. GET `/api/films/genre/:genre` - Récupérer les films par genre -> `find()`

Vous pouvez étendre et implémenter ces endpoints dans le fichier routes/films.js en suivant la même structure que celle présentée pour l'ajout d'un nouveau film.

## Étape 5: Lancer l'application

Dans le fichier app.js, configurez et lancez votre application Express.

```
const express = require('express');
const bodyParser = require('body-parser');
const mongoose = require('mongoose');
const filmRoutes = require('./routes/films');

const app = express();

mongoose.connect('mongodb://localhost:27017/filmsdb', { useNewUrlParser: true, useUnifiedTopology: true });

app.use(bodyParser.json());
app.use('/api', filmRoutes);

app.listen(3000, () => {
    console.log('Server is running on port 3000');
});
```

Démarrez votre serveur Node.js avec la commande suivante :

```
node app.js
```
Votre API est maintenant prête à être utilisée ! Testez les différents endpoints avec un outil comme Postman.

# Annexes 

### Données JSON d'un film

```
{
    "title": "Shutter Island",
    "releaseYear": 2010,
    "genre": "Science Fiction",
    "actors": [
        { "name": "Leonardo DiCaprio" },
        { "name": "Joseph Gordon-Levitt" },
        { "name": "Elliot Page" }
    ],
    "directors": [
        { "name": "Christopher Nolan" }
    ],
    "ratings": [
        { "score": 8.8, "reviewer": "IMDb" },
        { "score": 9.0, "reviewer": "Rotten Tomatoes" },
        { "score": 8.5, "reviewer": "Metacritic" }
    ]
}
```

