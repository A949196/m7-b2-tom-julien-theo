# Estimer les coûts LLM — Mini-cours

> Brief associé : M7-B2
> Durée de lecture : ~20 min
> Pré-requis : notion de token, d'API

## Pourquoi cette techno ?

La sobriété ne se décrète pas, elle se **chiffre**. Comparer une option LLM à un
RandomForest sans estimer les **€/mois** et **€/inférence** rend l'arbitrage
creux. Vous n'avez pas besoin d'un budget au centime — des **ordres de grandeur
honnêtes** à partir de tarifs publics suffisent pour trancher.

## Concepts clés

- **Tokens** : les LLM facturent par token (≈ ¾ d'un mot). Coût = tokens_in ×
  prix_in + tokens_out × prix_out. **L'output coûte souvent plus cher** que l'input.
- **API commerciale vs self-host** : une API (OpenAI/Anthropic/Mistral) = pas
  d'infra mais coût récurrent par appel ; self-host (Mistral 7B sur GPU) = coût
  fixe GPU mais maîtrise des données. En pratique, le self-host = louer un GPU
  chez un hyperscaler (étage IaaS) et l'API = leur étage managé — repères dans
  `cheatsheet_cloud_hyperscalers.md` (`ia-atos-ressources`).
- **Unité de coût** : une extraction se paie **par document** (une fois par
  compte-rendu, en batch), une prédiction ML **par requête**, un RAG **par
  question** (+ embeddings à l'indexation et à chaque requête). Ne pas
  mélanger les unités.
- **Exemple de calcul** (pas un repère à mémoriser) : sur l'hypothèse de volume
  ci-dessous, un RandomForest revient à **~50 €/mois** (compute négligeable) et
  une option LLM en API à **~300–800 €/mois**. Le rapport entre les deux
  **dépend entièrement du volume, de la taille des documents, du modèle et de
  l'unité de coût retenue** — il se recalcule à chaque cas, il ne se retient pas.
- **Coût caché** : latence (UX), maintenance de la stack, dépendance fournisseur.
- **Honnêteté** : « ~500 €/mois », pas « 487,32 €/mois ». Et préciser les hypothèses
  (volume d'inférences/jour).

## Exemple minimal qui tourne

```text
Hypothèse : 5 000 séjours/jour, 1 compte-rendu par séjour.
- Option A (RandomForest) : compute ~négligeable → ~50 €/mois (hébergement).
- Option B (extraction LLM en API, ~2 000 tokens in + 150 out JSON / CR) :
  5 000 × 30 × (2 000 × prix_in + 150 × prix_out) ≈ quelques centaines d'€/mois,
  + le coût A (le modèle ML tourne toujours) + relecture humaine des cas incertains.
→ Ordre de grandeur : B ≈ A + un ordre de grandeur, **avant** relecture humaine.
```

## Exercice guidé

1. Pose une hypothèse de volume (inférences/jour) pour MediVox.
2. Estime le €/mois de l'option A et de l'option B (tarifs publics), en choisissant la bonne **unité** (requête ou document).
3. Conclus en **ordre de grandeur**, en rappelant l'hypothèse de volume qui
   porte le résultat (« à 5 000 séjours/jour, B ≈ 10× A »), + 1 risque de coût
   caché.

## Pièges fréquents

| Piège | Conséquence |
|---|---|
| Oublier le coût d'output | Sous-estime fortement le coût LLM |
| Budget au centime | Faux précis, non crédible |
| Ignorer les embeddings (RAG) ou la relecture humaine (extraction) | Coût incomplet |
| Comparer sans hypothèse de volume | Chiffres incomparables |
| Oublier les coûts cachés (latence, maintenance) | Arbitrage biaisé |

| Symptôme | Cause probable |
|---|---|
| Estimation contestée | hypothèses de volume non explicites |
| LLM « pas si cher » | output + embeddings oubliés |

## Pour aller plus loin

- OpenAI pricing : https://openai.com/api/pricing/
- Mistral pricing : https://mistral.ai/products/la-plateforme

## Vérification (checklist apprenant)

- [ ] Je pose une hypothèse de volume explicite.
- [ ] Je compte input **et** output (et embeddings si RAG, relecture si extraction).
- [ ] Je donne des **ordres de grandeur** (pas un faux précis).
- [ ] Je compare A et B en €/mois.
- [ ] Je cite ≥ 1 coût caché.

> 💡 **Récap** : chiffrer en **ordre de grandeur** sur une hypothèse de volume —
> compter input **et** output (+ embeddings si RAG). Ne pas oublier les coûts
> cachés (latence, maintenance, dépendance fournisseur). Faux précis
> (« 487,32 € ») = non crédible.

*Réflexe : toujours expliciter l'hypothèse de volume (inférences/jour) — sans elle, deux estimations ne sont pas comparables.*

*Ce qui se retient n'est pas un ratio, c'est la méthode : choisir la bonne **unité de coût** (par requête, par document, par question), poser l'hypothèse de volume, puis calculer. Un écart LLM/ML mesuré sur un cas ne se transpose pas à un autre — et c'est justement pour ça qu'on prouve le gain avant d'y aller.*

*(Et n'oubliez pas : le coût d'output d'un LLM dépasse souvent le coût d'input — ne le sous-estimez pas dans l'estimation.)*
