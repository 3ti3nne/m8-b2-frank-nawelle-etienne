# M8-B2 — Conception : Cabinet Maître Devalle (cas A)

**Groupe** : Etienne Roubaud · Franck Walter · Nawelle Polin
**Livrable principal** : [`dossier_conception.md`](dossier_conception.md), la fiche de décision (4 pages maximum).

## Lire la fiche en 2 minutes

1. **Le besoin** : le cabinet perd environ 30 min à chaque fois qu'il cherche **ses propres décisions** (environ 10 fois par jour). Il recopie aussi ses courriers types à partir de vieux courriers.
2. **Notre réponse** (§3, schéma) : un **moteur de recherche hybride** (mots-clés + sens) sur les ~2 000 décisions du cabinet, qui renvoie **5 décisions réelles avec le lien vers l'original**, et des **modèles Word** à jour pour les courriers.
3. **Ce qu'on refuse** (§2, §3) : **ni LLM, ni RAG génératif, ni agent**. La cliente ne tolère aucune décision inventée, et le besoin est de *retrouver* un document, pas d'en *rédiger* un.
4. **L'imprévu du 31/12** (§3, §5, §6) : sans prestataire informatique, rien ne tourne au cabinet. Les décisions sont copiées chez un hébergeur français infogéré avant son départ.
5. **Comment on saura que ça marche** (§4) : 30 recherches réelles, avec une baseline par **mots-clés seuls**. L'hybride n'est gardé que s'il apporte au moins 10 points de plus ; la bonne décision doit être dans le top 5 pour au moins 80 % des recherches.

Pour aller plus vite : lire le **§1** (nos décisions de groupe), le **§2** (les 5 arbitrages) et le **schéma du §3**.

## Qui a fait quoi

| Membre | Contributions |
|---|---|
| **Franck Walter** | §1 Décisions de groupe (divergences entre les 3 cadrages), §7 Coûts |
| **Etienne Roubaud** | Création du repo, §3 Architecture finale (schéma Mermaid + ce qu'on n'a pas mis), §5 Déploiement et monitoring, annexe des 5 questions prévues |
| **Nawelle Polin** | §2 Les 5 arbitrages, §4 Évaluation, §6 Conformité et sécurité, pseudo-code de la recherche hybride, README |

Le §1 et le §2 ont été validés par les trois membres. Les commits rédigés par un membre portent les deux autres en `Co-authored-by`.
