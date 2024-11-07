# I- Sélection des composants AWS  nécéssaire pour déployer l'app

1- **Amazon Virtual Private Cloud (VPC)** : pour créer un réseau privé isolé, permettant de mieux contrôler la sécurité et le trafic des ressources. Le VPC comporte des sous-réseaux publics et privés dans deux zones de disponibilité pour assurer la haute disponibilité et la résilience aux pannes.

2- **Sous-réseaux publics et privés** :

- Sous-réseaux publics : pour héberger les services frontend et backend d’Amazon ECS Fargate, avec accès contrôlé depuis Internet via un ELB.
- Sous-réseaux privés : pour héberger la base de données Amazon RDS Aurora Serverless, à l’abri des accès directs depuis Internet.

3- **Amazon ECS avec Fargate** : pour l'exécution des containers Docker sans gestion de serveur. Un cluster ECS est déployé dans chaque AZ avec deux services distincts pour frontend et backend, assurant scalabilité et tolérance aux pannes.

4- **Elastic Load Balancer (ELB)** : pour répartir le trafic utilisateur entre les services frontend et backend hébergés dans les deux zones de disponibilité.

5- **Amazon Route 53** : pour la gestion DNS et la redirection du trafic vers le ELB, avec haute disponibilité et redondance géographique.

6- **Amazon Relational Database Service (RDS) Aurora Serverless** : pour la base de données backend, hébergée dans des sous-réseaux privés. Aurora Serverless est entièrement managée, autoscalable, et ne nécessite pas de gestion d'infrastructure.

7- **Amazon S3** : pour stocker les assets statiques du frontend. Les fichiers stockés sont distribués avec CloudFront, optimisant la vitesse d’accès.

8- **Amazon CloudFront** : pour diffuser les assets statiques du frontend à travers le monde avec une faible latence, améliorant l'expérience utilisateur.

9- **Amazon ECR (Elastic Container Registry)** : pour stocker et gérer les images Docker frontend et backend, qui peuvent être directement tirées par ECS Fargate lors du déploiement.


# II- Schéma décrivant l'infrastructure à déployer
![alt text](schema_d'infrastructure_aws.png)


# III- Justification des choix techniques
**1- Amazon VPC et sous-réseaux** 

Le VPC offre un environnement sécurisé et isolé pour l’infrastructure. Les sous-réseaux publics hébergent les services ECS Fargate du frontend et backend, qui nécessitent un accès direct à Internet via le ELB, tandis que les sous-réseaux privés hébergent la base de données RDS Aurora, qui reste isolée pour des raisons de sécurité. Cette architecture permet également de contrôler précisément le trafic entre les services et la base de données, en utilisant des groupes de sécurité AWS.

**2- Amazon ECS avec Fargate**

L’utilisation d’ECS avec Fargate dans un environnement serverless simplifie le déploiement et la gestion des containers. En ayant des clusters distincts dans chaque AZ, les services sont répliqués, assurant haute disponibilité et continuité d’activité en cas de panne d’une zone. ECS Fargate permet également de mettre à l’échelle les services en fonction de la demande, et de gérer facilement le cycle de vie des containers.

**3- Elastic Load Balancer (ELB) et Amazon Route 53**

L’ELB répartit intelligemment le trafic entre les zones de disponibilité, garantissant une expérience utilisateur constante même en cas de surcharge ou de panne d'une zone. Route 53 permet de gérer les DNS avec redondance et géolocalisation, simplifiant la gestion de domaine et l’acheminement du trafic.

**4- Amazon RDS Aurora Serverless**

Aurora Serverless s’adapte automatiquement aux variations de charge, simplifiant l’administration de la base de données tout en optimisant les coûts. Hébergée dans des sous-réseaux privés, cette base de données est protégée contre des accès directs depuis Internet, tout en restant accessible par les services ECS via le VPC.


# IV- Bonus:  service AWS utilisé pour uploader les images Docker et les rendre disponibles dans le projet AWS
**Amazone ECR** : les images Docker frontend et backend ont été stockées dans Amazon Elastic Container Registry (ECR). ECR est un service managé qui facilite le stockage sécurisé des images Docker, tout en permettant à ECS Fargate de tirer directement ces images pour le déploiement. ECR garantit également la haute disponibilité des images et leur accessibilité rapide pour les clusters ECS, simplifiant le déploiement et la gestion continue des containers Docker.
