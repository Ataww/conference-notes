# Agile Grenoble : refactoring
_Clément Bouillier_ - **Freelance** - _@clem_bouillier_
hackyourjob

## Legacy code
empilement de couches hétérogènes, chaque dev ajoute à sa sauce.
_Michael C. Feathers_ - **Working effectively with Legacy Code**

_Assertion:_ pas de base de test, pas d’audit de code, peu ou pas de documentation, pas de style standard ou guidelines d’écriture

## Rapport avec l’agilité
Rendre le code plus flexible, adaptable.  Eviter le monolithique (à enrichir)

## Refactoring
### Les codes smells
Trouver des erreurs triviales, des incohérences (cf **Sonar**)
* _primitive obsession_: utilisation exclusive des type primitifs, des classes concrètes au lieu d’interfaces
* méthodes longues
* Console (portage de la UI)
* complexité cyclique/cognitive
* _god class_ (classe qui fait tout)
* pas de nommages
-> test de caractérisations (_black box testing / golden master_)
-> je me branche dessus et je regarde ce qui sort)
Je cherche pas à comprendre le code dans son ensemble, je corrige petit à petit des bouts de code et la compréhension vient au fil des corrections.

* SRP
	* Single Responsibility Principle, **S de SOLID** (look it up)
* Nommage correct
	* Moins de commentaire nécessaire
* inversion de dépendance
	* Initialize les dépendances avant la classe. La classe est créée avec toutes les dépendances nécessaires
	* Limite le besoin des setters à la mutabilité d’une classe
	* principe **DIP** de **SOLID** (_Dependency Inversion Principle_)
	* Exemple du trivial pursuit qui init les joueurs, j’ai besoin du jeu ou des joueurs en premier?
#### SOLID
**S**ingle Responsibility Principle
**O**pen Closed Principle
**L**iskov Substitution
**I**nterface Segregation
**D**ependency Inversion Principle

* Extraire le code d’infrastructure (logging, web service, database, etc…)
	* architecture hexagonale, archi en oignon
	* pattern adapter 

refactoring avant modif  (préconisé) vs refactoring après modif

#gbs #dev #conference #agile-grenoble