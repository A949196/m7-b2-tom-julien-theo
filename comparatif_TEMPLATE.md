# Comparatif des 3 options — 4 dimensions (À COMPLÉTER)

| Dimension | A — ML classique | B — Hybride LLM → ML | C — Multi-agents |
|---|---|---|---|
| **Conformité** *(qualifiée)* | _niveau + 2 risques + maîtrise_ | _…_ | _…_ |
| **Performance** *(chiffrée)* | _F1 classe « prolongé », abstention, p95_ | _…_ | _…_ |
| **Sobriété** *(chiffrée)* | _€/mois, €/inf. (ou €/doc) — kWh en bonus_ | _…_ | _…_ |
| **Évolutivité** *(qualifiée)* | _niveau + points de rupture_ | _…_ | _…_ |

> **Évolutivité** = capacité à intégrer de **nouvelles sources de données**, à
> faire **évoluer le modèle** et à **augmenter le volume** sans refonte majeure.
> Même définition pour les 3 options — sinon vous comparez des choses différentes.

> **Chiffré vs qualifié** : seules **sobriété** et **performance** se chiffrent.
> Conformité et évolutivité se **qualifient** — niveau *faible / intermédiaire /
> fort* + justification + mesures de maîtrise. Une conformité « 7/10 » est une
> fausse précision.

> Chaque cellule : ordre de grandeur **chiffré** (ou niveau **qualifié**) +
> **risque principal**.

## Hypothèses (obligatoire)

Tout chiffre du tableau renvoie à une hypothèse listée ici. Ce sont des
**ordres de grandeur fondés sur des hypothèses explicites, pas des mesures de
production** : le but est de rendre les options comparables, pas de prédire le
coût réel.

| # | Hypothèse | Valeur retenue | Source / date |
|---|---|---|---|
| H1 | Volume (dossiers/mois) | _…_ | _…_ |
| H2 | Tokens moyens par compte-rendu (in / out) | _…_ | _…_ |
| H3 | Tarif du modèle retenu | _…_ | _…_ |

> ⚠️ **Performance de l'option B** : aucun gain annoncé sans le **protocole
> d'ablation** qui le prouverait (même modèle avec / sans les variables
> extraites, même jeu de test, même métrique) — cf. `ressources/02`.
