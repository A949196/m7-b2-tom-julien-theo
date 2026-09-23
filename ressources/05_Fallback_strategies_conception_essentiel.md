# Fallback strategies en conception — Mini-cours

> Brief associé : M7-B2
> Durée de lecture : ~20 min
> Pré-requis : notion de seuil, de confiance modèle

## Pourquoi cette techno ?

Un système IA se trompe parfois. Une bonne **architecture** prévoit **quoi faire
quand le modèle n'est pas sûr** : c'est la **fallback strategy**. La concevoir
**dès l'architecture** (pas en réaction à un incident) est un marqueur de
maturité — et un atout de conformité (AI Act supervision humaine). ⚠️ Ici on
parle de fallback en **conception** (choix d'archi), distinct de la réaction au
**drift** en exploitation (M6).

## Concepts clés

- **Seuil de rejet** : si la probabilité prédite est dans une zone d'incertitude
  (ex. 0.4–0.7), **ne pas décider automatiquement** → router vers un humain.
- **Abstention** : le système répond « je ne sais pas » plutôt que de forcer une
  réponse (utile en RAG : pas de contexte → abstention).
- **Human-in-the-loop (HITL)** : un humain tranche les cas incertains. Ce n'est une
  supervision effective que si l'on précise **qui** (rôle, compétence), son
  **autorité**, le **délai**, la **trace** et le **pouvoir réel de contredire** —
  un agent superviseur qui route vers une file ne fournit rien de cela par lui-même.
- **Fallback par option** : chaque architecture a son fallback naturel
  (A : seuil de rejet ; B : extraction incertaine → champ `null` + relecture
  humaine ; C : HITL via superviseur ; assistant RAG : abstention).
- **Risque d'abord, obligation si applicable** : chaque fallback répond à un
  **risque** (erreur coûteuse, valeur inventée, hallucination). Il peut aussi servir
  une obligation **si elle s'applique** : supervision humaine (AI Act art. 14) pour
  un système **qualifié** haut risque ; intervention humaine au titre du RGPD
  art. 22 **seulement** si la décision est exclusivement automatisée **et** à effet
  juridique ou similairement significatif.
- **Conception ≠ exploitation** : ici on **choisit** la stratégie ; en M6 on
  **réagit** à une dérive observée.

## Exemple minimal qui tourne

```text
Option A — seuil de rejet :
  if 0.4 <= proba < 0.7:  → revue humaine
  else:                   → décision automatique tracée
```

## Exercice guidé

Pour chacune des 3 options MediVox, propose **un** fallback :
1. Option A (ML) : quel seuil de rejet ?
2. Option B (hybride) : que fait-on d'une extraction incertaine ou invalide ?
3. Option C (agents) : quand le superviseur appelle-t-il un humain ?
Relie chaque fallback à un **risque** et, si applicable, à l'obligation correspondante.

## Pièges fréquents

| Piège | Conséquence |
|---|---|
| Aucun fallback | Erreurs coûteuses sans rattrapage prévu |
| Confondre fallback (conception) et drift (M6) | Mauvais cadre |
| Seuil de rejet arbitraire | Non justifiable |
| HITL « théorique » (file humaine sans qui / délai / trace / pouvoir de contredire) | Supervision fictive, validation par réflexe |
| Citer AI Act art. 14 ou RGPD art. 22 sans qualification | Obligation plaquée là où elle ne s'applique pas |
| Abstention non prévue en RAG | Hallucinations |
| Extraction incertaine injectée telle quelle | Le modèle ML apprend et prédit sur des valeurs inventées |

| Symptôme | Cause probable |
|---|---|
| Supervision contestée | rôle, délai ou pouvoir de contredire non décrits |
| Trop de cas en revue humaine | seuil trop large (coût opérationnel) |

## Pour aller plus loin

- AI Act — supervision humaine : https://artificialintelligenceact.eu/the-act/
- Cf. mémoire interne : fallback (conception) ≠ OOD/drift (exploitation).

## Vérification (checklist apprenant)

- [ ] Chaque option a un fallback explicite (seuil / abstention / HITL).
- [ ] Je relie chaque fallback à un risque et, si applicable, à l'obligation correspondante.
- [ ] Je distingue fallback (conception) et réaction au drift (M6).
- [ ] Mes seuils sont justifiés, pas arbitraires.
- [ ] Le HITL est décrit comme une **procédure** (qui, autorité, délai, trace, pouvoir de contredire), pas un vœu.

> 💡 **Récap** : prévoir **dès l'architecture** quoi faire quand le modèle n'est pas
> sûr — seuil de rejet (A), `null` + relecture (B), HITL (C), abstention (RAG) — et relier chaque fallback à un
> **risque** et, si applicable, à l'obligation correspondante (AI Act art. 14 si haut risque, RGPD art. 22 si ses 2 conditions). Fallback en **conception** ≠ réaction au
> **drift** en exploitation (M6).

*Réflexe : un fallback est une **procédure** (qui, quand, comment), pas une intention — sinon il n'existe pas en pratique.*
