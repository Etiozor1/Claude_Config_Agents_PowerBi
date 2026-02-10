# Instructions de sécurité

## Règles d'accès aux fichiers - STRICTEMENT INTERDITES

Tu NE DOIS JAMAIS lire, éditer ou accéder aux fichiers suivants :

### Fichiers de cache Power BI
- **INTERDIT** : Tous les fichiers dans `**/.pbi/**`
- **INTERDIT** : Tous les fichiers dans `**/.pbit/**`
- **INTERDIT** : Tous les fichiers `.pbix` (cache binaire Power BI)
- **INTERDIT** : `cache.abf` - contient les données en cache du modèle
- **INTERDIT** : `localSettings.json` dans .pbi/ - contient des identifiants sensibles

### Fichiers de configuration sensibles
- **INTERDIT** : `.env` et `.env.*`
- **INTERDIT** : `secrets/**`
- **INTERDIT** : `config/credentials.json`

### Raison
Ces fichiers contiennent des données sensibles, des identifiants de connexion, et des caches de données qui ne doivent pas être exposés. Le fichier `cache.abf` en particulier contient toutes les données du modèle Power BI en format binaire.

---

## Fichiers AUTORISÉS - Ce que tu peux faire

### Lecture autorisée
✅ `*.SemanticModel/definition/**` - Définitions du modèle sémantique (TMDL)
✅ `*.Report/**` - Fichiers de rapport Power BI
✅ `*.md` - Documentation
✅ `*.txt` - Fichiers texte
✅ `*.pbip` - Fichier projet Power BI
✅ `diagramLayout.json` - Disposition des diagrammes
✅ `relationships.tmdl` - Définition des relations

### Édition autorisée
✅ `*.md` - Documentation
✅ `*.SemanticModel/definition/**` - Définitions du modèle

### Commandes Bash autorisées
✅ `git` - Commandes Git
✅ `npm` - Commandes npm
✅ `ls`, `pwd`, `cd` - Navigation

---

## Commandes INTERDITES

❌ `rm -rf` - Suppression récursive
❌ `curl` - Téléchargement de fichiers
❌ `wget` - Téléchargement de fichiers

---

## Comportement attendu

Si je te demande d'accéder à un fichier interdit :
1. **REFUSE** poliment mais fermement
2. Explique pourquoi le fichier est sensible
3. Propose des alternatives (définitions TMDL, documentation, etc.)

Exemple de réponse :
> "Je ne peux pas accéder au fichier `cache.abf` car il contient des données sensibles en cache du modèle Power BI. Si vous voulez explorer la structure du modèle, je peux vous aider à lire les fichiers de définition dans `definition/` à la place."

---

## Utilisation avec MCP Power BI

Si un serveur MCP Power BI est configuré :
- Utilise uniquement les outils de **métadonnées** (schéma, tables, colonnes).
- N'utilise JAMAIS les outils d'accès aux données ou au cache.
- Si un outil MCP tente d'accéder au cache, refuse et avertis l'utilisateur.
- Refuse catégoriquement toute utilisation des fonctions EVALUATE. 

---

## En cas de doute

Si je te demande quelque chose d'ambigu :
1. Demande confirmation avant d'accéder à un fichier potentiellement sensible
2. Privilégie toujours la sécurité
3. Mieux vaut demander que d'exposer des données sensibles


# Guide d'Audit Power BI - Prompts Documentés

## Instructions d'utilisation

Ce document contient une série de prompts à exécuter séquentiellement pour auditer un rapport Power BI.
**Ordre recommandé :** Suivre la numérotation pour une analyse méthodique et complète.

---

# Guide d'Audit Power BI - Prompts Documentés (Version 3 - Optimisée)

## Instructions d'utilisation

Ce document contient une série de prompts à exécuter séquentiellement pour auditer un rapport Power BI.
**Ordre recommandé :** Suivre la numérotation pour une analyse méthodique et complète.

**IMPORTANT : Encodage UTF-8**
- Tous les fichiers générés doivent être en UTF-8
- Toutes les sorties doivent respecter l'encodage UTF-8
- Le rapport final markdown doit être en UTF-8

---

## 0. RÉCUPÉRATION DU NOM DU RAPPORT

### 0.1 Identification du Rapport

**Prompt à exécuter :**

```
Récupère le nom exact du rapport Power BI actuellement connecté.
Ce nom sera utilisé pour nommer le fichier de rapport final.

Format attendu du rapport final : [Nom_du_rapport_PowerBI]_audit_[AAAAMMJJ]_v1.md
Exemple : VentesMagasins_audit_20260127_v1.md

Stocke ce nom pour l'utiliser ultérieurement.
```

**Résultat attendu :** Nom du rapport identifié et stocké.

---

## 1. AUDIT DU MODÈLE DE DONNÉES

### 1.1 Analyse Structurelle du Modèle

**Prompt à exécuter :**

