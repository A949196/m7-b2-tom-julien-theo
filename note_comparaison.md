# Note de comparaison — Évolution du prédicteur de séjour prolongé (À COMPLÉTER)

> 3 pages maximum. **Décision en une phrase** (à figer au freeze) : Moderniser d'abord l'option A pour obtenir une baseline fiable et conforme ; ne basculer vers l'option B que si un protocole d'ablation prouve un gain de F1 significatif sur les variables extraites des comptes-rendus ; écarter l'option C, qui n'apporte aucun gain de score démontré pour un coût, une latence et une complexité supérieurs.

## Synthèse (3 min)

Le prédicteur actuel cumule des risques techniques sérieux (SPOF, absence de logs, mot de passe en clair, aucune séparation train/test) et un risque éthique mesuré et actif (sous-détection des séjours prolongés chez les femmes, 3× plus fréquente que chez les hommes). Aucune des 3 architectures étudiées ne corrige ce biais par elle-même : c'est un problème de données et d'usage de variable, pas d'architecture. Les 3 options se distinguent en revanche nettement sur la sobriété, la latence et la complexité d'exploitation, pour un gain de performance qui reste à prouver côté B et qui est nul par construction côté C.

## 3 options détaillées (1 page + schéma chacune)

### Option A — ML classique modernisé
 
**Principe.** Conserver l'approche actuelle (modèle scikit-learn sur variables tabulaires : âge, comorbidités, IMC) et l'industrialiser : service persistant, CI/CD avec split train/test documenté et registre de versions, logs et traçabilité complète des décisions, redondance de déploiement, secrets externalisés. Le texte des comptes-rendus n'est pas exploité.
 
**Ce que ça règle.** La quasi-totalité des risques techniques de l'audit : absence de traçabilité, mot de passe en clair, SPOF, absence de validation d'entrée, absence de déploiement automatisé, code dupliqué, et l'absence de séparation train/test qui rendait jusqu'ici toute mesure de performance non fiable.
 
**Ce que ça ne règle pas seul.** Le biais de sous-détection par sexe : l'architecture ne peut que l'instrumenter (monitoring d'équité, seuils d'alerte, retrait ou justification de la variable), pas le corriger structurellement.
 
**Fallback.** Seuil de rejet sur la zone d'incertitude (0.4 ≤ p < 0.7) → revue humaine par un·e professionnel·le de santé habilité·e, décision sous 4h ouvrées, tracée avec motif dans le dossier patient informatisé, pouvoir de contredire intégralement le score.
 
### Option B — Hybride : extraction LLM → ML
 
**Principe.** Le LLM n'effectue pas la prédiction : il extrait, selon un schéma JSON imposé, des variables structurées à partir des comptes-rendus (autonomie à la sortie, complication mentionnée, besoin de suivi après sortie). Ces variables, après validation, enrichissent le même modèle ML (identique à A ou une version modernisée) qui reste seul responsable de la prédiction.
 
**Ce que ça ajoute face à A.** Une information potentiellement absente du jeu administratif — mais ce gain n'est jamais présumé : il doit être démontré par un protocole d'ablation (même modèle, même jeu de test, mêmes métriques, avec et sans les variables extraites). Cette option ajoute aussi un coût, une latence d'extraction (asynchrone, à l'arrivée du document) et un risque de valeur inventée par le LLM.
 
**Ce que ça ne règle pas seul.** Comme A, elle n'élimine pas le biais de sexe existant dans les données — le modèle prédictif final reste soumis aux mêmes limites.
 
**Point de vigilance conformité propre à B.** Les données de santé ne peuvent être envoyées à un fournisseur LLM externe qu'après vérification du cadre RGPD, de la localisation des traitements et des garanties contractuelles ; un modèle auto-hébergé est à étudier si cette contrainte s'avère bloquante.
 
**Fallback.** Extraction invalide, incertaine ou sans preuve textuelle → champ `null`/`inconnu` + relecture par un professionnel habilité, avec pouvoir de contredire le LLM, dans un délai défini par MediVox, tracée. Si le score ML lui-même est dans la zone d'incertitude (0.40–0.70), la prédiction s'abstient et rejoint la même revue humaine qu'en A.
 
**Produit distinct — assistant RAG.** Un assistant documentaire RAG répondant aux questions des équipes à partir des procédures ou comptes-rendus est un produit séparé (sortie = texte pour un humain, pas un score) : il ne se compare pas au prédicteur et ne remplace pas le pipeline d'extraction.
 
### Option C — Multi-agents
 
**Principe.** Des agents spécialisés (auditeur, prédicteur, explicateur, superviseur, surveillance) coordonnent la validation, la prédiction, l'explication et le routage autour du même modèle ML versionné, qui reste seul responsable du score.
 
**Arbitrage agent par agent** L'agent **auditeur** est déconseillé : un validateur déterministe, comme en A, est plus sobre, plus fiable et plus auditable pour des règles stables. L'agent **prédicteur** est déconseillé : appeler le même modèle via un agent ne le rend pas meilleur, et ajoute un point de panne. L'agent **explicateur** a un intérêt modéré (adapter la restitution au soignant), à condition d'ancrer l'explication dans une méthode déterministe (SHAP) plutôt que de la laisser générer librement. Les agents **superviseur** et **surveillance** ont un intérêt faible tant que les règles de routage et les seuils de monitoring restent stables et peu nombreux — ce qui est le cas ici.
 
