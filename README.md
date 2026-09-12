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