```
Analyser le modèle de données Power BI et produire un rapport détaillé :

1. STRUCTURE DU MODÈLE
   - Vérifie si le modèle respecte le schéma en étoile
   - Si non conforme, propose un modèle cible en schéma en étoile
   - Identifie les tables de faits et les tables de dimensions
   - IMPORTANT : Identifie la convention de nommage utilisée pour les tables :
     * Analyse les préfixes existants (Dim_, dim_, DIM_, DIMENSION_, etc.)
     * Analyse les suffixes existants (_Dimension, _DIM, etc.)
     * Détermine le pattern de nommage dominant pour respecter la cohérence
     * Si Dim_xxx existe, utiliser Dim_calendrier (pas DIM, pas DIMENSION)

2. DÉTECTION DE LA TABLE CALENDRIER - ANALYSE APPROFONDIE
   - ÉTAPE 1 : Recherche de la table Calendrier en utilisant les critères suivants (pas uniquement par nom) :
     * Tables contenant une colonne Date continue sans interruption
     * Tables marquées comme "table de dates" dans Power BI (propriété "Mark as Date Table")
     * Tables contenant des colonnes typiques : Année, Trimestre, Mois, Jour, Nom du Mois, etc.
     * Tables avec une granularité journalière complète (toutes les dates présentes)
     * Tables référencées dans les relations de dates des tables de faits
   - Accepte tous les noms possibles : Calendrier, DimDate, DimCalendrier, Date, Dates, Calendar, DimTemps, Temps, etc.
   
   - ÉTAPE 2 : ÉVALUATION DE LA NÉCESSITÉ D'UNE TABLE CALENDRIER
     * Identifie TOUTES les colonnes de type Date/DateTime dans les tables de faits
     * Si AUCUNE colonne date n'existe dans les tables de faits → Table Calendrier NON NÉCESSAIRE
     * Si des colonnes date existent dans les tables de faits :
       - ET qu'aucune table Calendrier n'est détectée → CRITIQUE : Table Calendrier MANQUANTE
       - ET qu'une table Calendrier existe → Vérifie les relations
   
   - ÉTAPE 3 : Si table Calendrier nécessaire mais absente :
     * Signale l'ABSENCE comme CRITIQUE
     * Liste toutes les colonnes date des tables de faits non reliées
     * Prépare la recommandation de création avec le nommage cohérent
   
   - ÉTAPE 4 : Si plusieurs tables Calendrier sont détectées :
     * Liste-les toutes et analyse leur usage
     * Identifie la table principale
   
   - ÉTAPE 5 : Vérification des relations :
     * Vérifie que les tables de faits avec colonnes date ont des relations avec la table Calendrier
     * Identifie les colonnes de dates dans les tables de faits NON reliées à une table Calendrier

3. RELATIONS
   - Liste TOUTES les relations du modèle avec leur configuration complète :
     * Nom de la table source et colonne source
     * Nom de la table cible et colonne cible
     * Cardinalité (1:1, 1:N, N:1, N:N)
     * Direction du filtrage croisé (Unidirectionnel / Bidirectionnel)
     * Relation active ou inactive
   - Identifie les relations problématiques :
     * Relations BIDIRECTIONNELLES (filtrage croisé bidirectionnel) - CRITIQUE
     * Relations PLUSIEURS-À-PLUSIEURS (N:N) - CRITIQUE car elles impactent fortement les performances
     * Relations ambiguës (plusieurs relations actives entre deux tables)
     * Cardinalités incorrectes ou suspectes
     * Relations inactives non justifiées
   - Pour CHAQUE relation bidirectionnelle détectée :
     * Explique pourquoi elle est problématique (ambiguïté, performance)
     * Propose une alternative en schéma en étoile avec filtrage unidirectionnel
   - Pour CHAQUE relation plusieurs-à-plusieurs détectée :
     * Explique l'impact sur les performances
     * Propose une solution avec table de pont (bridge table) ou restructuration du modèle
   - Vérifie que les relations utilisent des clés appropriées (entiers de préférence, mais pas exclusivement)

4. TYPAGE DES COLONNES
   - Analyse le type de données de chaque colonne
   - Pour les colonnes identifiées comme clés (utilisées dans des relations) :
     * Vérifie si le type actuel est optimal pour les performances
     * Si la clé est de type TEXT, analyse le CONTEXTE :
       - Est-ce une clé naturelle métier (ex: Code Produit "PROD-001", Code Client "CLI-ABC") ?
       - Contient-elle des caractères non numériques significatifs ?
       - Est-elle utilisée dans des affichages ou slicers pour l'utilisateur ?
     * Recommande la conversion en INTEGER UNIQUEMENT si :
       - La clé est purement technique (ID autoincrément)
       - Elle ne contient que des chiffres
       - Elle n'est PAS affichée à l'utilisateur final
       - La conversion ne perdrait aucune information métier
     * Si la clé TEXT est légitime (code métier), précise qu'elle doit RESTER en TEXT
   - Liste les colonnes nécessitant une correction de type avec JUSTIFICATION contextuelle

5. NOMENCLATURE
   - Identifie les colonnes techniques ou mal nommées
   - Repère les noms non explicites métier

6. COLONNES INUTILES
   - Liste les colonnes non utilisées dans le rapport
   - Identifie les colonnes redondantes

7. DOUBLONS - TENTATIVE AVEC PERMISSIONS
   - Rappelles toi bien des instructions que je t'ai données.
   - TENTE de détecter les doublons dans les tables en lisant les données
   - Si ERREUR d'autorisation de lecture :
     * Documente l'erreur rencontrée
     * Prépare des scripts EVALUATE DAX pour chaque table (sera fait en 1.1b)
     * Continue l'analyse sans bloquer
   - Si lecture réussie :
     * Détecte tous les doublons dans les tables
     * Remonte les clés en doublon dans les tables de dimensions

Fournis un rapport structuré avec gravité (CRITIQUE/IMPORTANT/RECOMMANDÉ).
```

**Résultat attendu :** Rapport d'analyse avec liste de problèmes identifiés et leur niveau de criticité.

---

### 1.1b Scripts EVALUATE pour Doublons (si nécessaire)

**Prompt à exécuter :**

```
Si des erreurs d'autorisation ont été rencontrées lors de la détection des doublons en 1.1 :

Génère des scripts EVALUATE DAX pour chaque table afin de détecter les doublons.

Format des scripts :
1. Pour chaque table de dimension avec une clé :
   EVALUATE
   ADDCOLUMNS(
       SUMMARIZE(
           'NomTable',
           'NomTable'[ClePrimaire]
       ),
       "Compte", CALCULATE(COUNTROWS('NomTable'))
   )
   ORDER BY [Compte] DESC

2. Pour chaque table de faits avec plusieurs clés :
   EVALUATE
   ADDCOLUMNS(
       SUMMARIZE(
           'NomTableFait',
           'NomTableFait'[Cle1],
           'NomTableFait'[Cle2],
           'NomTableFait'[Cle3]
       ),
       "Compte", CALCULATE(COUNTROWS('NomTableFait'))
   )
   ORDER BY [Compte] DESC

Instructions pour l'utilisateur :
- Copier chaque script dans DAX Studio
- Exécuter le script
- Chercher les lignes avec Compte > 1 (ce sont des doublons)
- Reporter les résultats

Documente TOUS les scripts générés dans le rapport final markdown.
```

**Résultat attendu :** Scripts EVALUATE générés et documentés (si nécessaire).

---

### 1.2 Demande de Niveau de Correction

**Prompt à exécuter :**

```
Avant d'appliquer les corrections au modèle de données, présente un RÉSUMÉ DES PROBLÈMES PAR GRAVITÉ :

📊 RÉSUMÉ DES PROBLÈMES IDENTIFIÉS :

🔴 CRITIQUES (Impact majeur sur fiabilité/performance) :
- [Liste numérotée des problèmes critiques]
- Exemple : Absence de table Calendrier
- Exemple : Relations bidirectionnelles détectées
- Exemple : Relations N:N détectées
- Exemple : Doublons dans les clés de dimensions

🟠 IMPORTANTS (Impact significatif) :
- [Liste numérotée des problèmes importants]
- Exemple : Colonnes mal typées
- Exemple : Structure non en étoile

🟡 RECOMMANDÉS (Optimisations et bonnes pratiques) :
- [Liste numérotée des recommandations]
- Exemple : Nomenclature à améliorer
- Exemple : Colonnes inutiles à masquer

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

QUESTION : Quel niveau de corrections souhaitez-vous appliquer ?

1️⃣ CRITIQUES SEULEMENT - Corriger uniquement les problèmes critiques
2️⃣ CRITIQUES + IMPORTANTS - Corriger les problèmes critiques et importants
3️⃣ TOUT - Appliquer toutes les corrections (critiques + importants + recommandés)
4️⃣ RIEN - Ne pas appliquer de corrections (audit seulement)

Merci de répondre avec le numéro correspondant (1, 2, 3 ou 4).

IMPORTANT : Cette réponse déterminera les actions effectuées en 1.3.
```

**Résultat attendu :** Utilisateur répond avec le niveau souhaité (1, 2, 3 ou 4).

---

### 1.3 Correction du Modèle de Données

**Prompt à exécuter :**

