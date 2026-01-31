# 📑 Index de la configuration Claude Code

Ce fichier répertorie tous les fichiers de cette configuration et leur rôle.

## 📂 Structure complète

```
_ClaudeConfig/
├── .claude/                           # Dossier de configuration Claude Code
│   └── agents/                        # Agents personnalisés
│       ├── code-reviewer.md          # ✅ Agent de révision de code
│       ├── powerbi-documentation.md  # ✅ Agent de documentation Power BI
│       └── TEMPLATE.md               # 📝 Template pour créer nouveaux agents
│
├── .gitignore                         # Fichiers à ignorer par Git
├── CONTRIBUTING.md                    # Guide pour ajouter de nouveaux agents
├── INDEX.md                           # ⭐ Ce fichier - Index de la configuration
└── README.md                          # Documentation principale
```

## 📄 Description des fichiers

### 🔧 Fichiers de configuration

| Fichier | Rôle | Statut |
|---------|------|--------|
| [.gitignore](.gitignore) | Ignore fichiers temporaires et cache | ✅ Prêt |
| [README.md](README.md) | Documentation principale, guide d'utilisation | ✅ Prêt |
| [CONTRIBUTING.md](CONTRIBUTING.md) | Guide pour créer de nouveaux agents | ✅ Prêt |
| [INDEX.md](INDEX.md) | Index de tous les fichiers | ✅ Prêt |

### 🤖 Agents disponibles

| Agent | Fichier | Description | Statut |
|-------|---------|-------------|--------|
| **powerbi-documentation** | [.claude/agents/powerbi-documentation.md](.claude/agents/powerbi-documentation.md) | Documentation de modèles Power BI avec règles de sécurité strictes | ✅ Prêt |
| **powerbi-audit** | [.claude/agents/powerbi-audit.md](.claude/agents/powerbi-audit.md) | Audit complet Power BI (modèle, DAX, filtres, performance, RLS) | ✅ Prêt |
| **code-reviewer** | [.claude/agents/code-reviewer.md](.claude/agents/code-reviewer.md) | Révision de code (qualité, sécurité, best practices) | ✅ Prêt |
| **TEMPLATE** | [.claude/agents/TEMPLATE.md](.claude/agents/TEMPLATE.md) | Template pour créer de nouveaux agents | 📝 Template |

## 🎯 Démarrage rapide

### 1. Utiliser cette configuration dans un projet

**Copier dans un projet existant** :
```bash
cp -r .claude/ /chemin/vers/votre/projet/
```

**Utiliser au niveau utilisateur (tous vos projets)** :
```bash
cp .claude/agents/*.md ~/.claude/agents/
```

### 2. Invoquer un agent

**Méthode automatique** (recommandée) :
```
"Peux-tu documenter ce modèle Power BI ?"
→ Claude délègue automatiquement à powerbi-documentation
```

**Méthode manuelle** :
```bash
# Dans Claude Code
/agents
# Puis sélectionner l'agent désiré
```

### 3. Créer un nouvel agent

```bash
# 1. Copier le template
cp .claude/agents/TEMPLATE.md .claude/agents/mon-agent.md

# 2. Éditer le fichier et remplir les sections

# 3. L'agent est automatiquement disponible
```

Consultez [CONTRIBUTING.md](CONTRIBUTING.md) pour les détails.

## 📊 Statistiques de la configuration

| Métrique | Valeur |
|----------|--------|
| **Agents actifs** | 3 |
| **Templates disponibles** | 1 |
| **Fichiers de documentation** | 4 |
| **Dernière mise à jour** | Janvier 2026 |

## 🔍 Trouver rapidement

### Je veux...

