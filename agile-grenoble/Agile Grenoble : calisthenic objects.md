# Agile Grenoble : calisthenic objects
Par Nastasia Saby - **Zenika** 
_@saby_nastasia_

## Origine
Exercice de prog par **Jeff Bay** pour mieux écrire en POO

## 9 règles
### 1 niveau d’indentation par méthode
Lisibilité
### Pas de bloc else
**solution:** early return / polymorphisme / pattern strategy
### Wrap primitives and string
Donner du sens à nos données (monnaie/heure par ex)
### First class collection
class Cat & class CatList pour gérer la liste des chats
### One dot per line
« On ne parle qu’à ses amis immédiats »
### Don’t abbreviate
* reuse -> code dupliqué
* non trop long -> trop de responsabilité?
### Keep all entities small
Pas plus de 50 lignes / classe et 10 fichiers / package
### Pas plus de 2 instances de variable
à combiner avec règle 3
Client -> {Name -> {firstName -> String, lastName -> String}}
### No getters/setters
Méthodes ne définissent que des actions
**par exemple** addScore(delta) au lieu de setScore(getScore() + delta)

## Kata : FizzBuzz en calisthénique
### Faire en TDD
100% de coverage
### Pair programming 
A trois :)
## Retro


#dev/poo #conference #gbs #agile-grenoble