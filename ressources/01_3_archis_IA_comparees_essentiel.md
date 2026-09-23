# 3 architectures IA comparées — Mini-cours

> Brief associé : M7-B2
> Durée de lecture : ~25 min
> Pré-requis : audit M7-B1, grille de décision C4 (M4)

## Pourquoi cette techno ?

Face à une évolution, la tentation est de choisir la techno « à la mode » (LLM,
agents). Le geste pro inverse : **comparer** des architectures de natures
différentes sur les **mêmes dimensions**, puis **trancher** selon le besoin réel.
Sur un problème tabulaire simple, la réponse est souvent « moderniser l'existant »
— et c'est un excellent verdict, pas un aveu de paresse.

## Concepts clés

- **A — ML classique modernisé** : le modèle actuel + industrialisation (CI/CD,
  monitoring). Explicable, sobre, conforme. Ignore le texte non structuré.
- **B — Hybride LLM → ML** : un LLM **extrait**, sous schéma contrôlé, des
  variables des comptes-rendus ; après validation (incertitude, relecture
  humaine), elles **enrichissent le modèle ML** qui, lui, prédit. Exploite le
  texte ; ajoute une stack LLM, un coût par document et un risque d'extraction.
- **Hors comparatif — l'assistant documentaire RAG** : répondre aux équipes à
  partir d'un corpus est un **autre produit** (sortie = texte, pas un score).
  Il peut répondre à un vrai besoin, mais il ne se compare pas au prédicteur.
- **C — Multi-agents** : des agents spécialisés qui coopèrent. Modulaire ; complexe,
  risque de sur-engineering. Router vers un humain n'y est pas plus « natif »
  qu'ailleurs : la supervision se conçoit dans toute option.
- **4 dimensions de comparaison** : conformité, performance, sobriété, évolutivité.
- **Anti-pattern de mode** : choisir B ou C parce que « c'est moderne » sans gain
  démontré = perte de points (et d'argent client).
- **Le bon réflexe** : commencer par la solution la plus simple qui répond au
  besoin ; ne complexifier que si un gain est **prouvé**.

## Exemple minimal qui tourne

Sur un **autre** cas (tri de réclamations d'un bailleur social — pas le
vôtre) : la structure se réutilise, **pas les valeurs ni le verdict** —
chaque cellule se re-dérive de VOTRE contexte, chiffres à l'appui.

```markdown
| Dimension | A ML | B hybride | C agents |
|---|---|---|---|
| Conformité | à évaluer | à évaluer | à évaluer |
| Sobriété (€/mois) | ~X | ~10-20X | ~5-10X |
| Perf (gain) | référence | à démontrer | à démontrer |
→ Recommandation : UNE option, conditionnée (« sauf si <preuve>, alors <option> »).
```

Les ordres de grandeur de coût se chiffrent avec le mini-cours
`04_Estimation_couts_LLM` — un « ~10-20X » nu, sans calcul derrière, est
exactement l'argument non défendable du tableau des pièges.

## Exercice guidé

Pour le prédicteur MediVox :
1. Pour chacune des 3 options, note **1 force + 1 faiblesse** majeures.
2. Sur quelle dimension B ou C pourrait-elle battre A ? À quelle condition ?
3. Quelle option recommanderais-tu **par défaut** ? Pourquoi ?

## Pièges fréquents

| Piège | Conséquence |
|---|---|
| Choisir la techno avant d'analyser le besoin | Sur-engineering |
| Comparer sur des dimensions différentes par option | Comparaison invalide |
| Comparer des produits différents (un RAG qui répond vs un modèle qui prédit) | Colonne sans métrique commune |
| Recommander 3 options | Indécision — il faut trancher UNE |
| Écarter A par principe | On rate souvent la meilleure réponse |
| Gain « intuitif » non chiffré | Argument non défendable |

| Symptôme | Cause probable |
|---|---|
| Recommandation contestée | gain non démontré, dimensions floues |
| « pourquoi pas un agent ? » | absence d'argument de sobriété explicite |

## Pour aller plus loin

- Grille de décision C4 (M4-B1) — structure de comparaison.
- AI Act — implications selon l'architecture : https://artificialintelligenceact.eu/the-act/

## Vérification (checklist apprenant)

- [ ] Je compare les 3 options sur les **mêmes** 4 dimensions.
- [ ] Je sais dire quand A suffit (souvent) et quand B/C se justifient.
- [ ] Je reconnais l'anti-pattern de mode.
- [ ] Je tranche **une** recommandation.
- [ ] Mon gain annoncé est chiffré ou conditionné à une preuve.

> 💡 **Récap** : comparer A (ML modernisé) / B (hybride LLM → ML) / C (agents) sur les **mêmes**
> 4 dimensions, puis **trancher UNE** option. Réflexe : la solution la plus **simple**
> qui répond au besoin ; ne complexifier que si un gain est **prouvé**. Sur du tabulaire,
> « moderniser l'existant » est souvent le meilleur verdict (sobriété).

*Réflexe : la bonne question n'est pas « quelle est la techno la plus avancée ? » mais « quelle est la plus simple qui résout le problème ? ».*