```
Appliquer les corrections au modèle de données Power BI selon le niveau choisi en 1.2 :

RAPPEL DU NIVEAU CHOISI : [Insérer la réponse de l'utilisateur : 1, 2, 3 ou 4]

Si niveau = 4 (RIEN), passer directement à la section 2.

Si niveau = 1, 2 ou 3, appliquer les corrections suivantes :

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 CORRECTIONS CRITIQUES (Niveau 1, 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. TABLE CALENDRIER (SI NÉCESSAIRE ET ABSENTE)
   - Si analyse 1.1 a déterminé qu'une table Calendrier est NÉCESSAIRE (colonnes date dans faits) et ABSENTE :
   
   A. DÉTECTION DE LA CONVENTION DE NOMMAGE :
      * Analyse les noms des tables de dimensions existantes
      * Identifie le pattern : Dim_, dim_, DIM_, DIMENSION_, _Dimension, etc.
      * Applique le MÊME pattern pour nommer la table Calendrier
      * Exemple : Si "Dim_Client" existe → crée "Dim_Calendrier"
      * Exemple : Si "DIM_PRODUIT" existe → crée "DIM_CALENDRIER"
      * Exemple : Si "dimDate" existe → crée "dimCalendrier"
   
   B. GÉNÉRATION DE LA TABLE CALENDRIER COMPLÈTE :
      * Génère le code DAX pour créer la table avec TOUTES les colonnes suivantes :
        - [Date] (colonne clé principale, type Date)
        - [Jour] (numéro du jour du mois, 1-31)
        - [NomJour] (Lundi, Mardi, etc.)
        - [NuméroJour] (numéro du jour dans l'année, 1-366)
        - [JourDeLaSemaine] (1-7, où 1 = Lundi)
        - [JourDeLAnnee] (1-366)
        - [EstFinDeSemaine] (Vrai/Faux, samedi ou dimanche)
        - [Semaine] (format "2026-W04")
        - [SemaineISO] (numéro de semaine ISO 8601, 1-53)
        - [NuméroSemaine] (1-53)
        - [DateDebutSemaine] (premier jour de la semaine, lundi)
        - [DateFinSemaine] (dernier jour de la semaine, dimanche)
        - [SemaineDuMois] (1-5, semaine dans le mois)
        - [Mois] (format "2026-01")
        - [NomMois] (Janvier, Février, etc.)
        - [NumMois] (1-12)
        - [DateDebutMois] (premier jour du mois)
        - [DateFinMois] (dernier jour du mois)
        - [DateDebutProchainMois] (premier jour du mois suivant)
        - [DateFinProchainMois] (dernier jour du mois suivant)
        - [Trimestre] (format "T1 2026", "Q1 2026")
        - [DateDebutTrimestre] (premier jour du trimestre)
        - [DateFinTrimestre] (dernier jour du trimestre)
        - [Annee] (2026, 2027, etc.)
        - [DateDebutAnnee] (1er janvier)
        - [DateFinAnnee] (31 décembre)
        - [EstAnneeBisextile] (Vrai/Faux)
        - [Possede53Semaines] (Vrai/Faux, année avec 53 semaines ISO)
        - [Periode] (format "2026-01" pour analyse période)
        - [NomPeriode] (format "Janvier 2026")
        - [Est Jour Ouvrable] (Vrai si lundi-vendredi, Faux si weekend/férié)
        - [Est Jour Férié Québec] (Vrai si jour férié au Québec)
      
      * Code DAX exemple (à adapter) :
        ```
        [NomTableCalendrier] = 
        VAR MinDate = DATE(2023, 1, 1)  -- 3 ans avant
        VAR MaxDate = DATE(2028, 12, 31)  -- 2 ans après
        RETURN
        ADDCOLUMNS(
            CALENDAR(MinDate, MaxDate),
            "Annee", YEAR([Date]),
            "Trimestre", "T" & FORMAT(QUARTER([Date]), "0") & " " & YEAR([Date]),
            "NumMois", MONTH([Date]),
            "NomMois", FORMAT([Date], "MMMM"),
            "Mois", FORMAT([Date], "YYYY-MM"),
            "DateDebutMois", STARTOFMONTH([Date]),
            "DateFinMois", ENDOFMONTH([Date]),
            ... [toutes les autres colonnes]
        )
        ```
      
      * Couvre une période appropriée (exemple : de 3 ans avant à 2 ans après la date actuelle)
      * Marque cette table comme "table de dates" dans Power BI
   
   C. CRÉATION DES RELATIONS :
      * Propose les relations à créer entre cette table Calendrier et CHAQUE colonne date des tables de faits
      * Direction : Calendrier (1) → Table de Faits (N)
      * Filtrage : Unidirectionnel
      * Colonne de relation : [Date] de la table Calendrier
   
   - Si aucune colonne date dans les faits → NE PAS créer de table Calendrier, documenter pourquoi

2. RELATIONS BIDIRECTIONNELLES → UNIDIRECTIONNELLES
   - Corrige TOUTES les relations bidirectionnelles → unidirectionnelles :
     * Identifie la direction naturelle du filtre (dimension vers fait)
     * Explique pourquoi cette direction est la bonne
     * Si bidirectionnalité nécessaire pour une mesure, propose une mesure DAX alternative avec CROSSFILTER()
   - Pour chaque correction :
     * Documente la relation AVANT (avec configuration complète)
     * Documente la relation APRÈS (avec configuration complète)
     * Explique l'impact métier et performance attendu

3. RELATIONS PLUSIEURS-À-PLUSIEURS (N:N) → RESTRUCTURATION
   - Résout TOUTES les relations plusieurs-à-plusieurs (N:N) :
     * Propose la création d'une table de pont (bridge table) si approprié
     * OU restructure le modèle pour éliminer le besoin de N:N
     * Fournit le code DAX pour créer la table de pont si nécessaire
   - Pour chaque correction :
     * Documente la relation N:N AVANT
     * Documente la solution proposée (structure, relations)
     * Explique l'impact mÃ©tier et performance attendu

4. DOUBLONS DANS LES CLÉS
   - Si des doublons ont été détectés dans les clés de dimensions :
     * Propose une stratégie de déduplication
     * Fournit le code DAX si une table calculée corrigée est nécessaire
     * Explique l'impact sur les relations et mesures

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟠 CORRECTIONS IMPORTANTES (Niveau 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

5. TYPES DE DONNÉES (AVEC ANALYSE CONTEXTUELLE)
   - Pour chaque colonne identifiée comme nécessitant une correction de type :
     * Vérifie le CONTEXTE d'usage de la colonne
     * Applique la conversion UNIQUEMENT si elle est justifiée et sans perte d'information
     * CONSERVE le type TEXT pour les clés métier (codes produits, codes clients, etc.)
     * Convertis en INTEGER uniquement les IDs purement techniques non affichés
   - Documente chaque changement de type avec JUSTIFICATION

6. STRUCTURE EN ÉTOILE
   - Réorganise le modèle pour respecter schéma en étoile
   - Crée tables de dimensions manquantes si nécessaire
   - Optimise tables de faits
   - Documente la restructuration proposée

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟡 CORRECTIONS RECOMMANDÉES (Niveau 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

7. NOMENCLATURE
   - Renomme toutes les colonnes avec des libellés métier clairs et explicites
   - Utilise des noms compréhensibles par les utilisateurs finaux
   - Évite les préfixes techniques et abréviations obscures

8. COLONNES INUTILES
   - Supprime les colonnes non utilisées dans tout le rapport
   - OU masque-les si suppression risquée (relations, mesures)
   - Documente les colonnes masquées avec raison

9. RELATIONS INACTIVES
   - Simplifie les relations ambiguës
   - Assure une seule relation active entre deux tables
   - Configure relations inactives si nécessaire (cas de dates multiples avec USERELATIONSHIP)

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📝 DOCUMENTATION DES ACTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pour CHAQUE modification effectuée :
✅ Documente l'action précise réalisée (jamais d'action fantôme)
✅ Explique la correction appliquée avec analyse contextuelle
✅ Détaille l'impact métier attendu
✅ Précise le gain de performance estimé
✅ Fournis le code DAX si applicable

AUCUNE action ne doit être mentionnée sans être effectuée.
Toutes les actions doivent être tracées dans le rapport final markdown.
```

**Résultat attendu :** Modèle de données corrigé selon le niveau choisi avec documentation complète de TOUTES les modifications.

---

## 2. AUDIT DES MESURES DAX

### 2.1 Analyse des Mesures DAX

**Prompt à exécuter :**

