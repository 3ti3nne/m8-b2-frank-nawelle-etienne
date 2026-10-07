# Fiche de décision — <votre cas> (À COMPLÉTER)

**Client :** <nom, rôle> · **Groupe :** <prénoms> · **Cas <A/C/D>**

> **Livrable principal — 3-4 pages au maximum** (un plafond, pas une cible).
> Lisible par un architecte technique. Renommez en `dossier_conception.md`.
> Tout s'écrit **ici, une seule fois** : décisions, arbitrages, schéma et
> questions prévues. Tableaux plutôt que paragraphes.

---

## 1. Décisions de groupe (mardi 15h30-16h45)

> 3-5 points où vos cadrages M8-B1 divergeaient. Pas de compromis mou : un
> choix tranché et argumenté.

| Divergence | Positions (qui pensait quoi) | Décision retenue | Pourquoi |
| --- | --- | --- | --- |
| Périmètre du projet | <ul><li>Franck et Étienne : interface de recherche utilisée par les assistantes pour retrouver les décisions, les trier manuellement et conserver les jurisprudences utiles à la rédaction des documents.</li><li>Nawelle : recherche et modèles de courriers préremplis, avec récupération des informations du logiciel de gestion si son éditeur le permet.</li></ul> | Limiter le projet à une **interface de recherche** avec d'autre part des **modèles préremplis**. | Une intervention humaine est nécessaire pour filtrer les résultats ; les informations disponibles ne permettent pas de confirmer la faisabilité de l'automatisation des courriers. |
| Recherche hybride vs recherche par mots-clés | <ul><li>Nawelle : recherche par mots-clés, solution minimale qui peut être suffisante.</li><li>Étienne et Franck : recherche hybride, combinant mots-clés et mots apparentés par le sens pour ne rien rater.</li></ul> | **Recherche hybride** | Augmenter le rappel pour ne rater aucun DOC. |
| Solution pour gagner du temps sur le courrier | <ul><li>Étienne : modèles Word préremplis avec des champs à remplir.</li><li>Franck : pas de solution.</li><li>Nawelle : modèles Word préremplis avec des champs à remplir et récupération des informations du logiciel de gestion pour préremplir les documents.</li></ul> | Proposer des **modèles Word** au client et **étudier la faisabilité d'un lien avec le logiciel de gestion**. | Solution simple sans IA pour gagner du temps sur le courrier. |
| KPI de recherche | <ul><li>Étienne : passer de 30 minutes à moins d'une minute par recherche ; la décision recherchée doit figurer parmi les 5 premiers résultats.</li><li>Nawelle : seuil de rappel de 80 % pour revoir la solution, tolérance à l'invention de 0, signature humaine de 100 % des courriers ; objectif d'1 h gagnée par avocat jugé irréaliste, avec environ 5 h de recherche par jour estimées.</li><li>Franck : passer de 30 minutes à 30 secondes par recherche, 75 % de précision et 95 % de rappel.</li></ul> | **30 secondes par recherche** (tolérance : **quelques minutes**) + **décision recherchée dans les 5 premiers résultats**. | Mesurer le temps et la qualité de chaque recherche ; le gain quotidien dépend du nombre de recherches par avocat. |
| Anonymisation / pseudonymisation | <ul><li>Franck : anonymisation ou pseudonymisation lors de la préparation des données.</li><li>Étienne et Nawelle : aucune anonymisation ou pseudonymisation proposée dans leurs cadrages.</li></ul> | **Anonymiser les modèles de courriers** ; conserver les **décisions sans anonymisation** ; disposer d'un **jeu de test pour les embeddings 100 % anonyme**. | L'anonymisation complète des décisions représente trop de travail. Celle des modèles de courriers est importante car les modèles seront réutilisés constamment et ne doivent donc pas contenir de données personnelles. |

## 2. Les 5 arbitrages

> Pour chacun : **choix + raisons (≥ 1 chiffrée) + condition de changement
> d'avis** — ou « non applicable » en une ligne justifiée (+ ce qui ferait se
> poser la question). Répondre « SLM » ou « RAG » sur un cas sans texte pour
> remplir la grille est un signal de tropisme.

