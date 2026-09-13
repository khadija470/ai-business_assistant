# AI Business Assistant — Atelier Prompt Engineering

## Partie 1 – Anatomie d'un prompt

**Problème :** « Je souhaite analyser les retours de clients d'une entreprise. »

Un prompt efficace se construit à partir de plusieurs composantes : un **rôle**, un **contexte**, une **tâche**, des **données d'entrée**, des **contraintes**, un **format de sortie** et un **ton**.

### Prompt utilisé
```
Tu es un analyste expérimenté de la relation client, spécialisé dans l'exploitation des retours d'expérience.

Une entreprise a collecté des retours écrits de ses clients (avis, e-mails, réponses à enquêtes). Elle souhaite en tirer une vision claire pour améliorer ses produits et services.

Analyse l'ensemble des retours clients ci-dessous et produis une synthèse actionnable.

Retours clients :
- "Livraison rapide, produit conforme, je recommande !"
- "Application pratique mais elle plante souvent depuis la dernière mise à jour."
- "Service client injoignable, j'ai attendu 3 jours pour une réponse."
- "Bon rapport qualité-prix, rien à redire."
- "Colis arrivé en retard et abîmé, très déçu."
- "Interface claire, mais les frais de livraison sont trop élevés."

Contraintes :
- Base-toi uniquement sur les retours fournis, n'invente aucune information.
- Regroupe les retours par thème (livraison, prix, qualité, service client, application).
- Pour chaque thème, indique le sentiment dominant et sa fréquence approximative.
- Reste factuel et neutre.

Format de sortie :
1. Résumé général (3-4 phrases)
2. Tableau : Thème | Sentiment dominant | Points clés | Fréquence
3. Top 3 des problèmes prioritaires
4. Recommandations concrètes

Ton : professionnel et concis.
```

### Réponse du LLM (Gemini)
![Réponse Partie 1 - partie 1](captures/p1_analyse_retours_1.PNG)
![Réponse Partie 1 - partie 2](captures/p1_analyse_retours_2.PNG)
![Réponse Partie 1 - partie 3](captures/p1_analyse_retours_3.PNG)

### Commentaire
Le découpage en composantes rend le prompt clair et réutilisable : on peut changer les données d'entrée sans toucher au reste, et ajuster une seule contrainte pour modifier le comportement du modèle.


## Partie 2 – Comparer les techniques de prompting

**Commentaire à classer :** « Le service est rapide mais l'application plante régulièrement. »
**Classes possibles :** positif, négatif, neutre.

On compare quatre techniques de prompting :
- **Zero-shot** : consigne seule, aucun exemple.
- **One-shot** : un seul exemple fourni avant la question.
- **Few-shot** : plusieurs exemples fournis (le modèle imite le format).
- **Prompt structuré** : rôle, règles explicites et format de réponse imposé.

### Prompts utilisés
```
Zero-shot : Classe le commentaire, sans exemple.

One-shot : un exemple ("Produit excellent, livraison rapide." → positif) puis la question.

Few-shot : trois exemples (positif / négatif / neutre) puis la question.

Prompt structuré : rôle de système de classification, règle de la classe dominante,
justification en une phrase, aucune information inventée.
```

### Réponse du LLM (Gemini)
![Comparaison des techniques de prompting](captures/p2_comparaison1.PNG)

