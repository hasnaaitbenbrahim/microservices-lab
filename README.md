## Étude de cas : Clients Synchrones (RestTemplate vs Feign vs WebClient) avec Eureka et Consul ##

### Capture Eureka ###

<img width="1511" height="909" alt="Screenshot 2025-12-17 at 18 06 28" src="https://github.com/user-attachments/assets/05b199ee-b6e1-432f-9950-f0ee6d11d9fe" />


### Capture Consul ###

<img width="1508" height="902" alt="Screenshot 2025-12-17 at 17 59 12" src="https://github.com/user-attachments/assets/b925eb4a-64ea-4eff-9818-28e09ab0248c" />

### Résultats de tests (latence, débit, CPU/RAM) ###

<img width="624" height="289" alt="Screenshot 2025-12-17 at 18 10 09" src="https://github.com/user-attachments/assets/7ce608f8-664c-46ba-b7e8-7efa5804b6d9" />

### Analyse comparée ###

-- Contexte de l’étude --

Dans le cadre de notre expérimentation, nous avons évalué trois solutions de communication entre microservices au sein d’un environnement Spring Cloud :

RestTemplate, le client HTTP synchrone historiquement utilisé avec Spring ;

OpenFeign, un client HTTP déclaratif permettant de réduire considérablement la complexité du code ;

WebClient, le client réactif plus récent de Spring, utilisé ici de façon bloquante afin de garantir une comparaison équitable.

Le microservice cible, nommé service-voiture, introduit volontairement un temps de traitement de 50 ms afin de simuler une charge réaliste.

-- Analyse des résultats obtenus --
------ Performances générales ------

Les tests montrent que les trois approches offrent des performances très proches, avec un débit moyen situé entre 170 et 180 requêtes par seconde.
Cette similarité s’explique principalement par le fait que le facteur limitant n’est pas le client HTTP lui-même, mais plutôt la latence réseau combinée au délai artificiel imposé par le service cible.

------ Stabilité et latence ------

En termes de régularité, OpenFeign se démarque légèrement dans ce scénario. Il présente une latence maximale relativement faible (78 ms) ainsi qu’un P99 de 66 ms, ce qui traduit un comportement stable.
RestTemplate affiche quelques variations plus marquées, avec des pics atteignant 134 ms.
Quant à WebClient, il enregistre la latence maximale la plus élevée (261 ms), probablement liée aux coûts d’initialisation ou à l’utilisation bloquante d’un modèle initialement réactif.

------ Simplicité de développement ------

Sur le plan de la productivité, OpenFeign s’impose clairement comme la solution la plus confortable. La définition d’une simple interface suffit, Spring se chargeant de générer automatiquement l’implémentation, ce qui rend le code clair et facile à maintenir.
À l’inverse, RestTemplate nécessite davantage de code répétitif, notamment pour la construction des URLs et la gestion des réponses.
WebClient, bien que très puissant, peut rapidement devenir verbeux pour des appels simples, surtout lorsqu’on n’exploite pas pleinement son modèle non bloquant.