**Conséquence.** Une version de C fidèle à cet arbitrage se réduit, dans les faits, à A ou B **plus** une couche d'orchestration et 2 à 4 agents LLM (explicateur, superviseur, surveillance), pour un score de prédiction strictement identique, une latence dégradée (plusieurs secondes contre quelques millisecondes) et un coût de complexité (observabilité par agent, points de panne multiples, debug) que le €/mois seul ne reflète pas.
 
**Fallback.** Détaillé par composant : rejet et demande de correction si l'agent auditeur détecte des données invalides ; bascule vers une instance redondante ou file de revue humaine sans score si l'agent prédicteur ou le modèle est indisponible ; affichage « explication indisponible » puis revue humaine si l'agent explicateur produit une justification incohérente ou non ancrée ; désactivation du routage automatique et transmission systématique à la revue humaine si l'agent superviseur est en défaut ou en désaccord de règles ; alerte d'exploitation et contrôle manuel temporaire si l'agent de surveillance est indisponible.

## Comparatif (cf. comparatif.md)

Cf. `comparatif.md` (tableau 3 options × 4 dimensions + hypothèses de chiffrage H1–H7).

## Fallback strategies (seuils / abstention / HITL — en conception)

Les 3 options partagent le même socle : un seuil de rejet sur le score ML (0.40–0.70) qui déclenche systématiquement une revue humaine habilitée, avec pouvoir de contredire le système, délai défini et trace conservée. B ajoute une strate de relecture pour les extractions incertaines. C reproduit le même socle mais le fait transiter par un agent superviseur, sans en changer la nature.

## Recommandation
 
**Option recommandée : A**, avec bascule conditionnelle vers B.
 
1. **Aucun gain n'est encore démontré pour B ou C.** Le F1 actuel n'est pas mesurable de façon fiable (absence de split train/test dans le legacy) ; tant que cette baseline n'existe pas, il est impossible de chiffrer un quelconque gain de B, et C n'en apporte structurellement aucun (même modèle appelé par un agent).
2. **A règle la quasi-totalité des risques techniques et une partie des risques de conformité de l'audit**, pour un coût de ~80–150 €/mois, sans dépendance à un fournisseur LLM ni risque d'extraction inventée.
3. **C est un signal de sur-engineering pour ce cas d'usage** : prédiction tabulaire simple, sans besoin d'orchestration multi-étapes hétérogène — son propre arbitrage agent par agent déconseille 2 des 5 agents proposés et ne trouve d'intérêt modéré ou faible qu'aux 3 autres.
**Condition de changement d'avis.** Basculer vers B si, une fois la baseline A mesurée sur un split propre, un protocole d'ablation démontre un gain de F1 significatif apporté par les variables extraites des comptes-rendus — et si le cadre RGPD de transmission à un fournisseur LLM (ou l'alternative auto-hébergée) est validé par Marc.
 
> **Garde-fou sobriété — règle opérationnelle** : aucun appel LLM n'est déclenché si l'information nécessaire à la prédiction est déjà disponible dans les variables structurées existantes ; un appel LLM (extraction ou agent) n'est introduit que sur un champ texte non couvert, et seulement si un protocole d'ablation démontre un gain de F1 significatif sur le même jeu de test.
 
## Plan de migration (3 étapes maximum)
 
| Étape | Changement | Risque | Critère de passage à la suivante |
|---|---|---|---|
| 1 — Existant → A | Industrialiser le prédicteur actuel : split train/test, CI/CD, logs/traçabilité, redondance, secrets externalisés, monitoring d'équité et de dérive, retrait ou justification de la variable sexe. | Le retrait de la variable sexe peut faire varier la performance mesurée ; à évaluer sur le split propre avant décision finale. | Baseline F1 mesurée de façon fiable sur un jeu de test isolé, monitoring d'équité en place, traçabilité complète opérationnelle. |
| 2 — A → B (conditionnelle) | Ajouter le pipeline d'extraction LLM sur les comptes-rendus (schéma JSON, validation, relecture humaine), en gardant le modèle ML de l'étape 1. | Risque d'extraction invalidée en volume plus élevé que prévu, faisant exploser la charge de relecture humaine. | Protocole d'ablation démontrant un gain de F1 significatif ET cadre RGPD de transmission des données validé par Marc. |
| 3 — Cible | B en production, avec le socle de fallback (null/relecture + seuil de rejet ML) et le monitoring d'équité hérité de l'étape 1. | Dérive de version du LLM extracteur non détectée. | Taux d'erreur d'extraction mesuré sur échantillon de contrôle stable dans le temps, sous le seuil fixé avec Hélène. |
 
> L'option C n'entre pas dans ce plan de migration : aucune étape ne la rend pertinente pour ce cas d'usage tant que le besoin reste une prédiction tabulaire séquentielle.
 