# Database

- [Introduction](#introduction)
  - [Configuration](#configuration)
  - [Connexion à plusieur Base de donnée](#connexion-multiple)
- [requête SQL Brute](#introduction)
  - [Execution de requête Select](#execution-select)
  - [Execution de requête Insert](#execution-insert)
  - [Execution de requête Update](#execution-update)
  - [Execution de requête Delete](#execution-delete)

## Introduction

Bow rend l'interaction avec les bases de données extrêmement simple sur une grande variété de backends de bases de données en utilisant le `SQL brut`, `le générateur de requêtes courant` et l'ORM Barry. 

Actuellement, Bow prend en charge deux bases de données:

- MySQL
- SQLite

### Configuration

La configuration de la base donnée de votre application se localise dans le fihcier `config/db.php`. Dans ce fichier, vous pouvez definir tous les connections de votre base de donnée, as well as specify which connection should be used by default. Un exemple en plus definir pour tous les supports de base de donnée est defini dans le fichier.

### SQLite Configuration

Après avoir créé une nouvelle base de données SQLite à l'aide d'une commande telle que `touch storage/database.sqlite`, vous pouvez facilement configurer vos variables d'environnement (dans le fichier `.env.json`) pour qu'elles pointent vers cette base de données nouvellement créée à l'aide du chemin absolu de la base de données:

```text
"DB_DEFAULT": "seconds",
...
"SQLITE_DATABASE": "/absolute/path/to/database.sqlite",
```

### Connexion à plusieur Base de donnée

Lorsque vous utilisez plusieurs connexions, vous pouvez accéder à chaque connexion via la méthode de `connexion` sur la classe de la `Database`. Le nom transmis à la méthode de connexion doit correspondre à l'une des points de connexions répertoriées dans votre fichier de configuration `config/db.php`:

```php
use Bow\Database\Database;
$users = Database::connection('seconds')->select(...);
```

Ou via le helper `db`:

```php
$users = db('seconds')->select(...);
```

Une fois la configuration changé elle est directement appliquer sur la connexion des Models. Cliquer ici pour plus d'information sur les models.

## requête SQL Brute

Les requête brute ici sont les requêtes SQL ecrite litteralement sans passer par un query builder.
Dans cette section nous allons utiliser une table nommer `pets` pour effectuer nos requête avec.

Voici la description de la table `pets`:

```sql
CREATE TABLE `pets` (id int primary key, name varchar(200), color varchar(50));
```

Alors notre table à comme colonne:

| Nom de la colonne | Description |
| ----------------- | ----------- |
| `id` | Ici la clé primaire |
| `name` | Le nom du pet |
| `color` | La couleur du pet |

> Pour info `pet` c'est un peu le synonime de animal domestique

### Execution de requête Select

Pour executer une requête brute de type `SELECT` nous devrez utiliser la methode `Database::select` ou le helper `select`. On considère notre table `pets` et que nous sommes bien connectés à la base de donnée.

Execution d'un requête pour optenir tous les informations de la table `pets`:

```php
use Bow\Database\Database;
$pets = Database::select('select * from pets');
```

Notez que la valeur retournée par la methode `select` est un `array` ou `null` s'il y a aucune informations.
Dans le cas ou c4est un `array` le content est de type `stClass` (plus d'information sur [stClass](http://php.net/manual/fr/language.types.object.php)).

### Execution de requête Insert

Pour executer une requête brute de type `INSERT` nous devrez utiliser la methode `Database::insert` ou le helper `insert`. On considère tout notre table `pets` et que nous sommes bien connectés à la base de donnée.

Execution d'un requête pour inserer une information dans table `pets`:

```php
use Bow\Database\Database;

$pet = [
  'id' => 1,
  'name' => 'Medor',
  'color' => 'Green'
];

$insertion_number = Database::insert('insert into pets (id, name, color) values (:id, :name, :color);', $pet);
```

Via helper `insert`:

```php
$pet = [
  'id' => 2,
  'name' => 'Mashmalo',
  'color' => 'White'
];

$insertion_number = insert('insert into pets (id, name, color) values (:id, :name, :color);', $pet);
```

Notez que la valeur retournée par la methode `insert` est un `int` ou `number` qui est le nombre d'insertion.

#### Insertion multiple

Vous avez aussi la possibilité d'inserer plusieurs enregistrements en même temps.

```php
use Bow\Database\Database;
// Liste de pets
$pets = [
  [
    'id' => 1,
    'name' => 'Medor',
    'color' => 'Black'
  ],
  [
    'id' => 2,
    'name' => 'Molou',
    'color' => 'Gay'
  ]
];

$insertion_number = Database::insert('insert into pets (id, name, color) values (:id, :name, :color);', $pets);
```

Via helper `insert`:

```php
$uppdated_number = insert('insert into pets (id, name, color) values (:id, :name, :color);', $pets);
```

### Execution de requête Update

Pour executer une requête brute de type `UPDATE` nous devrez utiliser la methode `Database::update` ou le helper `update`. On considère tout notre table `pets` et que nous sommes bien connectés à la base de donnée.

Execution d'un requête de mettre à jour d'information dans table `pets`:

```php
use Bow\Database\Database;

$pet = [
  'id' => 1,
  'name' => 'Medora',
  'color' => 'Purple'
];

$uppdated_number = Database::update('update pets set id=:id, name=:name, color=:color where id = :id', $pet);
```

Via helper `update`:

```php
$pet = [
  'id' => 2,
  'name' => 'Spark',
  'color' => 'Yello'
];

$uppdated_number = update('update pets set id=:id, name=:name, color=:color where id = :id', $pet);
```

### Execution de requête Delete

Pour executer une requête brute de type `DELETE` nous devrez utiliser la methode `Database::delete` ou le helper `delete`. On considère tout notre table `pets` et que nous sommes bien connectés à la base de donnée.

Execution d'un requête pour inserer une information dans table `pets`:

```php
use Bow\Database\Database;
$deleted_number = Database::delete('delete from pets where id = :id', ['id' => 1]);
```

Via le helper `delete`:

```php
$deleted_number = delete('delete from pets where id = :id', ['id' => 2]);
```