### Commentaire
Les quatre techniques classent le commentaire comme **négatif** (le défaut « application qui plante » domine l'aspect positif « service rapide »). Plus on fournit d'exemples et de structure, plus la réponse est **stable, concise et justifiée** : le zero-shot donne une réponse correcte mais parfois hésitante, tandis que le prompt structuré fournit directement la classe **et** une justification exploitable par une application.


## Partie 3 – Prompt Engineering et raisonnement

### 1. Décomposer un prompt

Un prompt vague donne des réponses désorganisées. **Décomposer** consiste à découper la tâche en sous-étapes explicites et ordonnées, pour guider le raisonnement du modèle.

Prompt initial vague : « Analyse ces avis clients et donne-moi les problèmes les plus importants ainsi que les recommandations. »

Version décomposée : extraire les thèmes → identifier le problème par thème → évaluer l'importance (fréquence + gravité) → classer les problèmes → proposer une recommandation par problème majeur.

![Décomposition 1](captures/p3_decomposition_1.PNG)
![Décomposition 2](captures/p3_decomposition_2.PNG)
![Décomposition 3](captures/p3_decomposition_3.PNG)
![Décomposition 4](captures/p3_decomposition_4.PNG)

### 2. Analyse puis auto-vérification

**Prompt A** — analyse d'un texte avec contraintes (max 80 mots, chiffres exacts, aucune invention, ton neutre).

![Analyse avec contraintes](captures/p3_analyse_1.PNG)

**Prompt B** — on demande au modèle de **vérifier sa propre réponse** : informations non justifiées, contradictions, omissions, hallucinations, respect des contraintes.

![Auto-vérification 1](captures/p3_verification_1.PNG)
![Auto-vérification 2](captures/p3_verification_2.PNG)

### Commentaire
Décomposer un prompt améliore la structure et la fiabilité de la réponse. L'auto-vérification est une technique clé du prompt engineering : elle pousse le modèle à relire sa production et à détecter ses propres erreurs (hallucinations, non-respect des contraintes), ce qui augmente la fiabilité des sorties dans une application métier.
## Partie 4 – Sorties structurées

Pour qu'une réponse soit exploitable par une application, on demande au modèle de répondre en **JSON** (format structuré lisible par un programme) plutôt qu'en texte libre.

### 1. Réponse au format JSON

On impose les champs `sentiment`, `categorie`, `urgence`, `probleme`, `confiance`, avec leurs types et leurs valeurs autorisées.

![Réponse JSON](captures/p4_json_1.PNG)

### 2. JSON avec règles de validation

On ajoute des règles strictes : JSON valide uniquement, aucune propriété supplémentaire, `sentiment` ∈ {positif, negatif, neutre}, `urgence` ∈ {faible, moyenne, elevee}, `confiance` entre 0 et 1.

![Réponse JSON validée](captures/p4_json_2.PNG)

### Commentaire
Imposer un format JSON avec des types et des valeurs autorisées rend la sortie du modèle directement exploitable par une application (parsing automatique, contrôle de validité). Les règles de validation réduisent les erreurs et garantissent une structure stable en sortie.



## Partie 5 – Prompts pour les applications métier

Cette partie applique le prompt engineering à cinq tâches métier concrètes.

### 1. Résumé de document
Contraintes : max 250 mots, informations factuelles conservées, objectifs / résultats / recommandations identifiés, aucune invention.

![Résumé de document](captures/p5_1.PNG)

### 2. Traduction FR → EN
Contraintes : sens et structure conservés, termes techniques conservés, pas de résumé, aucun ajout.

![Traduction](captures/p5_2.PNG)

### 3. Classification d'un ticket informatique
Sortie JSON avec les champs `categorie` et `justification` (catégories : reseau, logiciel, materiel, securite, acces, autre).

![Classification de ticket](captures/p5_3.PNG)

### 4. Extraction des données d'une facture
Extraction en JSON (numero_facture, date, client, montant_ht, tva, montant_ttc), avec `null` si une information est absente.

![Extraction de facture](captures/p5_4.PNG)

### 5. Rédaction d'un email de retard de livraison
Objectifs : reconnaître le retard, s'excuser, expliquer sans inventer, proposer une solution. Ton professionnel et courtois, max 150 mots.

![Email de retard](captures/p5_5.PNG)

### Commentaire
Chaque tâche métier repose sur des composantes de prompt adaptées : contraintes de longueur et de contenu pour le résumé et l'email, format JSON pour la classification et l'extraction. Définir clairement objectifs, contraintes et format de sortie permet d'obtenir des réponses directement exploitables dans un contexte professionnel.



## Partie 6 – Prompt Engineering pour le Machine Learning

Cette partie applique le prompt engineering à des tâches de data science, à partir du dataset des capteurs d'un bâtiment.

### 1. Stratégies de traitement des données
Détection, traitement et risques associés pour les valeurs manquantes, les doublons, les valeurs aberrantes et les variables catégorielles.

![Stratégies de nettoyage 1](captures/p6_1_1.PNG)
![Stratégies de nettoyage 2](captures/p6_1_2.PNG)
![Stratégies de nettoyage 3](captures/p6_1_3.PNG)
![Stratégies de nettoyage 4](captures/p6_1_4.PNG)

### 2. Visualisations pertinentes
Pour chaque visualisation : type de graphique, variables utilisées, objectif et interprétation attendue.

![Visualisations 1](captures/p6_2_1.PNG)
![Visualisations 2](captures/p6_2_2.PNG)
![Visualisations 3](captures/p6_2_3.PNG)
![Visualisations 4](captures/p6_2_4.PNG)

### 3. Modèles de prédiction de la consommation
Pour chaque modèle : principe, avantages, limites, type de problème et métriques pertinentes.

![Modèles 1](captures/p6_3_1.PNG)
![Modèles 2](captures/p6_3_2.PNG)
![Modèles 3](captures/p6_3_3.PNG)
![Modèles 4](captures/p6_3_4.PNG)
![Modèles 5](captures/p6_3_5.PNG)
![Modèles 6](captures/p6_3_6.PNG)

### 4. Métriques de classification
Accuracy, Precision, Recall, F1-score, ROC-AUC : définition, interprétation, exemple concret et contexte d'utilisation.

![Métriques classification 1](captures/p6_4_1.PNG)
![Métriques classification 2](captures/p6_4_2.PNG)
![Métriques classification 3](captures/p6_4_3.PNG)
![Métriques classification 4](captures/p6_4_4.PNG)
![Métriques classification 5](captures/p6_4_5.PNG)
![Métriques classification 6](captures/p6_4_6.PNG)
![Métriques classification 7](captures/p6_4_7.PNG)
![Métriques classification 8](captures/p6_4_8.PNG)

### 5. Métriques de régression
MAE, MSE, RMSE : définition, interprétation, exemple concret et contexte d'utilisation.

![Métriques régression 1](captures/p6_5_1.PNG)
![Métriques régression 2](captures/p6_5_2.PNG)
![Métriques régression 3](captures/p6_5_3.PNG)
![Métriques régression 4](captures/p6_5_4.PNG)
![Métriques régression 5](captures/p6_5_5.PNG)
![Métriques régression 6](captures/p6_5_6.PNG)
![Métriques régression 7](captures/p6_5_7.PNG)

### Commentaire
Le prompt engineering permet d'obtenir du modèle des explications structurées et des recommandations méthodologiques en data science. En imposant une grille de lecture claire (définition, interprétation, exemple, contexte pour les métriques ; principe, avantages, limites pour les modèles), on obtient des réponses pédagogiques et directement réutilisables.
