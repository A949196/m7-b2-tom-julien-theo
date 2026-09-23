# Multi-agents (LangGraph) — conception — Mini-cours

> Brief associé : M7-B2
> Durée de lecture : ~25 min
> Pré-requis : notion de LLM, conception RAG (mini-cours 02)

## Pourquoi cette techno ?

Une architecture **multi-agents** découpe une tâche en **agents spécialisés** qui
coopèrent (un valide, un prédit, un explique, un supervise). C'est séduisant —
modulaire, avec un routage vers un humain facile à dessiner — mais **coûteux et complexe**. En M7-B2 on
**conçoit** l'option C pour la **comparer**, en gardant un œil critique : sur un
problème tabulaire simple, des agents sont souvent du **sur-engineering**.

## Concepts clés

- **Agent** : une unité (souvent un LLM + des outils) avec un rôle précis et la
  capacité de décider de l'étape suivante.
- **Orchestration (LangGraph)** : un graphe d'états qui relie les agents ; chaque
  nœud transforme un **état partagé** et choisit la transition.
- **État partagé** : le contexte qui circule entre agents (input, prédictions,
  justifications, niveau de confiance).
- **Routage vers un humain ≠ supervision humaine** : un agent **superviseur** peut
  envoyer un cas incertain vers une file humaine, mais cela ne crée ni supervision
  effective ni conformité par soi-même. La supervision se **conçoit** : **qui**
  tranche (rôle, compétence), avec quelle **autorité**, dans quel **délai**, quelle
  **trace** est gardée, et le **pouvoir réel de contredire** le système (sans
  validation par réflexe). Les options A et B peuvent offrir la même chose.
- **Coût de la complexité** : plus d'appels LLM, debug difficile, observabilité
  exigeante, points de panne multiples.
- **Quand c'est justifié** : workflows réellement multi-étapes hétérogènes — **pas**
  une simple prédiction tabulaire.

## Exemple minimal qui tourne

```mermaid
flowchart TD
    IN[Demande] --> A1[Auditeur: valide + RGPD]
    A1 --> A2[Prédicteur] --> A3[Explainer]
    A3 --> A4{Superviseur: confiance ≥ seuil ?}
    A4 -->|oui| OUT[Décision tracée]
    A4 -->|non| HITL[Humain]
```

## Exercice guidé

Concevez (schéma + 3 lignes) l'option C pour MediVox :
1. Quels agents, quel rôle chacun ?
2. Quel état partagé circule ?
3. Quand le superviseur route-t-il vers un humain ?
4. **Question critique** : ce découpage se justifie-t-il pour prédire un séjour prolongé ?

## Pièges fréquents

| Piège | Conséquence |
|---|---|
| Des agents pour une tâche mono-étape | Sur-engineering caractérisé |
| Pas d'état partagé clair | Agents qui ne coopèrent pas vraiment |
| Oublier l'observabilité | Debug impossible en prod |
| Confondre « agent » et « fonction » | Complexité gratuite |
| Recommander C sans gain démontré | Signal négatif (jury / client) |

| Symptôme | Cause probable |
|---|---|
| Latence cumulée élevée | trop d'appels LLM en chaîne |
| Coût d'exploitation fort | orchestration + LLM partout |
| Conformité difficile à prouver | flux complexe peu traçable |

## Pour aller plus loin

- LangGraph : https://langchain-ai.github.io/langgraph/
- AI Act — supervision humaine : https://artificialintelligenceact.eu/the-act/

## Vérification (checklist apprenant)

- [ ] Je définis agent / orchestration / état partagé.
- [ ] Mon schéma C inclut un superviseur + HITL.
- [ ] Je sais nommer le coût de la complexité.
- [ ] Je juge **honnêtement** si des agents se justifient ici (souvent non).
- [ ] Je **conçois** sans implémenter.

> 💡 **Récap** : des **agents spécialisés** coopèrent via un **état partagé**
> (orchestration). Le routage vers un humain n'est pas un atout propre aux agents : la supervision (qui, autorité, délai, trace, pouvoir de contredire) se conçoit dans toute option. Coût : complexité, debug,
> observabilité, points de panne. Pour une prédiction tabulaire simple = souvent
> **sur-engineering** — à juger honnêtement.
