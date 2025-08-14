# Calculatrice carbone

## Créer une infrastructure

Sur votre espace utilisateur, rendez-vous sur *Mes infrastructures*, puis choissisez *Nouvelle Infrastructure*. Configurez ensuite votre infrastructre en choisissant un nom, un cloud provider parmis ceux compatible avec IroCO2 ainsi qu’une région par défault sur laquelle vous souhaitez configurer vos composants.

![New infra page](./images/create_infrastructure.png)

## Ajouter des composants 

Afin de représenter les ressources cloud qui sont ou serons présents sur votre infrastructure,  il faut créer des composants qui reprènne les éléments de configuration d’une ressource (vCPUs, mémoire, stockage, etc...), ainsi que des éléments de configuration sur son usage (temps d’utilisation, réplication, etc...). Vous pouvez si vous le souhaitez configurer un comosant une région alternative à celle par défaut de votre infrastrucure.

Sur le *concepteur d'infrastructure*, selectionner *Nouveau composant* et choisissez le service à configurer (par exemple : EC2, Lambda, etc...).

## Estimer l'empreinte 

L'estimation carbone d'une infrastructure va vous permettre d'avoir accès aux informations suivantes :

- **Prévision d'empreinte carbone mensuelle** : Une masse CO2eq prévue sur une période de un mois
- **Une aide à la comparaison** : Il sera plus facile de se rendre compte pour vos équipes de l'ampleur de l'impact carbone des changements apportés à vos infrastructures avec des rapprochements à des actions du quotidien (déplacements en voiture/avion, chauffage, etc...)
- **Un classement des ressources les plus émétrices** : Pour mieux concentrer vos effort d'amélioration
- **Des propositions de régions alternatives** : Une comparaison des régions proches en fonction de la différences financières et environnemental pour vous aider dans les décisions de migration.

## Réduire son empreinte 

Pour chacun des services cloud compatible avec IroCO2, vous pouvez vous référer au *Catalogue de disponibilité* pour y trouver des leviers d’actions permettant de commencer à éco-concevoir votre infrastructure et réduire son empreinte carbone.