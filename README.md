# M7-B2 — Comparer 3 évolutions architecturales (MediVox)

> **Repo template.** Binôme ou trinôme (groupes annoncés au lancement) ; chacun rédige l'option opposée à son affinité. « Use this template » →
> `M7-B2-medivox-evolutions-<binome>`. **Pas de code** — conception et arbitrage.
> Restitution orale **mardi M8** (15 min, sur vos schémas — pas de slides).

## 🧭 Ce que vous produisez

| # | À faire | Fichier |
|---|---|---|
| 1 | 3 schémas Mermaid (convention cohérente) | `schemas/option_{a,b,c}_TEMPLATE.md` |
| 2 | Comparatif 3×4 chiffré | `comparatif_TEMPLATE.md` |
| 3 | Note 3 pages maximum + **recommandation UNE** + décision en une phrase | `note_comparaison_TEMPLATE.md` |

Renommez les `*_TEMPLATE` en versions finales.

## ✅ Réussite (rappel)

- 3 schémas **comparables** (mêmes formes/couleurs).
- Comparatif 3×4 **chiffré** (ordres de grandeur honnêtes, pas « cher »).
- **Fallback strategies** explicitées par option (seuil / abstention / HITL).
- **UNE** recommandation tranchée, argumentée chiffrée.
- **Garde-fou sobriété explicite** : justifiez le choix (ou non) d'une approche
  LLM. *« 5 agents pour prédire un séjour prolongé »* = signal négatif.
- **Option B = hybride** : le LLM extrait des variables des comptes-rendus, le
  modèle ML prédit. Un assistant RAG qui *répond* aux équipes est un autre
  produit — à mentionner à part, pas à comparer au prédicteur.
- **Avant de rendre** : répétition duo chronométrée (15 min) et une ligne
  « décision en une phrase » en tête de note.
- **Journal de bord** tenu.

## 📚 Ressources

Voir [`./ressources/`](./ressources/) — 6 mini-cours (dont fine-tuning ⭐) + `liens_officiels.md`.