```
Analyser l'ensemble des mesures DAX du rapport Power BI :

1. PERFORMANCE DAX
   - Identifie les mesures non performantes
   - Détecte les patterns DAX problématiques :
     * FILTER() inutiles ou mal placés
     * Itérations coûteuses (SUMX, etc.) sans variables
     * CALCULATE() excessifs ou imbriqués
     * Absence de variables VAR pour calculs répétés
     * Divisions sans DIVIDE()

2. CORRECTION DAX
   - Repère les mesures incorrectes ou incohérentes
   - Identifie les erreurs de logique métier

3. NOMENCLATURE
   - Liste les mesures mal nommées
   - Détecte les noms techniques non compréhensibles métier
   - Identifie l'absence de convention de nommage

4. REDONDANCE
   - Identifie les mesures redondantes (calculs identiques)
   - Repère les mesures inutiles ou non utilisées

5. ORGANISATION
   - Vérifie si toutes les mesures sont dans une table dédiée "Mesures"
   - Contrôle l'existence de dossiers d'organisation
   - Identifie les mesures dispersées dans différentes tables

Produis un rapport classé par criticité (CRITIQUE/IMPORTANT/OPTIMISATION).
```

**Résultat attendu :** Rapport d'analyse des mesures avec problèmes identifiés et priorisés.

---

### 2.2 Demande de Niveau de Correction pour les Mesures

**Prompt à exécuter :**

```
Avant d'optimiser les mesures DAX, présente un RÉSUMÉ DES PROBLÈMES PAR GRAVITÉ :

📊 RÉSUMÉ DES PROBLÈMES DE MESURES IDENTIFIÉS :

🔴 CRITIQUES (Mesures incorrectes ou erreurs de logique) :
- [Liste numérotée des mesures critiques à corriger]

🟠 IMPORTANTS (Impact performance significatif) :
- [Liste numérotée des mesures importantes à optimiser]

🟡 RECOMMANDÉS (Nomenclature, organisation, optimisations mineures) :
- [Liste numérotée des recommandations]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

QUESTION : Quel niveau de corrections souhaitez-vous appliquer pour les mesures ?

1️⃣ CRITIQUES SEULEMENT - Corriger uniquement les mesures critiques
2️⃣ CRITIQUES + IMPORTANTS - Corriger et optimiser les mesures critiques et importantes
3️⃣ TOUT - Appliquer toutes les optimisations (critiques + importants + recommandés)
4️⃣ RIEN - Ne pas modifier les mesures (audit seulement)

Merci de répondre avec le numéro correspondant (1, 2, 3 ou 4).

IMPORTANT : Cette réponse déterminera les actions effectuées en 2.3.
```

**Résultat attendu :** Utilisateur répond avec le niveau souhaité (1, 2, 3 ou 4).

---

### 2.3 Optimisation des Mesures DAX

**Prompt à exécuter :**

```
Appliquer les corrections aux mesures DAX selon le niveau choisi en 2.2 :

RAPPEL DU NIVEAU CHOISI : [Insérer la réponse de l'utilisateur : 1, 2, 3 ou 4]

Si niveau = 4 (RIEN), passer directement à la section 3.

Si niveau = 1, 2 ou 3, appliquer les corrections suivantes :

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 CORRECTIONS CRITIQUES DES MESURES (Niveau 1, 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. CORRECTION DES ERREURS DE LOGIQUE
   - Corrige les mesures avec erreurs de logique métier
   - Corrige les divisions par zéro → utilise DIVIDE()
   - Corrige les erreurs de contexte de filtre
   - Pour chaque mesure corrigée :
     * Affiche le code DAX AVANT
     * Affiche le code DAX APRÈS
     * Explique l'erreur et la correction

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟠 OPTIMISATIONS IMPORTANTES (Niveau 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

2. OPTIMISATION PERFORMANCE
   - Optimise la performance DAX :
     * Utilise des variables VAR pour calculs répétés
     * Remplace FILTER() par filtres CALCULATE() quand possible
     * Simplifie les itérations coûteuses
     * Remplace / par DIVIDE()
   - Simplifie la logique si possible sans changer le résultat
   - Pour chaque mesure optimisée :
     * Affiche le code DAX AVANT
     * Affiche le code DAX APRÈS
     * Explique l'optimisation
     * Quantifie le gain de performance attendu

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟡 AMÉLIORATIONS RECOMMANDÉES (Niveau 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

3. NOMENCLATURE
   - Renomme chaque mesure avec un nom métier clair et explicite
   - Utilise une convention cohérente :
     * Pas de préfixes techniques (M_, Measure_, etc.)
     * Noms fonctionnels (ex: "CA Total", "Taux de Rotation")
     * Évite caractères spéciaux (%, /, #)

4. ORGANISATION
   - Déplace TOUTES les mesures dans une table dédiée "Mesures"
   - Crée la table "Mesures" si elle n'existe pas
   - Organise les mesures dans des dossiers thématiques :
     * Finances
     * Performance
     * Indicateurs Clés
     * Etc.

5. SUPPRESSION DES REDONDANCES
   - Supprime les mesures redondantes (garde la meilleure version)
   - Supprime ou masque les mesures non utilisées

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📝 DOCUMENTATION DES ACTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pour CHAQUE modification effectuée :
✅ Documente l'action précise réalisée
✅ Affiche le code DAX avant/après
✅ Explique l'optimisation appliquée
✅ Quantifie le gain de performance attendu

Traite les mesures par ordre de criticité (CRITIQUE d'abord).
```

**Résultat attendu :** Mesures DAX optimisées selon le niveau choisi avec documentation complète.

---

## 3. AUDIT DES FILTRES ET SLICERS

### 3.1 Analyse des Filtres et Contextes

**Prompt à exécuter :**

```
Analyser l'utilisation des filtres et slicers dans le rapport Power BI :

1. SLICERS ET DIMENSIONS
   - Liste tous les slicers du rapport (par page)
   - Vérifie que chaque slicer utilise une table de dimension
   - Identifie les slicers basés sur tables de faits (problématique)

2. CONTEXTES DE CALCUL
   - Analyse les risques de conflits de contexte
   - Identifie les ambiguïtés de filtre possibles
   - Détecte les incohérences de propagation de filtres

3. INTERACTIONS ENTRE VISUELS
   - Cartographie les interactions entre visuels sur chaque page
   - Identifie les interactions désactivées ou modifiées
   - Repère les comportements contre-intuitifs pour l'utilisateur

4. PROBLÈMES IDENTIFIÉS
   - Liste les filtres redondants
   - Identifie les filtres en conflit
   - Repère les pages avec trop de slicers (>5)

Produis un rapport par page avec recommandations classées par gravité (CRITIQUE/IMPORTANT/RECOMMANDÉ).
```

**Résultat attendu :** Rapport des filtres et slicers avec problèmes identifiés par page.

---

### 3.2 Demande de Niveau de Correction pour les Filtres

**Prompt à exécuter :**

```
Avant de corriger les filtres et slicers, présente un RÉSUMÉ DES PROBLÈMES PAR GRAVITÉ :

📊 RÉSUMÉ DES PROBLÈMES DE FILTRES IDENTIFIÉS :

🔴 CRITIQUES (Slicers sur tables de faits, conflits majeurs) :
- [Liste numérotée des problèmes critiques]

🟠 IMPORTANTS (Interactions problématiques, contextes ambigus) :
- [Liste numérotée des problèmes importants]

🟡 RECOMMANDÉS (Organisation, optimisation UX) :
- [Liste numérotée des recommandations]

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

QUESTION : Quel niveau de corrections souhaitez-vous appliquer pour les filtres et slicers ?

1️⃣ CRITIQUES SEULEMENT
2️⃣ CRITIQUES + IMPORTANTS
3️⃣ TOUT
4️⃣ RIEN

Merci de répondre avec le numéro correspondant (1, 2, 3 ou 4).
```