| Objectif | Fichier à consulter |
|----------|---------------------|
| **Comprendre cette configuration** | [README.md](README.md) |
| **Documenter un modèle Power BI** | Utiliser l'agent `powerbi-documentation` |
| **Réviser du code** | Utiliser l'agent `code-reviewer` |
| **Créer un nouvel agent** | Lire [CONTRIBUTING.md](CONTRIBUTING.md) et copier [TEMPLATE.md](.claude/agents/TEMPLATE.md) |
| **Partager avec mon équipe** | Commiter `.claude/` dans Git (voir [README.md](README.md#-partage-avec-lquipe)) |
| **Installer au niveau utilisateur** | Copier `.claude/agents/*.md` vers `~/.claude/agents/` |

## 🔗 Liens utiles

### Documentation

- **Guide d'utilisation** : [README.md](README.md)
- **Guide de contribution** : [CONTRIBUTING.md](CONTRIBUTING.md)
- **Template d'agent** : [.claude/agents/TEMPLATE.md](.claude/agents/TEMPLATE.md)

### Agents

- **Power BI Documentation** : [.claude/agents/powerbi-documentation.md](.claude/agents/powerbi-documentation.md)
- **Code Reviewer** : [.claude/agents/code-reviewer.md](.claude/agents/code-reviewer.md)

### Ressources externes

- [Documentation Claude Code](https://docs.anthropic.com/claude/docs/claude-code)
- [Guide des agents personnalisés](https://docs.anthropic.com/claude/docs/custom-agents)
- [Claude Agent SDK](https://github.com/anthropics/claude-agent-sdk)
- [GitHub Issues](https://github.com/anthropics/claude-code/issues)

## 📝 Détails des agents

### Agent : powerbi-documentation

**Fichier** : [.claude/agents/powerbi-documentation.md](.claude/agents/powerbi-documentation.md)

**Quand l'utiliser** :
- Documentation de modèles sémantiques Power BI (.pbip)
- Mise à jour de documentation existante
- Analyse de structure de modèle

**Outils** : Read, Write, Edit, Bash, Grep, Glob

**Restrictions** :
- ❌ Fichiers cache (.pbi, .pbix, cache.abf)
- ❌ Credentials (localSettings.json, .env)
- ✅ Définitions TMDL (*.SemanticModel/definition/)
- ✅ Documentation markdown

**Format de sortie** :
- Documentation structurée 270-350 lignes
- 8 sections principales
- Tableaux synthétiques
- Template standardisé

---

### Agent : powerbi-audit

**Fichier** : [.claude/agents/powerbi-audit.md](.claude/agents/powerbi-audit.md)

**Quand l'utiliser** :
- Audit complet d'un rapport Power BI existant
- Avant mise en production d'un nouveau rapport
- Identification et correction de problèmes de performance
- Validation des bonnes pratiques Power BI

**Outils** : Read, Write, Bash, Grep, Glob

**Restrictions** :
- ❌ Fichiers cache (.pbi, .pbix, cache.abf)
- ❌ Credentials (localSettings.json, .env)
- ✅ Définitions TMDL (*.SemanticModel/definition/)
- ✅ Génération de rapports markdown

**Processus d'audit (8 phases)** :
1. Identification du rapport
2. Audit modèle de données (structure, relations, types, doublons)
3. Audit mesures DAX (performance, corrections, organisation)
4. Audit filtres et slicers (contextes, interactions)
5. Audit UX et lisibilité (recommandations)
6. Audit performance globale (volumétrie, optimisations)
7. Audit sécurité RLS (si applicable)
8. Génération rapport markdown UTF-8

**Niveaux de correction** :
- 🔴 Niveau 1 : Critiques seulement
- 🟠 Niveau 2 : Critiques + Importants
- 🟡 Niveau 3 : Tout (critiques + importants + recommandés)
- ⚪ Niveau 4 : Audit seulement (pas de modifications)

**Format de sortie** :
- Rapport markdown détaillé : `[NomRapport]_audit_[AAAAMMJJ]_v1.md`
- Encodage UTF-8 obligatoire
- Documentation complète des actions effectuées
- Scripts DAX générés pour corrections
- Traçabilité totale (avant/après pour chaque modification)

---

### Agent : code-reviewer

**Fichier** : [.claude/agents/code-reviewer.md](.claude/agents/code-reviewer.md)

**Quand l'utiliser** :
- Après modifications de code (pre-commit)
- Avant création de Pull Request
- Audit de sécurité du codebase

**Outils** : Read, Grep, Glob, Bash (read-only)

**Restrictions** :
- ❌ Pas de Write/Edit (lecture seule)
- ❌ Pas de modifications du repo (commit, push)
- ✅ Lecture de code source
- ✅ Commandes git d'analyse (diff, log, status)

**Critères de révision** :
1. Sécurité (secrets, vulnérabilités OWASP)
2. Qualité (lisibilité, complexité, DRY)
3. Tests (couverture, edge cases)
4. Performance (requêtes N+1, algorithmes)
5. Documentation (commentaires, API docs)

**Format de sortie** :
- Rapport structuré par priorité
- Critical / Warnings / Suggestions
- Références fichier:ligne
- Statistiques et métriques

## 🚀 Roadmap

### Agents en développement

- [ ] **sql-optimizer** : Optimisation de requêtes SQL
- [ ] **api-documenter** : Documentation OpenAPI automatique
- [ ] **test-generator** : Génération de tests unitaires
- [ ] **security-auditor** : Audit de sécurité approfondi

### Améliorations prévues

- [ ] Hooks de pré-commit automatiques
- [ ] Intégration CI/CD pour validation automatique
- [ ] Dashboard de métriques de qualité
- [ ] Templates de rapports personnalisables

## 💡 Contribution

Pour proposer de nouveaux agents ou améliorer les existants :

1. Consultez [CONTRIBUTING.md](CONTRIBUTING.md)
2. Utilisez le [TEMPLATE.md](.claude/agents/TEMPLATE.md)
3. Testez localement avant de soumettre
4. Documentez clairement le rôle et les restrictions

## 📞 Support

**Questions ou problèmes ?**

1. Consultez [README.md](README.md) - FAQ et guide complet
2. Consultez [CONTRIBUTING.md](CONTRIBUTING.md) - Créer des agents
3. `/help` dans Claude Code - Aide générale
4. [GitHub Issues](https://github.com/anthropics/claude-code/issues) - Reporter un bug

---

## 📅 Historique des versions

| Version | Date | Changements |
|---------|------|-------------|
| 1.0 | Janvier 2026 | Configuration initiale avec 2 agents |

---

**Maintenu par** : Étienne Le Pironnec / AQRCB
**Organisation** : Association Québécoise de Récupération des Contenants de Boissons
**Dernière mise à jour** : Janvier 2026
