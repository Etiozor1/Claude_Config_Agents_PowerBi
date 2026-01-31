# 🤖 Configuration Claude Code - Agents Personnalisés

Ce dossier contient la configuration de vos agents Claude Code personnalisés pour l'organisation.

## 📁 Structure du dossier

```
_ClaudeConfig/
├── .claude/
│   └── agents/
│       └── powerbi-documentation.md    # Agent de documentation Power BI
└── README.md                            # Ce fichier
```

## 🎯 Agents disponibles

### 1. **powerbi-documentation**

Agent spécialisé dans la création de documentation pour les modèles sémantiques Power BI.

**Utilisation** :
- Documentation automatique de projets Power BI (.pbip)
- Respect strict des règles de sécurité (pas d'accès aux caches, credentials, etc.)
- Génération de documentation concise suivant le template standard (270-350 lignes)

**Quand l'utiliser** :
- Lorsque vous avez un modèle Power BI à documenter
- Pour mettre à jour la documentation existante
- Pour analyser la structure d'un modèle sémantique

**Restrictions** :
- ❌ Ne peut pas accéder aux fichiers `.pbi/`, `.pbix`, `cache.abf`
- ❌ Ne peut pas lire les credentials ou secrets
- ✅ Peut lire les définitions TMDL (`*.SemanticModel/definition/**`)
- ✅ Peut créer/éditer la documentation markdown

### 2. **powerbi-audit**

Agent expert en audit complet de rapports Power BI avec correction guidée.

**Utilisation** :
- Audit complet du modèle de données (structure, relations, types, doublons)
- Optimisation des mesures DAX (performance, corrections, organisation)
- Analyse des filtres, slicers et contextes de calcul
- Audit UX, performance globale et sécurité RLS
- Génération d'un rapport markdown détaillé en UTF-8

**Quand l'utiliser** :
- Pour auditer un rapport Power BI existant
- Avant mise en production d'un nouveau rapport
- Pour identifier et corriger les problèmes de performance
- Pour valider les bonnes pratiques Power BI

**Fonctionnement** :
- Processus guidé en 8 phases séquentielles
- Choix du niveau de correction (Critiques / Critiques+Importants / Tout / Audit seul)
- Documentation complète des actions effectuées
- Génération de scripts DAX pour corrections

**Restrictions** :
- ❌ Mêmes restrictions de sécurité que powerbi-documentation
- ✅ Peut lire/analyser les définitions TMDL
- ✅ Génère un rapport markdown complet avec toutes les actions tracées

## 🚀 Installation

Vous avez deux options pour utiliser ces agents :

### Option 1 : Niveau Projet (recommandé pour partage en équipe)

Cette configuration est déjà prête ! Si vous copiez le dossier `.claude/` dans votre projet Power BI, l'agent sera disponible automatiquement.

```bash
# Depuis ce dossier
cp -r .claude/ /chemin/vers/votre/projet/powerbi/

# L'agent sera maintenant disponible dans ce projet
cd /chemin/vers/votre/projet/powerbi/
claude
```

### Option 2 : Niveau Utilisateur (disponible partout)

Pour rendre l'agent disponible dans tous vos projets :

**Windows** :
```bash
# Copier l'agent dans votre configuration utilisateur
cp .claude/agents/powerbi-documentation.md ~/.claude/agents/
```

**Linux/Mac** :
```bash
cp .claude/agents/powerbi-documentation.md ~/.claude/agents/
```

## 💡 Comment utiliser les agents

### Méthode 1 : Délégation automatique

Claude délègue automatiquement aux agents appropriés selon leur description. Demandez simplement :

```
"Peux-tu documenter ce modèle Power BI ?"
```

Claude détectera que c'est une tâche de documentation Power BI et utilisera l'agent `powerbi-documentation`.

### Méthode 2 : Invocation via le menu agents

```bash
# Dans Claude Code
/agents
```

Puis sélectionnez l'agent "powerbi-documentation" dans la liste.

### Méthode 3 : Utilisation programmatique

Dans vos workflows ou scripts :

```bash
claude --agents '{
  "powerbi-documentation": {
    "description": "Expert en documentation Power BI",
    "prompt": "...",
    "tools": ["Read", "Write", "Edit", "Grep", "Glob", "Bash"]
  }
}'
```

## 📝 Exemples d'utilisation

### Documenter un modèle Power BI complet

```
Utilisateur : "J'ai un projet Power BI dans ./MonProjet.SemanticModel/.
              Peux-tu créer la documentation complète ?"

Claude : [Délègue à powerbi-documentation]
         "Je vais analyser votre modèle sémantique Power BI et créer
         la documentation. Je vais lire les fichiers de définition TMDL..."
```

### Mettre à jour la documentation existante

```
Utilisateur : "La documentation dans docs/MODEL.md est obsolète.
              Peux-tu la mettre à jour ?"

Claude : [Utilise powerbi-documentation]
         "Je vais analyser les changements dans le modèle et mettre
         à jour la documentation..."
```

### Analyser la structure d'un modèle

```
Utilisateur : "Quelles sont les tables de faits dans ce modèle ?"

Claude : [Peut déléguer à powerbi-documentation]
         "Je vais examiner les définitions du modèle sémantique..."
```

## 🔒 Sécurité

L'agent `powerbi-documentation` est configuré avec des restrictions strictes :

### ❌ Fichiers INTERDITS
- `**/.pbi/**` - Cache Power BI
- `**/.pbix` - Fichiers binaires Power BI
- `cache.abf` - Données en cache
- `localSettings.json` - Identifiants
- `.env`, `secrets/**` - Credentials

### ✅ Fichiers AUTORISÉS
- `*.SemanticModel/definition/**` - Définitions TMDL
- `*.Report/**` - Rapports Power BI
- `*.md` - Documentation
- `*.pbip` - Fichiers projet
- `diagramLayout.json`, `relationships.tmdl`

### Commandes Bash autorisées
- ✅ `git`, `npm`, `ls`, `pwd`, `cd`
- ❌ `rm -rf`, `curl`, `wget`

## 🛠️ Créer vos propres agents

Pour ajouter un nouvel agent :

1. Créez un fichier `.claude/agents/mon-agent.md`

2. Ajoutez le frontmatter YAML :

```markdown
---
name: mon-agent
description: Description de quand utiliser cet agent
tools: Read, Write, Edit, Bash, Grep, Glob
model: sonnet
permissionMode: default
---

# Instructions de votre agent

[Vos instructions détaillées ici...]
```

3. L'agent sera automatiquement disponible dans Claude Code

### Exemples d'autres agents utiles

- **code-reviewer** : Révision de code automatique
- **test-writer** : Génération de tests unitaires
- **security-auditor** : Audit de sécurité
- **api-developer** : Développement d'APIs
- **data-analyst** : Analyse de données SQL/Python

## 📚 Ressources

- [Documentation officielle Claude Code](https://docs.anthropic.com/claude/docs/claude-code)
- [Guide des agents personnalisés](https://docs.anthropic.com/claude/docs/custom-agents)
- [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk)

## 🔄 Portée et priorités

Claude Code gère 4 niveaux de portée pour les agents :

1. **Session CLI** (priorité 1) - Flags `--agents` lors du lancement
2. **Projet** (priorité 2) - `.claude/agents/` dans votre projet ⭐ **C'est ce dossier**
3. **Utilisateur** (priorité 3) - `~/.claude/agents/` dans votre profil
4. **Plugin** (priorité 4) - Agents fournis par des plugins

Un agent au niveau **Session** écrasera un agent du même nom au niveau **Projet**, qui écrasera celui au niveau **Utilisateur**, etc.

## ⚙️ Configuration avancée

### Hooks de cycle de vie

Vous pouvez ajouter des hooks dans le frontmatter pour valider les commandes :

```yaml
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "./scripts/validate-command.sh"
```

### Restrictions d'outils

Limiter les outils disponibles :

```yaml
tools: Read, Grep, Glob          # Uniquement lecture
disallowedTools: Write, Edit      # Pas de modifications
```

### Modes de permission

```yaml
permissionMode: default           # Demande confirmation
permissionMode: acceptEdits       # Auto-accepte les éditions
permissionMode: dontAsk           # Ne demande pas pour les lectures
permissionMode: bypassPermissions # Bypass complet (attention !)
permissionMode: plan              # Mode planification uniquement
```

## 🤝 Partage avec l'équipe

Pour partager cette configuration avec votre équipe :

1. **Commitez le dossier `.claude/` dans Git** :

```bash
git add .claude/
git commit -m "Ajout de la configuration Claude Code avec agent Power BI"
git push
```

2. **Les membres de l'équipe clonent/pull le repo** et les agents sont automatiquement disponibles.

3. **Standardisez la documentation** : Tous utilisent les mêmes règles et templates.

## 📞 Support

Pour des questions ou suggestions sur cette configuration :

1. Consultez `/help` dans Claude Code
2. Reportez des issues sur [GitHub](https://github.com/anthropics/claude-code/issues)
3. Contactez l'administrateur de cette configuration

---

**Version** : 1.0
**Dernière mise à jour** : Janvier 2026
**Auteur** : Étienne Le Pironnec / AQRCB