| Arbitrage | Choix — ou « non applicable » | Raisons (≥ 1 chiffrée) | On changerait d'avis si… |
| --- | --- | --- | --- |
| ML classique vs deep learning | **Deep learning pré-entraîné, sans aucun entraînement** : un modèle d'embeddings sert pour la partie sémantique de la recherche hybride. Aucun modèle ML classique, faute de tâche de prédiction | Aucun label de sujet juridique : le registre ne donne que matière, date et issue, donc rien à apprendre. Avec ~2 000 décisions, c'est trop peu pour entraîner un modèle de langue, mais assez peu pour les vectoriser en quelques minutes sur CPU (~10-50 ms par document, coût ~0 €) | On voudrait **classer automatiquement les nouvelles décisions par matière** → ML classique (TF-IDF + régression logistique) entraîné sur les matières déjà saisies dans le registre |
| SLM vs LLM | **Non applicable** : aucun texte généré, l'outil affiche des décisions réelles et des modèles Word | Tolérance à l'invention : **0** (« Ça, jamais chez nous », Q7). Un SLM hébergé en France sur GPU coûterait **300 à 1 500 € par mois**, hors du budget de « quelques centaines d'euros par mois » (Q8) | Le cabinet demande des **résumés de décisions** → SLM hébergé en France, en **batch une seule fois** sur les ~2 000 décisions (pas de GPU permanent), avec relecture humaine |
| RAG oui / non | **Non** : on garde le *retrieval* (recherche hybride), **sans génération**. Le résultat est une liste des 5 meilleures décisions avec le lien vers l'original | Le besoin est de **retrouver un document** (~10 recherches par jour, soit ~220 par mois), pas d'obtenir une réponse rédigée. Sans génération : aucune invention possible, et pas de prompt injection via des conclusions adverses indexées | Les avocats demandent une **synthèse de plusieurs décisions** **et** le KPI (décision dans les 5 premiers résultats) est déjà atteint → RAG avec citation obligatoire des sources, une nouvelle analyse AI Act (art. 50) et un filtrage du corpus contre l'injection |
| Agents oui / non | **Non** : une recherche correspond à une requête, et un courrier à un modèle rempli. Il n'y a pas de chaîne d'actions à orchestrer | ~15 courriers par jour, **100 % relus et signés par un avocat** (Q3) : l'envoi est irréversible et engage la responsabilité professionnelle. Un agent ajouterait des droits d'écriture sans aucun gain | **Jamais pour l'envoi.** On reposerait la question seulement pour un enchaînement en **lecture seule** (par exemple recherche puis ouverture du dossier dans le logiciel de gestion), avec des droits minimaux |
| Zero-shot suffit ? | **Oui** : les embeddings pré-entraînés sont utilisés **tels quels**, sans fine-tuning | **0** paire « question → bonne décision » existe aujourd'hui, donc démarrage à froid. Le jeu de test (30 recherches réelles, anonymisé, cf. §1) sert **à mesurer, pas à entraîner** | Sur le jeu de test, **moins de 80 % des recherches** trouvent la bonne décision dans les 5 premiers résultats → modèle d'embeddings **spécialisé en droit français**, puis fine-tuning si l'on collecte quelques centaines de paires validées |

## 3. Architecture finale et sobriété

```mermaid
flowchart LR
    subgraph SRC["Cabinet"]
        SRV[("Serveur du cabinet<br/>~2 000 décisions + registre")]
        ASS("Assistante du registre<br/>seule à déposer")
    end
    LGC[("Logiciel de gestion<br/>hébergé en France")]

    subgraph HEB["Hébergeur français sous contrat : VM CPU, chiffré, sauvegardé"]
        ORI[("1. Originaux<br/>+ registre")]
        ING(["2. Ingestion batch (cron)<br/>OCR, passages,<br/>embeddings pré-entraînés"])
        BDD[("3. PostgreSQL<br/>plein texte + pgvector<br/>+ filtres du registre")]
        RECH["4. Recherche hybride<br/>fusion RRF mots + sens<br/>top 5 + extrait + lien<br/>aucun texte généré"]
        LOG[("6. Journal + métriques<br/>alertes à la référente")]
        MOD[("5. Modèles Word<br/>anonymisés, sans IA")]
    end

    LECT{"Avocat ou assistante<br/>double authentification<br/>lit l'original : utile ?"}
    USE[/"Utilisée<br/>dans le dossier"/]
    COUR("Assistante<br/>remplit le modèle")
    SIG{"L'avocat relit<br/>et signe ?"}
    ENV[/"Envoi : 100 %<br/>des courriers signés"/]

    SRV -->|"copie unique avant le 31/12,<br/>puis accès prestataire coupés"| ORI
    ASS -->|"nouvelle décision"| ORI
    ORI --> ING --> BDD --> RECH
    RECH -->|"top 5"| LECT
    LECT -.->|"non : nouvelle requête"| RECH
    LECT -->|oui| USE
    RECH -.-> LOG

    LGC -.->|"pré-remplissage :<br/>faisabilité à l'étude"| MOD
    MOD --> COUR --> SIG
    SIG -.->|"non : correction"| COUR
    SIG -->|oui| ENV

    classDef data fill:#e5e7eb,stroke:#6b7280,color:#111
    classDef code fill:#ffffff,stroke:#6b7280,color:#111
    classDef ml fill:#bfdbfe,stroke:#1d4ed8,color:#111
    classDef dec fill:#fef08a,stroke:#a16207,color:#111
    classDef hum fill:#bbf7d0,stroke:#15803d,color:#111
    classDef out fill:#e9d5ff,stroke:#7e22ce,color:#111
    class SRV,LGC,ORI,BDD,LOG,MOD data
    class RECH code
    class ING ml
    class LECT,SIG dec
    class ASS,COUR hum
    class USE,ENV out
```