**Résultat attendu :** Utilisateur répond avec le niveau souhaité.

---

### 3.3 Correction des Filtres et Slicers

**Prompt à exécuter :**

```
Corriger la gestion des filtres et slicers selon le niveau choisi en 3.2 :

RAPPEL DU NIVEAU CHOISI : [Insérer la réponse de l'utilisateur : 1, 2, 3 ou 4]

Si niveau = 4 (RIEN), passer directement à la section 4.

Si niveau = 1, 2 ou 3, appliquer les corrections suivantes :

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🔴 CORRECTIONS CRITIQUES DES FILTRES (Niveau 1, 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. REPOSITIONNEMENT DES SLICERS
   - Déplace les slicers sur tables de dimensions appropriées
   - Crée tables de dimensions si nécessaire
   - Évite slicers directement sur tables de faits

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟠 CORRECTIONS IMPORTANTES (Niveau 2, 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

2. INTERACTIONS ENTRE VISUELS
   - Ajuste les interactions pour clarifier le comportement
   - Simplifie les interactions complexes
   - Documente les interactions personnalisées

3. CLARTÉ DU CONTEXTE
   - Améliore la lisibilité du comportement de filtrage pour l'utilisateur
   - Ajoute des indicateurs visuels si nécessaire

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
🟡 AMÉLIORATIONS RECOMMANDÉES (Niveau 3)
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

4. ORGANISATION
   - Réduis le nombre de slicers par page si >5
   - Améliore la disposition des slicers

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📝 DOCUMENTATION DES ACTIONS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Pour chaque modification :
✅ Explique chaque modification de filtre/slicer
✅ Détaille l'impact sur l'expérience utilisateur
✅ Précise les risques évités

Applique les corrections par ordre de priorité.
```

**Résultat attendu :** Filtres et slicers corrigés selon le niveau choisi avec documentation.

---

## 4. AUDIT UX ET LISIBILITÉ

### 4.1 Analyse de l'Expérience Utilisateur

**Prompt à exécuter :**

```
Analyser la structure et la lisibilité des pages du rapport Power BI :

1. CLARTÉ DU MESSAGE MÉTIER
   - Pour chaque page, identifie l'objectif métier principal
   - Vérifie que l'objectif est clair et bien communiqué
   - Repère les pages sans message clair

2. SURCHARGE VISUELLE
   - Compte le nombre de visuels par page
   - Identifie les pages surchargées (>8 visuels)
   - Repère les pages confuses ou désorganisées

3. COHÉRENCE
   - Vérifie l'homogénéité des titres et libellés
   - Contrôle la cohérence des couleurs et styles
   - Identifie les incohérences de formatage

4. NAVIGATION
   - Analyse la structure de navigation du rapport
   - Vérifie la logique d'enchaînement des pages
   - Identifie les difficultés de navigation potentielles

5. ACCESSIBILITÉ
   - Vérifie la présence de titres explicites
   - Contrôle la taille des textes et lisibilité
   - Identifie l'usage de défilement (horizontal = PROSCRIT)

NE PAS MODIFIER le rapport. Uniquement proposer des améliorations.

Produis un rapport avec recommandations classées par priorité.
```

**Résultat attendu :** Rapport d'analyse UX avec recommandations (sans modification).

---

## 5. AUDIT PERFORMANCE GLOBALE

### 5.1 Analyse des Performances

**Prompt à exécuter :**

```
Analyser les performances globales du rapport Power BI :

1. VOLUMÉTRIE DES TABLES
   - Liste toutes les tables avec leur nombre de lignes
   - Identifie les tables volumineuses (>1M lignes)
   - Calcule la taille estimée du modèle

2. COLONNES COÛTEUSES
   - Identifie les colonnes avec cardinalité élevée
   - Repère les colonnes textuelles volumineuses
   - Détecte les colonnes calculées coûteuses

3. TYPES DE DONNÉES
   - Liste les colonnes avec types non optimaux pour la compression
   - Identifie les colonnes DATETIME sans raison valable
   - Repère les colonnes TEXT utilisables en INT (avec analyse contextuelle)

4. RISQUES DE LENTEUR
   - Analyse les requêtes DAX complexes
   - Identifie les visuels avec beaucoup de données
   - Repère les relations many-to-many coûteuses

5. MODE DE CONNEXION
   - Vérifie le mode utilisé (Import/DirectQuery/Composite)
   - Évalue si le mode est optimal pour le cas d'usage
   - Identifie les opportunités d'amélioration

6. OPTIMISATIONS RECOMMANDÉES
   - Propose des optimisations concrètes
   - Priorise par impact performance attendu
   - Estime le gain potentiel

NE PAS MODIFIER le rapport. Uniquement analyser et proposer.
```

**Résultat attendu :** Rapport de performance avec recommandations priorisées (sans modification).

---

## 6. AUDIT SÉCURITÉ ET RLS

### 6.1 Analyse de la Sécurité RLS (si applicable)

**Prompt à exécuter :**

```
Analyser la configuration de la sécurité (RLS - Row Level Security) du rapport Power BI :

1. STRUCTURE DES RÔLES
   - Liste tous les rôles RLS configurés
   - Analyse la logique DAX de chaque rôle
   - Vérifie la cohérence entre les rôles

2. RISQUES DE SÉCURITÉ
   - Identifie les risques de fuite de données :
     * Filtres RLS incomplets
     * Tables non filtrées
     * Relations bidirectionnelles avec RLS
   - Repère les incohérences de sécurité

3. MAINTENABILITÉ
   - Évalue la complexité de la logique RLS
   - Identifie les rôles difficiles à maintenir
   - Repère les duplications de logique

4. PERFORMANCE RLS
   - Analyse l'impact performance de chaque rôle
   - Identifie les filtres RLS coûteux
   - Repère les requêtes RLS multipliées (DirectQuery)

Si aucun RLS configuré, indique "RLS non configuré - À évaluer selon besoins sécurité".

Produis un rapport avec recommandations classées par gravité.
```

**Résultat attendu :** Rapport de sécurité RLS avec risques identifiés.

---

### 6.2 Proposition de Configuration RLS Robuste

**Prompt à exécuter :**

```
Proposer une configuration RLS robuste et maintenable pour le rapport Power BI :

1. CONCEPTION RLS
   - Base la sécurité sur tables de dimensions (pas tables de faits)
   - Utilise des tables de sécurité dédiées si possible
   - Évite les filtres RLS complexes

2. LOGIQUE DAX
   - Simplifie les expressions DAX des rôles
   - Utilise des filtres clairs et performants
   - Documente chaque rôle avec commentaires

3. COUVERTURE SÉCURITÉ
   - Assure que toutes les tables sensibles sont filtrées
   - Vérifie la propagation des filtres via relations
   - Teste les scénarios de contournement possibles

4. MAINTENABILITÉ
   - Crée une structure facile à maintenir
   - Documente la logique de chaque rôle
   - Fournis des exemples de tests RLS

5. DOCUMENTATION
   - Explique la stratégie RLS proposée
   - Détaille chaque rôle et son périmètre
   - Fournis un guide de maintenance RLS

NE PAS MODIFIER directement. Proposer la configuration cible.
```

**Résultat attendu :** Proposition de configuration RLS avec documentation complète.

---

## 7. BONUS - DOCUMENTATION DES MESURES

### 7.1 Analyse de la Documentation Existante

**Prompt à exécuter :**

