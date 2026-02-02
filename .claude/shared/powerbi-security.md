# Instructions de sécurité Power BI

## Règles d'accès aux fichiers - STRICTEMENT INTERDITES

Tu NE DOIS JAMAIS lire, éditer ou accéder aux fichiers suivants :

### Fichiers de cache Power BI
- **INTERDIT** : Tous les fichiers dans `**/.pbi/**`
- **INTERDIT** : Tous les fichiers dans `**/.pbit/**`
- **INTERDIT** : Tous les fichiers `.pbix` (cache binaire Power BI)
- **INTERDIT** : `cache.abf` - contient les données en cache du modèle
- **INTERDIT** : `localSettings.json` dans .pbi/ - contient des identifiants sensibles
- **INTERDIT** : `expressions.tmdl` dans .pbi/ - contient des identifiants sensibles sur les connexions

### Fichiers de configuration sensibles
- **INTERDIT** : `.env` et `.env.*`
- **INTERDIT** : `secrets/**`
- **INTERDIT** : `config/credentials.json`

### Requêtes de base de données - STRICTEMENT INTERDIT
- **INTERDIT** : `expressions.tmdl` - contient les expressions M/Power Query avec les requêtes BD
- **INTERDIT** : `**/tables/**/partitions/*.tmdl` - contient les requêtes SQL/M des partitions
- **INTERDIT** : `*.m` - fichiers Power Query M
- **INTERDIT** : Toute section `partition` dans les fichiers TMDL de tables
- **INTERDIT** : Lecture ou analyse des propriétés `source` ou `expression` contenant des requêtes SQL/M

### Raison
Ces fichiers contiennent des données sensibles, des identifiants de connexion, et des caches de données qui ne doivent pas être exposés. Le fichier `cache.abf` en particulier contient toutes les données du modèle Power BI en format binaire.

Les requêtes de base de données (SQL, M/Power Query) sont également confidentielles car elles peuvent révéler :
- La structure interne des bases de données sources
- Les noms de serveurs, bases de données et schémas
- La logique métier des transformations de données
- Des informations potentiellement exploitables pour des attaques

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
