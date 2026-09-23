# Option A — ML classique modernisé (industrialisation du prédicteur existant)
 
```mermaid
flowchart LR
    classDef process fill:#e8f0fe,stroke:#4285f4,color:#1a1a1a
    classDef data fill:#fef7e0,stroke:#f9ab00,color:#1a1a1a
    classDef decision fill:#fce8e6,stroke:#ea4335,color:#1a1a1a
    classDef fallback fill:#fde9e9,stroke:#ea4335,stroke-dasharray: 4 3,color:#1a1a1a
    classDef monitor fill:#e6f4ea,stroke:#34a853,color:#1a1a1a
 
    IN[Nouvelle admission]:::process --> VAL{Validation des<br/>données d'entrée}:::decision
    VAL -->|invalide| ERRLOG[Rejet + log erreur]:::fallback
    VAL -->|valide| FEAT[Pipeline de features<br/>âge, comorbidités, IMC<br/>variable sexe : à justifier ou retirer]:::process
    FEAT --> MODEL[Modèle ML versionné<br/>ex. HistGradientBoosting<br/>service persistant, chargé une fois]:::process
    MODEL --> SCORE[Score de probabilité]:::process
    SCORE --> SEUIL{Zone d'incertitude ?<br/>0.4 ≤ p < 0.7}:::decision
    SEUIL -->|oui| HUM[Revue humaine tracée<br/>infirmier·e référent·e / médecin]:::fallback
    SEUIL -->|non| AUTO[Décision automatique tracée]:::process
    HUM --> LOGS
    AUTO --> LOGS[(Logs et traçabilité<br/>input, score, décision, qui, quand)]:::data
 
    subgraph CICD["Industrialisation — CI/CD"]
        direction LR
        REPO[(Dépôt versionné<br/>+ secrets externalisés)]:::data --> TRAIN[Pipeline d'entraînement<br/>split train/test, random_state documenté]:::process
        TRAIN --> REG[(Registre de modèles<br/>versions horodatées)]:::data
        REG -.déploie.-> MODEL
    end
 
    subgraph OBS["Observabilité continue"]
        direction LR
        MODEL -.mesure.-> FAIR[Suivi d'équité<br/>disparate impact, écart de FNR par sexe]:::monitor
        MODEL -.mesure.-> DRIFT[Suivi de dérive<br/>distribution des features, calibration]:::monitor
        FAIR -.seuil dépassé.-> ALERT[Alerte + réentraînement déclenché]:::fallback
        DRIFT -.seuil dépassé.-> ALERT
    end
 
    INFRA[Déploiement redondant<br/>≥ 2 instances + sauvegarde automatisée]:::process -.remplace le SPOF.-> MODEL
```
 
## Légende de convention (à reprendre à l'identique pour les schémas B et C)
 
- **rectangle plein** = étape de traitement
- **losange** = point de décision
- **cylindre** = stockage de données
- **trait plein** = flux principal
- **trait pointillé** = chemin de fallback, supervision ou monitoring
- **couleur bleue** = traitement métier
- **couleur jaune** = stockage
- **couleur rouge** = décision / fallback
- **couleur verte** = observabilité / monitoring
> À valider en binôme : si vous adoptez cette légende pour B et C, les 3 schémas seront directement comparables (exigence du livrable 2 : « mêmes formes et couleurs, mêmes niveaux de détail »).
 
## Principe
 
Reprendre l'architecture ML existante (RandomForest ou équivalent scikit-learn), l'industrialiser (CI/CD, service persistant, logs, redondance, secrets externalisés) et combler les manques que l'audit M7-B1 a identifiés — sans introduire de brique LLM. Le texte des comptes-rendus n'est pas exploité dans cette option.
 
## Force
 
Coût et complexité minimaux ; explicabilité native (feature importance, SHAP possible) ; conformité facilitée par une traçabilité complète et l'absence de traitement de données non structurées supplémentaires.