# Option C — Multi-agents pour supervision du prédicteur

```mermaid
flowchart LR
    classDef process fill:#e8f0fe,stroke:#4285f4,color:#1a1a1a
    classDef data fill:#fef7e0,stroke:#f9ab00,color:#1a1a1a
    classDef decision fill:#fce8e6,stroke:#ea4335,color:#1a1a1a
    classDef fallback fill:#fde9e9,stroke:#ea4335,stroke-dasharray: 4 3,color:#1a1a1a
    classDef monitor fill:#e6f4ea,stroke:#34a853,color:#1a1a1a

    IN[Nouvelle admission]:::process --> AUDIT[Agent auditeur<br/>valide les données et les droits]:::process
    AUDIT --> CHECK{Données valides<br/>et complètes ?}:::decision
    CHECK -->|non| ERRLOG[Rejet + log erreur]:::fallback
    CHECK -->|oui| STATE[(État partagé<br/>données validées, versions, traces)]:::data
    STATE --> PRED[Agent prédicteur<br/>appelle le modèle ML versionné]:::process
    PRED --> EXPLAIN[Agent explicateur<br/>score, facteurs, limites]:::process
    EXPLAIN --> SUP[Agent superviseur<br/>applique les règles de routage]:::process
    SUP --> DEC{Cas certain et<br/>cohérent ?}:::decision
    DEC -->|oui| OUT[Signal de risque tracé<br/>mis à disposition du soignant]:::process
    DEC -->|non| HITL[Revue humaine tracée<br/>infirmier·e référent·e / médecin<br/>peut contredire le système]:::fallback
    HITL --> OUT
    OUT --> LOGS[(Logs et traçabilité<br/>input, score, explication,<br/>versions, décision, qui, quand)]:::data

    subgraph CICD["Industrialisation — CI/CD"]
        direction LR
        REPO[(Dépôt versionné<br/>+ secrets externalisés)]:::data --> TRAIN[Pipeline d'entraînement<br/>split train/test, tests d'équité]:::process
        TRAIN --> REG[(Registre de modèles<br/>versions horodatées)]:::data
        REG -.déploie.-> PRED
    end

    subgraph OBS["Observabilité continue"]
        direction LR
        PRED -.mesure.-> FAIR[Suivi d'équité<br/>disparate impact, écart de FNR]:::monitor
        PRED -.mesure.-> DRIFT[Suivi de dérive<br/>features, calibration, latence]:::monitor
        FAIR -.seuil dépassé.-> RETRAIN[Demande de réentraînement<br/>revue humaine obligatoire]:::fallback
        DRIFT -.seuil dépassé.-> RETRAIN
        RETRAIN -.après validation.-> TRAIN
    end

    INFRA[Déploiement redondant<br/>≥ 2 instances + sauvegarde automatisée]:::process -.remplace le SPOF.-> PRED
```

**Principe** : des agents spécialisés coordonnent la validation, la prédiction,
l'explication et le routage. Le modèle ML versionné reste responsable du score ;
le système fournit un signal au soignant, pas une décision médicale autonome.

**Force** : les responsabilités et le contexte partagé sont explicités ; les cas
incertains, incohérents ou dégradés sont orientés vers une décision humaine
traçable.

**Faiblesse** : orchestration, tests, observabilité et débogage plus complexes ;
un gain sur une prédiction tabulaire reste à démontrer face à l'option A.

**Fallback** : l'agent superviseur transmet à un professionnel habilité tout cas
incertain, incohérent ou en erreur. Celui-ci peut modifier ou annuler le signal,
dans un délai défini et avec une justification tracée. Une dérive ou un écart
d'équité déclenche une demande de réentraînement, jamais un redéploiement automatique.