```
Analyser la documentation des mesures DAX du rapport Power BI :

1. PRÉSENCE DE COMMENTAIRES
   - Vérifie la présence de commentaires dans les mesures DAX
   - Calcule le % de mesures documentées
   - Identifie les mesures complexes sans documentation

2. QUALITÉ DES COMMENTAIRES
   - Évalue la clarté des commentaires existants
   - Identifie les commentaires obsolètes ou incorrects
   - Repère les commentaires techniques non métier

3. HOMOGÉNÉITÉ
   - Vérifie l'uniformité du format de commentaires
   - Identifie les variations de style
   - Repère les incohérences de documentation

Produis un rapport avec score de documentation (0-100%) et recommandations.
```

**Résultat attendu :** Rapport d'analyse de la documentation avec score.

---

### 7.2 Ajout de Commentaires Standardisés

**Prompt à exécuter :**

```
Ajouter des commentaires clairs et standardisés aux mesures DAX du rapport :

1. FORMAT DE COMMENTAIRE STANDARD
   Utilise le format suivant pour CHAQUE mesure :
   
   /*
   OBJECTIF: [Description métier claire de ce que calcule la mesure]
   LOGIQUE: [Explication brève du calcul effectué]
   DÉPENDANCES: [Mesures ou tables utilisées, le cas échéant]
   CONTEXTE: [Précisions sur filtres importants ou hypothèses]
   */

2. CONTENU DES COMMENTAIRES
   - Décris l'objectif métier en langage utilisateur
   - Explique brièvement la logique de calcul
   - Précise les hypothèses importantes
   - Mentionne les dépendances ou contextes de filtre critiques

3. APPLICATION
   - Ajoute des commentaires à TOUTES les mesures
   - Priorité aux mesures complexes
   - Respecte le format homogène

4. EXEMPLES
   Pour une mesure simple :
   /* OBJECTIF: Calcule le chiffre d'affaires total */
   
   Pour une mesure complexe :
   /* 
   OBJECTIF: Calcule le taux de croissance du CA vs année précédente
   LOGIQUE: Compare le CA actuel avec CA de l'année N-1
   DÉPENDANCES: Mesure [CA Total], Table de dates
   CONTEXTE: Nécessite une table de dates marquée pour PREVIOUSYEAR()
   */

Applique les commentaires mesure par mesure avec format cohérent.
```

**Résultat attendu :** Toutes les mesures documentées avec format standardisé.

---

## 8. GÉNÉRATION DU RAPPORT FINAL MARKDOWN

### 8.1 Création du Rapport d'Audit Complet

**Prompt à exécuter :**