- **Hébergeur, 1 et 6** : imprévu de mardi (prestataire parti le 31/12) : rien ne tourne au cabinet, copie des décisions avant son départ, journal suivi par l'hébergeur avec alertes à la référente du cabinet.
- **2 et 3** : arbitrages 1 et 5 : embeddings pré-entraînés utilisés tels quels, sur CPU, sans entraînement ; texte, filtres et vecteurs dans une seule base (≈ 40 000 passages × 384 dimensions × 4 octets ≈ 60 Mo).
- **4** : §1 (recherche hybride) et arbitrages 2 et 3 : on renvoie 5 décisions réelles avec le lien vers l'original, jamais un texte généré.
- **5 et losanges** : §1 (courriers, anonymisation) et arbitrage 4 : modèles Word sans IA, chaque résultat lu et chaque courrier signé par un humain.

**Ce qu'on n'a PAS mis (obligatoire)** :

- **Pas de LLM, de SLM ni de RAG génératif** (arbitrages 2 et 3) : tolérance à l'invention de 0 ; un GPU coûterait 300 à 1 500 € par mois, hors budget.
- **Pas de base vectorielle dédiée** : ≈ 60 Mo de vecteurs tiennent dans la base PostgreSQL déjà nécessaire (pgvector) ; sans prestataire après le 31/12, chaque logiciel en moins est une panne en moins.
- **Pas d'agent** (arbitrage 4) : l'envoi d'un courrier est irréversible, il reste 100 % humain.
- **Pas d'entraînement, de fine-tuning ni de registre de modèles (MLflow Registry)** (arbitrages 1 et 5) : un seul modèle, figé, aucune paire labellisée ; il ne change qu'après le jeu de test (§2).
- **Pas de GPU ni d'orchestrateur (Kubernetes, Airflow)** : indexation complète une seule fois sur CPU (≈ 40 000 passages × 10-50 ms ≈ 7 à 35 min), puis ≈ 130 nouvelles décisions par an ajoutées par une tâche de nuit (cron).
- **Pas de reclassement par un 2e modèle (reranker)** : un modèle de plus à héberger ; à ajouter seulement si le jeu de test montre la bonne décision dans le top 10 mais hors du top 5.
- **Pas d'installation au cabinet ni de cloud américain** : plus personne pour l'entretenir après le 31/12 ; dossiers sous secret, exigence du client (Cloud Act).

## 4. Évaluation

<!-- Comment on saura que ça marche AVANT la mise en service :
     baseline simple à battre, découpage des données (temporel si le temps compte),
     métriques alignées sur le KPI métier du cadrage. -->

## 5. Déploiement et monitoring (héritage M5 / M6)

<!-- Où et comment ça tourne, rollback. Puis : -->

| Question | Métrique | Seuil | Alerte vers |
| --- | --- | --- | --- |
| En vie ? |  |  |  |
| Prédit bien ? |  |  |  |
| Données qui dérivent ? |  |  |  |

<!-- Quand réentraîner, et qui décide. -->

## 6. Conformité et sécurité

<!-- Qualification AI Act et base légale RGPD reprises des cadrages (raisonnement,
     pas étiquette), en tenant compte de l'imprévu client de mardi 14h30.
     Chaque menace de sécurité retenue → sa réponse d'architecture + le risque résiduel. -->

## 7. Coûts (ordres de grandeur, sur VOTRE volumétrie)

<!-- ressources/fiche_chiffrage.md — à recalculer, pas à recopier. -->

| Poste | Estimation | Hypothèse |
| --- | --- | --- |
|  |  |  |

---

## ⭐ Optionnel — Pseudo-code du composant critique

```text
fonction <nom>(<entrées>):
    # 10-20 lignes : cas nominal, cas limite (donnée manquante, confiance basse…),
    # ce qui est journalisé.
```

## Annexe — 5 questions prévues (hors pagination)

| # | Question probable | Réponse préparée (2-3 lignes) | Qui répond |
| --- | --- | --- | --- |
| 1 |  |  |  |
| 2 |  |  |  |
| 3 |  |  |  |
| 4 |  |  |  |
| 5 |  |  |  |
