# Option B - Architecture hybride : extraction LLM puis modèle ML

```mermaid
flowchart LR
    classDef process fill:#e8f0fe,stroke:#4285f4,color:#1a1a1a
    classDef data fill:#fef7e0,stroke:#f9ab00,color:#1a1a1a
    classDef decision fill:#fce8e6,stroke:#ea4335,color:#1a1a1a
    classDef fallback fill:#fde9e9,stroke:#ea4335,stroke-dasharray: 4 3,color:#1a1a1a
    classDef monitor fill:#e6f4ea,stroke:#34a853,color:#1a1a1a

    CR[(Compte-rendu médical)]:::data --> EXT[LLM extracteur<br/>Schéma JSON imposé]:::process
    EXT --> VAL{Validation<br/>format + valeurs + preuve textuelle}:::decision
    VAL -->|valide| VAR[Variables textuelles<br/>structurées]:::data
    VAL -. invalide ou incertain .-> REV[File de relecture<br/>humaine habilitée]:::fallback
    REV -. validé .-> VAR
    REV -. non confirmé .-> NULL[Champ null / inconnu]:::data

    ADM[(Variables administratives<br/>conservées)]:::data --> FEAT[Jeu de variables<br/>existantes + extraites]:::process
    VAR --> FEAT
    NULL --> FEAT
    FEAT --> ML[Modèle ML<br/>classification séjour prolongé]:::process
    ML --> SCORE[Score de risque<br/>+ explication]:::process
    SCORE --> DEC{Seuil de confiance}:::decision
    DEC -->|confiant| OUT[Décision tracée]:::process
    DEC -. incertain .-> HITL[Revue humaine<br/>avec pouvoir de contredire]:::fallback

    AUDIT[(Logs et traçabilité<br/>source, version, score, décision)]:::data
    REV -. trace .-> AUDIT
    OUT -. trace .-> AUDIT
    HITL -. trace .-> AUDIT

    EXT -. mesure .-> MON[Qualité extraction<br/>taux de null et relecture]:::monitor
    ML -. mesure .-> MON

    subgraph CICD["Industrialisation — CI/CD"]
        direction LR
        HIST[(Historique annoté<br/>avec nouvelles variables)]:::data --> TRAIN[Réentraînement<br/>et validation comparative]:::process
        TRAIN --> REG[(Registre de modèles<br/>versions horodatées)]:::data
        REG -. déploie .-> ML
    end

    subgraph OBS["Observabilité continue"]
        direction LR
        ML -. mesure .-> FAIR[Suivi d'équité<br/>écart de performance]:::monitor
        ML -. mesure .-> DRIFT[Suivi de dérive<br/>features, calibration]:::monitor
        FAIR -. seuil dépassé .-> ALERT[Alerte + réentraînement]:::fallback
        DRIFT -. seuil dépassé .-> ALERT
    end

    INFRA[Déploiement redondant<br/>≥ 2 instances + sauvegarde]:::process -. remplace le SPOF .-> ML
```

**Principe** : le LLM n'effectue pas la prédiction. Il extrait, à partir des
 comptes-rendus, des variables structurées selon un schéma imposé. Ces variables
 sont validées puis ajoutées aux variables administratives avant l'inférence du
 modèle ML existant ou modernisé.

**Variables candidates** : autonomie à la sortie, complication mentionnée et
besoin de suivi après sortie. Chaque variable utilise une liste de valeurs
fermée et accepte `null` ou `inconnu` si l'information est absente ou non
confirmée par une phrase source.

**Validation et traçabilité** : la validation contrôle le format JSON, les types,
les valeurs autorisées et la présence d'une preuve textuelle. La version du
LLM, le compte-rendu source, la variable produite et le résultat de validation
sont conservés dans une trace d'audit. Les données de santé ne sont envoyées à
un fournisseur externe qu'après vérification du cadre RGPD, de la localisation
des traitements et des garanties contractuelles ; un modèle auto-hébergé peut
être étudié si cette contrainte est bloquante.

**Force** : l'architecture exploite une information textuelle absente du jeu de
données administratif tout en conservant un modèle ML mesurable, calibrable et
explicable pour la prédiction.

**Faiblesse** : elle ajoute le coût et la latence d'une extraction LLM, ainsi
qu'un risque d'erreur ou de valeur inventée. Le gain n'est pas présumé : il est
mesuré par ablation, avec le même modèle, le même jeu de test et les mêmes
métriques, avec puis sans les variables extraites.

**Fallback** : si l'extraction est invalide, incertaine ou sans preuve textuelle,
la variable devient `null` ou `inconnu` et le dossier est placé en relecture
par un professionnel habilité. La relecture doit pouvoir contredire le LLM,
être réalisée dans le délai défini par MediVox et laisser une trace. Si le score
ML est lui-même dans une zone d'incertitude, par exemple entre 0,40 et 0,70,
la prédiction s'abstient et est transmise à la revue humaine.

**RAG distinct** : un assistant RAG pourrait répondre aux questions des équipes
à partir des procédures ou des comptes-rendus, avec des sources citées. Ce
serait un produit documentaire séparé ; il ne remplace pas le pipeline
d'extraction puis de prédiction de cette option.