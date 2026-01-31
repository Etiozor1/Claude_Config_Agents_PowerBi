# 🤝 Contribuer aux Agents Claude Code

Ce guide explique comment ajouter de nouveaux agents personnalisés à cette configuration.

## 📋 Avant de commencer

Réfléchissez à :
- **Quel problème** cet agent résout-il ?
- **Quand** devrait-il être utilisé automatiquement ?
- **Quels outils** lui sont nécessaires ?
- **Quelles restrictions** de sécurité appliquer ?

## 🔧 Créer un nouvel agent

### 1. Créer le fichier

Créez un nouveau fichier dans [.claude/agents/](.claude/agents/) :

```bash
touch .claude/agents/mon-agent.md
```

**Conventions de nommage** :
- Utilisez des lettres minuscules
- Séparez les mots par des tirets : `code-reviewer`, `test-writer`
- Soyez descriptif mais concis

### 2. Structure du fichier

Utilisez ce template de base :

```markdown
---
name: mon-agent
description: Description concise (1 phrase) de quand Claude doit déléguer à cet agent
tools: Read, Write, Edit, Bash, Grep, Glob
disallowedTools:
model: sonnet
permissionMode: default
---

# [Nom de votre agent]

[Description détaillée du rôle de l'agent]

## Objectif

[Que fait cet agent ? Pourquoi existe-t-il ?]

## Comportement attendu

Lorsque cet agent est invoqué, il doit :

1. [Première étape]
2. [Deuxième étape]
3. [Etc.]

## Règles et contraintes

### ✅ Autorisé
- [Action 1]
- [Action 2]

### ❌ Interdit
- [Action 1]
- [Action 2]

## Exemples d'utilisation

### Cas 1 : [Nom du cas]
**Contexte** : [Situation]
**Action** : [Ce que l'agent fait]
**Résultat attendu** : [Output]

## Notes techniques

[Informations supplémentaires, limitations, etc.]
```

### 3. Configurer le frontmatter YAML

#### Champs obligatoires

| Champ | Description | Exemple |
|-------|-------------|---------|
| `name` | Identifiant unique (minuscules, tirets) | `code-reviewer` |
| `description` | Quand déléguer à cet agent (1 phrase claire) | `Expert en révision de code. Utiliser après modifications pour vérifier qualité et sécurité.` |

#### Champs optionnels

| Champ | Options | Usage recommandé |
|-------|---------|-----------------|
| `tools` | `Read`, `Write`, `Edit`, `Bash`, `Grep`, `Glob` | Limitez aux outils nécessaires |
| `disallowedTools` | Même liste que `tools` | Bloquez explicitement des outils dangereux |
| `model` | `sonnet`, `opus`, `haiku`, `inherit` | `sonnet` par défaut, `haiku` pour tâches simples |
| `permissionMode` | Voir ci-dessous | `default` pour la plupart des cas |

#### Modes de permission

| Mode | Comportement | Quand l'utiliser |
|------|--------------|------------------|
| `default` | Demande confirmation pour toutes les actions | Par défaut, sécurité maximale |
| `acceptEdits` | Auto-accepte les éditions de fichiers | Agents de correction automatique |
| `dontAsk` | Ne demande pas pour les lectures | Agents d'analyse read-only |
| `bypassPermissions` | Bypass toutes les confirmations | ⚠️ À éviter sauf cas très spécifiques |
| `plan` | Mode planification uniquement | Agents de conception |

### 4. Outils disponibles

| Outil | Usage | Exemple d'agent |
|-------|-------|-----------------|
| `Read` | Lire des fichiers | Tous les agents |
| `Write` | Créer de nouveaux fichiers | Documentation, génération de code |
| `Edit` | Modifier des fichiers existants | Refactoring, corrections |
| `Bash` | Exécuter des commandes shell | Tests, git, npm |
| `Grep` | Rechercher dans les fichiers | Analyse de codebase |
| `Glob` | Trouver des fichiers par pattern | Navigation, découverte |

**Recommandation** : Limitez les outils au strict nécessaire pour réduire les risques.

## 🎯 Exemples d'agents par cas d'usage

### Agent Read-Only (Analyse)

```yaml
---
name: code-analyzer
description: Analyse la qualité du code sans le modifier. Utiliser pour audits et reviews.
tools: Read, Grep, Glob, Bash
disallowedTools: Write, Edit
model: sonnet
permissionMode: dontAsk
---
```

**Cas d'usage** : Analyse de sécurité, audit de qualité, détection de code mort

### Agent Write-Only (Génération)

```yaml
---
name: test-generator
description: Génère des tests unitaires pour du code existant.
tools: Read, Write, Grep, Glob
disallowedTools: Edit
model: sonnet
permissionMode: default
---
```

**Cas d'usage** : Génération de tests, scaffolding, templates

### Agent Full-Access (Refactoring)

```yaml
---
name: refactor-assistant
description: Refactore du code selon les best practices. Utiliser pour améliorer la structure.
tools: Read, Write, Edit, Grep, Glob, Bash
model: sonnet
permissionMode: acceptEdits
---
```