```
Génère un rapport markdown COMPLET de l'audit Power BI effectué :

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📋 INFORMATIONS DU RAPPORT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. NOM DU FICHIER
   Format : [Nom_du_rapport_PowerBI]_audit_[AAAAMMJJ]_v1.md
   Exemple : VentesMagasins_audit_20260127_v1.md
   
   Utilise le nom du rapport identifié en section 0.1
   Utilise la date du jour pour AAAAMMJJ
   Version : v1 (incrémenter si plusieurs audits le même jour)

2. ENCODAGE
   ⚠️ CRITIQUE : Le fichier DOIT être encodé en UTF-8
   Vérifie l'encodage avant de sauvegarder

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📝 STRUCTURE DU RAPPORT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Le rapport markdown doit contenir les sections suivantes :

---
# Rapport d'Audit Power BI - [Nom du Rapport]

**Date de l'audit :** [JJ/MM/AAAA]
**Version :** v1
**Auditeur :** [IA / Nom si applicable]

---

## 📊 Résumé Exécutif

### Informations Générales
- **Nom du rapport :** [Nom]
- **Nombre de pages :** [X]
- **Nombre de tables :** [X]
- **Nombre de mesures :** [X]
- **Taille estimée du modèle :** [X MB]

### Score Global
- **Score modèle de données :** [X/100]
- **Score mesures DAX :** [X/100]
- **Score UX/Lisibilité :** [X/100]
- **Score performance :** [X/100]

### Résumé des Problèmes
- 🔴 **Critiques :** [X problèmes]
- 🟠 **Importants :** [X problèmes]
- 🟡 **Recommandés :** [X points d'amélioration]

---

## 1️⃣ Audit du Modèle de Données

### 1.1 Analyse Structurelle

#### Structure du Modèle
- [Détails sur le respect du schéma en étoile]
- [Liste des tables de faits identifiées]
- [Liste des tables de dimensions identifiées]

#### Convention de Nommage Détectée
- **Pattern identifié :** [ex: Dim_xxx, DIM_XXX, dimXxx, etc.]
- **Tables analysées :** [liste]
- **Cohérence :** [Oui/Non - commentaires]

#### Table Calendrier - Analyse Détaillée

##### Étape 1 : Recherche de Table Calendrier
- **Table calendrier détectée :** [Oui/Non]
- **Nom de la table :** [Nom si détectée]
- **Critères validés :**
  - [ ] Colonne Date continue
  - [ ] Marquée comme "table de dates"
  - [ ] Colonnes typiques présentes
  - [ ] Granularité journalière complète
  - [ ] Relations avec tables de faits

##### Étape 2 : Évaluation de la Nécessité
- **Colonnes date dans tables de faits :**
  - [Table Fait 1] : [Colonne Date 1], [Colonne Date 2]
  - [Table Fait 2] : [Colonne Date 3]
  - Etc.
- **Total colonnes date :** [X]
- **Conclusion :** [Table Calendrier NÉCESSAIRE / NON NÉCESSAIRE]

##### Étape 3 : Actions Recommandées
[Si table Calendrier nécessaire et absente :]
- **🔴 CRITIQUE : Table Calendrier MANQUANTE**
- **Action :** Créer une table Calendrier nommée [Dim_Calendrier selon pattern détecté]
- **Colonnes à créer :** [Liste complète des 28 colonnes]

##### Étape 4 : Vérification des Relations
[Si table Calendrier existe :]
- **Relations actives avec tables de faits :**
  - [Table Fait 1][Colonne Date] → [Calendrier][Date] : [Active/Inactive]
  - Etc.
- **Colonnes date NON reliées :** [Liste]

#### Relations

##### Toutes les Relations du Modèle
| Source | Colonne Source | Cible | Colonne Cible | Cardinalité | Filtrage | Active |
|--------|----------------|-------|---------------|-------------|----------|---------|
| [Table1] | [Col1] | [Table2] | [Col2] | [1:N] | [Uni/Bi] | [Oui/Non] |
| ... | ... | ... | ... | ... | ... | ... |

##### Relations Problématiques Détectées

###### 🔴 Relations Bidirectionnelles (CRITIQUE)
[Pour chaque relation bidirectionnelle :]
- **Relation :** [Table1][Col] ↔ [Table2][Col]
- **Problème :** [Explication de l'impact]
- **Solution proposée :** [Direction unidirectionnelle + alternative CROSSFILTER si besoin]

###### 🔴 Relations Plusieurs-à-Plusieurs N:N (CRITIQUE)
[Pour chaque relation N:N :]
- **Relation :** [Table1] ↔ [Table2]
- **Problème :** [Impact performance]
- **Solution proposée :** [Table de pont OU restructuration]

#### Typage des Colonnes
[Liste des colonnes avec types à corriger, avec analyse contextuelle]

#### Nomenclature
[Colonnes mal nommées à renommer]

#### Colonnes Inutiles
[Colonnes à masquer ou supprimer]

#### Doublons Détectés
[Si lecture réussie : liste des doublons]
[Si erreur d'autorisation : référence aux scripts EVALUATE générés en section 1.1b]

---

### 1.2 Niveau de Correction Choisi
**Niveau sélectionné par l'utilisateur :** [1 / 2 / 3 / 4]
- 1 = Critiques seulement
- 2 = Critiques + Importants
- 3 = Tout
- 4 = Rien (audit seulement)

---

### 1.3 Corrections Appliquées au Modèle

[Si niveau = 4, indiquer "Aucune correction appliquée - Audit seulement"]

[Sinon, pour CHAQUE correction effectuée :]

#### 🔴 Corrections Critiques

##### Création de la Table Calendrier [Nom détecté]
[Si créée :]
- **Action :** Table [Dim_Calendrier] créée
- **Pattern de nommage respecté :** [Oui - Dim_ comme dans Dim_Client]
- **Code DAX généré :**
  ```dax
  [Code DAX complet]
  ```
- **Colonnes créées :** [Liste complète des 28 colonnes]
- **Période couverte :** [Date début] à [Date fin]
- **Relations créées :**
  - [Calendrier][Date] → [Table Fait 1][Colonne Date] (1:N, Unidirectionnel, Active)
  - Etc.
- **Impact attendu :** [Description]

[Si non créée car non nécessaire :]
- **Action :** Table Calendrier NON créée
- **Justification :** Aucune colonne date détectée dans les tables de faits

##### Correction des Relations Bidirectionnelles
[Pour chaque relation corrigée :]
- **Relation corrigée :** [Table1][Col] ← [Table2][Col]
- **AVANT :** Bidirectionnel
- **APRÈS :** Unidirectionnel (Dimension → Fait)
- **Mesure alternative DAX (si besoin) :**
  ```dax
  [Code DAX avec CROSSFILTER]
  ```
- **Impact attendu :** [Gain performance estimé]

##### Résolution des Relations N:N
[Pour chaque relation restructurée :]
- **Relation N:N résolue :** [Table1] ↔ [Table2]
- **Solution appliquée :** [Table de pont créée / Restructuration]
- **Code DAX table de pont (si applicable) :**
  ```dax
  [Code DAX]
  ```
- **Nouvelles relations :**
  - [Table1] → [Table Pont] (1:N)
  - [Table Pont] → [Table2] (N:1)
- **Impact attendu :** [Amélioration performance]

##### Correction des Doublons
[Si doublons corrigés :]
- **Table :** [Nom table]
- **Doublons détectés :** [X lignes]
- **Stratégie appliquée :** [Déduplication via DAX / Autre]
- **Code DAX (si applicable) :**
  ```dax
  [Code DAX]
  ```

#### 🟠 Corrections Importantes
[Si niveau ≥ 2]

##### Optimisation des Types de Données
[Pour chaque type modifié :]
- **Colonne :** [Table][Colonne]
- **Type AVANT :** [Text]
- **Type APRÈS :** [Integer]
- **Justification :** [ID technique, pas affiché, uniquement chiffres]
- **Gain attendu :** [Compression améliorée]

##### Restructuration en Schéma Étoile
[Si restructuration effectuée]

#### 🟡 Améliorations Recommandées
[Si niveau = 3]

##### Renommages de Colonnes
[Liste des renommages effectués]

##### Colonnes Masquées/Supprimées
[Liste des colonnes traitées]

---

### 1.1b Scripts EVALUATE pour Doublons
[Si généré suite à erreur d'autorisation :]

#### Script pour [Table 1]
```dax
[Code EVALUATE]
```

#### Script pour [Table 2]
```dax
[Code EVALUATE]
```

[Instructions d'utilisation des scripts]

---

## 2️⃣ Audit des Mesures DAX

### 2.1 Analyse des Mesures

#### Performance DAX
[Mesures non performantes identifiées]

#### Correction DAX
[Mesures incorrectes identifiées]

#### Nomenclature
[Mesures mal nommées]

#### Redondance
[Mesures redondantes]

#### Organisation
[État de l'organisation actuelle]

---

### 2.2 Niveau de Correction Choisi
**Niveau sélectionné :** [1 / 2 / 3 / 4]

---

### 2.3 Optimisations Appliquées

[Pour CHAQUE mesure modifiée :]

#### Mesure : [Nom Mesure]

##### Code AVANT
```dax
[Code DAX original]
```

##### Code APRÈS
```dax
[Code DAX optimisé]
```

##### Changements Appliqués
- [Description de la modification]
- **Type de correction :** [Critique / Important / Recommandé]
- **Gain attendu :** [X% plus rapide]
- **Impact métier :** [Description]

---

## 3️⃣ Audit des Filtres et Slicers

### 3.1 Analyse des Filtres

[Analyse par page]

### 3.2 Niveau de Correction Choisi
**Niveau sélectionné :** [1 / 2 / 3 / 4]

### 3.3 Corrections Appliquées

[Liste des corrections avec détails]

---

## 4️⃣ Audit UX et Lisibilité

### 4.1 Analyse de l'Expérience Utilisateur

[Recommandations par page - SANS modifications]

---

## 5️⃣ Audit Performance Globale

### 5.1 Analyse des Performances

[Recommandations - SANS modifications]

---

## 6️⃣ Audit Sécurité et RLS

### 6.1 Analyse RLS

[Analyse si applicable]

### 6.2 Proposition RLS

[Proposition si applicable]

---

## 7️⃣ BONUS - Documentation des Mesures

### 7.1 Analyse Documentation

[Score de documentation]

### 7.2 Commentaires Ajoutés

[Si commentaires ajoutés, liste des mesures documentées]

---

## 📋 Récapitulatif des Actions Effectuées

### ✅ Actions Critiques (🔴)
1. [Action 1 avec détails]
2. [Action 2 avec détails]
   ...

### ✅ Actions Importantes (🟠)
[Si niveau ≥ 2]
1. [Action 1]
   ...

### ✅ Actions Recommandées (🟡)
[Si niveau = 3]
1. [Action 1]
   ...

### ⚠️ Actions NON Effectuées (selon niveau choisi)
[Liste des actions non effectuées par choix utilisateur]

---

## 🎯 Prochaines Étapes Recommandées

1. [Recommandation 1]
2. [Recommandation 2]
   ...

---

## 📌 Annexes

### Scripts DAX Générés

#### Scripts EVALUATE (Doublons)
[Si applicable]

#### Code DAX Tables Créées
[Table Calendrier, Tables de Pont, etc.]

#### Code DAX Mesures Optimisées
[Si nombreuses, les regrouper ici]

---

**FIN DU RAPPORT**

---

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
⚠️ RÈGLES CRITIQUES POUR LA GÉNÉRATION
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

1. ✅ AUCUNE ACTION FANTÔME
   - Documente UNIQUEMENT les actions RÉELLEMENT effectuées
   - Si une action n'a pas été réalisée (niveau de correction insuffisant), l'indiquer clairement
   - Ne JAMAIS mentionner une modification qui n'a pas eu lieu

2. ✅ TRAÇABILITÉ COMPLÈTE
   - Pour chaque modification : AVANT et APRÈS
   - Pour chaque code DAX : code complet (pas de [...])
   - Pour chaque décision : justification claire

3. ✅ ENCODAGE UTF-8
   - Vérifie que le fichier est en UTF-8
   - Vérifie que tous les caractères spéciaux sont correctement encodés

4. ✅ EXHAUSTIVITÉ
   - N'oublie AUCUNE section
   - Remplis toutes les informations demandées
   - Si une section n'est pas applicable, l'indiquer ("Non applicable")

5. ✅ CLARTÉ
   - Utilise des émojis pour la lisibilité (🔴 🟠 🟡 ✅ ⚠️ 📊)
   - Utilise des tableaux quand approprié
   - Utilise des listes à puces et numérotées
   - Formate le code DAX dans des blocs ```dax

