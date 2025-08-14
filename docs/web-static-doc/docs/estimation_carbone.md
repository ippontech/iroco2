# Estimation carbone

## 1. Périmètre de mesure 

Les émissions de carbone résultantes de la consommation de services sur le cloud public peuvent être catégorisées en deux types : 

- **La consommation électrique** : C'est le scope 2 du cloud provider,
qui correspond au carbone équivalent émis par la production de l'énergie électrique (centrale à charbon, solaire, etc...),
consommée par le matériel (serveur, climatiseurs, etc...) pour fournir le service demandé.
- **La chaîne de valeur du matériel** : C'est le scope 3 du cloud provider,
qui correspond au carbone équivalent émis par la fabrication et l'acheminement du matériel utilisé pour fournir le service demandé.

IroCO2 prend actuellement en compte la consommation électrique pour estimer l’empreinte carbone de vos ressources sur le cloud.
La prise en compte de la chaîne de valeur pourra y être intégrée quand les sources de données,
concernant le matériel physique utilisé par une ressource cloud ainsi que leur cycle de vie au sein des datacenters,
seront plus fournies.

## 2. Paramètres de consommation

Afin d’estimer la consommation électrique d’un service consommé sur le cloud, il faut se baser sur différents critères qui sont les suivants :

### 2.1. CPU

L'allocation de CPU sur le cloud se fait à travers la notion de vCPU.
Etant donné que l'on peut considérer qu'un cœur d'un CPU physique est égal à un vCPU (dans la virtualisation des ressources matérielles),
nous pourrons établir une relation de consommation entre les ressources physiques et virtuelles. 

Il faut ensuite prendre en compte la notion de TDP (Thermal Design Power) qui, selon les constructeurs de CPU,
peut être rapprochée à la puissance électrique maximale d'une puce processeur.
On peut donc en conclure que si un CPU a un TDP de 100W (à 100% de ses capacités), et qu'il est utilisé à 50% de ses capacités,
alors il aura une puissance de 50W. Afin de simplifier le calcul dans le cadre de l’_Estimateur_,
nous assumons que le CPU étudié est constamment utilisé à 50% de ses capacités. 

Il n'est pas possible de prévoir à l'avance avec exactitude quel modèle de processeur sera alloué par le cloud provider à votre ressource.
Il existe tout de même un moyen de faire la disctinction entre les processeurs en amont d'un déploiement afin d'éco-concevoir vos infrastructures cloud.
Pour cela, nous nous basons sur le type d'architecture CPU qui fait fortement varier le TDP moyen.
Par exemple, les processeurs de type ARM aurons en moyenne un TDP bien plus bas que ceux de type x86.

En reprenant tous ces élements, on peut estimer la consommation électrique d'un ensemble de vCPUs alloués à une ressource sur le cloud public avec la formule suivante :

$$
\text{Consommation électrique} = 0,5
\times \text{Nombre de CPU}
\times \frac{\text{TDP de l'architecture CPU}}{\text{Nombre de cœurs physiques}}
\times \text{Temps d'utilisation}
$$

### 2.2. Mémoire

Nous utilisons les constantes de consommation électrique de l’étude [Etsy Cloud Jewels](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud).  

### 2.3. Stockage

Nous utilisons les constantes de consommation électrique de l’étude [Etsy Cloud Jewels](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud).   

### 2.4. GPU

Afin d'estimer la consommation électrique d'une carte GPU,
nous nous basons sur les mêmes principes que le calcul du CPU,
à l'exception que nous prenons en compte la totalité du GPU,
il n'y a donc pas de notion de vGPU dans notre cas d'étude.

## 3. Mix énergétique

Le mix énergétique d'un pays ou d'une région réprésente la répartition des différents types de production d'énergie dans la zone.
Cette répartition permet de déterminer le coût carbone de l'énergie consommée à cet endroit, exprimé en gCO2eq / kWh.

C'est avec cette donnée que nous sommes en capacité d'estimer l'empreinte carbone de l'énergie consommée pour vos activités sur le cloud.
Par exemple, si vos ressources déployées en France consomment 50 kWh et étant donné le coût carbone de l'énergie française d'environ 30 gCO2eq pour 1 kWh,
alors l'empreinte carbone de l'utilisation de ces dernières sera de 1,5 kgCO2eq.

## 4. Limitations

Voici une liste de paramètres généraux que nous ne prenons pas en compte pour le moment : 

* Consommation électrique du trafic réseau
* PUE des datacenters AWS (non fournis)
* Réplications non exhaustives des ressources
* Pas de différenciation entre les différents modèles de processeurs Graviton
* Pas de différenciation entre les types de stockages (HDD, SSD, etc...)

Vous pouvez retrouver, et pour chacun des services cloud compatibles avec IroCO2,
les limitations précises (prise en compte partielle du service, manque de données, etc...).

## 5. Sources

* Cloud Jewels : [Estimating kwh in the cloud](https://www.etsy.com/codeascraft/cloud-jewels-estimating-kwh-in-the-cloud)
* Cloud Carbon Footprint : [Methodology](https://www.cloudcarbonfootprint.org/docs/methodology/)
* Microsoft : [How can I calculate co2eq emissions for my Azure VM?](https://devblogs.microsoft.com/sustainable-software/how-can-i-calculate-co2eq-emissions-for-my-azure-vm/)
* Intel : [Processor specifications and compatibility](https://www.intel.com/content/www/us/en/support/articles/000055611/processors.html)
* [AWS Documentation (physical specification of services, replication, ...)](https://docs.aws.amazon.com/)
