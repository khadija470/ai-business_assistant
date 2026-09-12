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