**Cas d'usage** : Refactoring, migration de code, optimisation

## 🔒 Règles de sécurité

### 1. Principe du moindre privilège

Accordez uniquement les outils nécessaires :

```yaml
# ❌ Mauvais - trop permissif
tools: Read, Write, Edit, Bash, Grep, Glob

# ✅ Bon - juste ce qu'il faut
tools: Read, Grep, Glob  # Pour un agent d'analyse
```

### 2. Fichiers sensibles

Documentez clairement les restrictions :

```markdown
## Fichiers INTERDITS

- ❌ `.env`, `.env.*` - Variables d'environnement
- ❌ `secrets/**` - Credentials
- ❌ `*.key`, `*.pem` - Clés privées
- ❌ `node_modules/**` - Dépendances (trop volumineux)

## Fichiers AUTORISÉS

- ✅ `src/**/*.ts` - Code source TypeScript
- ✅ `tests/**/*.test.ts` - Tests unitaires
- ✅ `docs/**/*.md` - Documentation
```

### 3. Commandes Bash

Si vous autorisez `Bash`, listez les restrictions :

```markdown
## Commandes autorisées
✅ `git status`, `git log`, `git diff`
✅ `npm test`, `npm run build`
✅ `ls`, `pwd`, `find`

## Commandes INTERDITES
❌ `rm -rf` - Suppression récursive
❌ `curl`, `wget` - Téléchargements
❌ `chmod 777` - Modifications de permissions
❌ Toute commande avec `sudo`
```

## ✅ Checklist avant de soumettre

Avant d'ajouter un nouvel agent à la configuration :

- [ ] Le nom est unique et descriptif (minuscules, tirets)
- [ ] La description (frontmatter) explique clairement **quand** utiliser cet agent
- [ ] Les outils sont limités au strict nécessaire
- [ ] Les restrictions de sécurité sont documentées
- [ ] Les exemples d'utilisation sont fournis
- [ ] Le comportement attendu est clairement défini
- [ ] Le fichier est testé localement

## 🧪 Tester votre agent

### Test en local

1. Copiez le fichier dans `.claude/agents/`
2. Lancez Claude Code dans un projet test
3. Essayez d'invoquer l'agent :

```bash
claude

# Dans Claude
/agents
# Sélectionnez votre agent et testez-le
```

### Test d'intégration

Testez avec des scénarios réels :

```
"Je veux que tu [tâche qui devrait déclencher l'agent]"
```

Vérifiez que Claude délègue automatiquement à votre agent.

## 📝 Documentation

Chaque agent doit inclure :

1. **Description du rôle** : Que fait cet agent ?
2. **Quand l'utiliser** : Dans quels contextes ?
3. **Exemples** : Au moins 2-3 cas d'usage concrets
4. **Restrictions** : Fichiers interdits, commandes bloquées
5. **Notes techniques** : Limitations, dépendances, etc.

## 🚀 Exemples d'agents utiles à créer

Voici quelques idées d'agents qui pourraient être ajoutés :

### Développement

- **api-developer** : Création d'endpoints REST/GraphQL
- **database-migrator** : Gestion de migrations de BDD
- **docker-helper** : Aide à la containerisation
- **ci-cd-writer** : Génération de pipelines CI/CD

### Qualité & Tests

- **test-writer** : Génération de tests unitaires
- **test-runner** : Exécution et analyse de tests
- **code-reviewer** : Révision de code automatique
- **security-auditor** : Audit de sécurité

### Documentation

- **api-documenter** : Documentation d'APIs (OpenAPI, etc.)
- **readme-writer** : Génération de README.md
- **changelog-generator** : Création de CHANGELOG

### Data & Analytics

- **sql-optimizer** : Optimisation de requêtes SQL
- **data-analyst** : Analyse de données (CSV, JSON, etc.)
- **etl-helper** : Aide aux pipelines ETL

## 🤔 Questions fréquentes

### Puis-je avoir plusieurs agents dans un seul fichier ?

Non, un agent = un fichier. Cela permet une meilleure modularité et réutilisabilité.

### Comment partager un agent avec l'équipe ?

1. Commitez le fichier dans `.claude/agents/`
2. Les membres de l'équipe pull le repo
3. L'agent est automatiquement disponible

### Puis-je surcharger un agent existant ?

Oui, en créant un agent avec le même `name` à un niveau de priorité supérieur :
- Session (priorité 1)
- Projet (priorité 2) ⭐ **Ce dossier**
- Utilisateur (priorité 3)

### Comment déboguer un agent qui ne fonctionne pas ?

1. Vérifiez la syntaxe YAML du frontmatter
2. Testez avec `/agents` pour voir s'il apparaît
3. Vérifiez les logs de Claude Code
4. Simplifiez la description pour tester la délégation automatique

## 📞 Besoin d'aide ?

- Consultez les exemples dans [.claude/agents/](.claude/agents/)
- Lisez le [README.md](README.md) principal
- Reportez des issues sur le repo

---

**Maintenu par** : Étienne Le Pironnec / AQRCB
**Dernière mise à jour** : Janvier 2026