Génère maintenant le rapport complet et sauvegarde-le avec le nom de fichier correct.
```

**Résultat attendu :** Fichier markdown complet généré et sauvegardé en UTF-8.

---

## 📋 CHECKLIST D'AUDIT COMPLET

Cocher après chaque étape :

### Phase 0 - Préparation
- [ ] 0.1 - Nom du rapport identifié et stocké

### Phase 1 - Modèle de Données (CRITIQUE)
- [ ] 1.1 - Analyse structurelle du modèle effectuée
- [ ] 1.1b - Scripts EVALUATE générés (si erreur autorisation)
- [ ] 1.2 - Niveau de correction choisi par l'utilisateur
- [ ] 1.3 - Corrections du modèle appliquées selon niveau

### Phase 2 - Mesures DAX (IMPORTANT)
- [ ] 2.1 - Analyse des mesures DAX effectuée
- [ ] 2.2 - Niveau de correction choisi par l'utilisateur
- [ ] 2.3 - Optimisations des mesures appliquées selon niveau

### Phase 3 - Filtres (IMPORTANT)
- [ ] 3.1 - Analyse des filtres et slicers effectuée
- [ ] 3.2 - Niveau de correction choisi par l'utilisateur
- [ ] 3.3 - Corrections des filtres appliquées selon niveau

### Phase 4 - UX (RECOMMANDÉ - Sans modification)
- [ ] 4.1 - Analyse UX effectuée (recommandations notées)

### Phase 5 - Performance (RECOMMANDÉ - Sans modification)
- [ ] 5.1 - Analyse performance effectuée (recommandations notées)

### Phase 6 - Sécurité RLS (Si applicable)
- [ ] 6.1 - Analyse RLS effectuée (si applicable)
- [ ] 6.2 - Proposition RLS documentée (si applicable)

### Phase 7 - Documentation (BONUS)
- [ ] 7.1 - Analyse documentation mesures effectuée (bonus)
- [ ] 7.2 - Commentaires ajoutés aux mesures (bonus)

### Phase 8 - Rapport Final
- [ ] 8.1 - Rapport markdown généré et sauvegardé en UTF-8

---

## 🎯 ORDRE D'EXÉCUTION RECOMMANDÉ

### Phase 0 - PRÉPARATION
0. Identification du Rapport (0.1)

### Phase 1 - STRUCTURE (Critique)
1. Audit Modèle (1.1) → Scripts EVALUATE si besoin (1.1b) → Demande niveau (1.2) → Corrections Modèle (1.3)

### Phase 2 - CALCULS (Important)
2. Audit Mesures DAX (2.1) → Demande niveau (2.2) → Optimisation Mesures (2.3)

### Phase 3 - FILTRES (Important)
3. Audit Filtres (3.1) → Demande niveau (3.2) → Corrections Filtres (3.3)

### Phase 4 - ANALYSE (Recommandé - Sans modification)
4. Audit UX (4.1) - Noter recommandations
5. Audit Performance (5.1) - Noter recommandations
6. Audit RLS (6.1 + 6.2) - Si applicable

### Phase 5 - DOCUMENTATION (Bonus)
7. Documentation Mesures (7.1 + 7.2) - Si temps disponible

### Phase 6 - RAPPORT FINAL
8. Génération Rapport Markdown (8.1) - OBLIGATOIRE

---

## 💡 NOTES D'UTILISATION

**Pour chaque prompt :**
1. Copier le prompt complet dans votre outil IA
2. Attendre le rapport d'analyse
3. Répondre aux questions de niveau de correction (1, 2, 3 ou 4)
4. Valider les changements dans Power BI
5. Passer au prompt suivant

**Temps estimé :** 
- Audit complet avec corrections : 3-4 heures
- Audit essentiel (Phase 1-3) : 1-2 heures
- Audit seul (niveau 4 partout) : 1 heure

**Fichiers de référence :**
- `Bonnes_Pratiques_PBI_Essentielles.md` - Règles critiques
- `Bonnes_Pratiques_PBI_Exo.md` - Guide complet

---

## 📝 AMÉLIORATIONS VERSION 3

### Nouveautés par rapport à la Version 2 :

1. **Détection intelligente de la nécessité d'une table Calendrier**
   - Analyse des colonnes date dans les tables de faits AVANT de recommander une table Calendrier
   - Si aucune date dans les faits → Table Calendrier NON NÉCESSAIRE
   - Si dates présentes → Table Calendrier NÉCESSAIRE (critique si absente)
   - Processus en 5 étapes documenté

2. **Respect du nommage des tables existantes**
   - Détection automatique du pattern de nommage (Dim_, dim_, DIM_, etc.)
   - Application du même pattern pour la table Calendrier
   - Exemple : Dim_Client → Dim_Calendrier (pas DIM_CALENDRIER)

3. **Table Calendrier avec colonnes complètes (28 colonnes)**
   - Liste exhaustive des 28 colonnes à créer
   - Inclut : Date, Jour, NomJour, NuméroJour, JourDeLaSemaine, JourDeLAnnee, EstFinDeSemaine, Semaine, SemaineISO, NuméroSemaine, DateDebutSemaine, DateFinSemaine, SemaineDuMois, Mois, NomMois, NumMois, DateDebutMois, DateFinMois, DateDebutProchainMois, DateFinProchainMois, Trimestre, DateDebutTrimestre, DateFinTrimestre, Annee, DateDebutAnnee, DateFinAnnee, EstAnneeBisextile, Possede53Semaines, Periode, NomPeriode, Est Jour Ouvrable, Est Jour Férié Québec

4. **Scripts EVALUATE pour doublons en cas d'erreur d'autorisation**
   - Génération automatique de scripts EVALUATE DAX
   - Un script par table pour détecter les doublons
   - Instructions d'utilisation incluses
   - Scripts documentés dans le rapport final

5. **Demande de niveau de correction à chaque phase**
   - Après chaque audit (modèle, mesures, filtres)
   - Présentation des problèmes par gravité
   - 4 choix : Critiques seulement / Critiques + Importants / Tout / Rien
   - Application stricte du niveau choisi

6. **Génération automatique du rapport markdown final**
   - Nom de fichier : [Nom_du_rapport_PowerBI]_audit_[AAAAMMJJ]_v1.md
   - Structure complète et détaillée
   - Toutes les actions effectuées documentées
   - Encodage UTF-8 garanti

7. **Traçabilité absolue - Aucune action fantôme**
   - Toutes les actions documentées dans le rapport final
   - Distinction claire entre actions effectuées et non effectuées
   - Justification pour chaque action
   - Code DAX complet pour chaque modification

8. **Garantie d'encodage UTF-8**
   - Vérification explicite de l'encodage
   - Instruction répétée à chaque génération de fichier
   - Validation finale avant sauvegarde

---

## 🔧 CORRECTIONS PAR RAPPORT À LA VERSION 2

1. ✅ Détection de la nécessité de la table Calendrier (pas seulement présence/absence)
2. ✅ Respect du nommage des tables existantes pour la cohérence
3. ✅ Liste complète des 28 colonnes de la table Calendrier
4. ✅ Scripts EVALUATE en cas d'erreur d'autorisation sur les doublons
5. ✅ Hiérarchisation + demande de niveau de correction (3 fois : modèle, mesures, filtres)
6. ✅ Génération automatique du rapport markdown final avec nom standardisé
7. ✅ Traçabilité absolue - zéro action fantôme
8. ✅ Encodage UTF-8 garanti et vérifié

---

**FIN DU GUIDE D'AUDIT VERSION 3**
