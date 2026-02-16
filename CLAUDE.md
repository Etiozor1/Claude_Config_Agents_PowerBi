# Instructions Projet - Claude Code

## Contexte du projet

Ce depot contient des agents et configurations Claude Code specialises pour Power BI (documentation, audit, review). Le public cible est une equipe BI. Toute reponse doit etre pertinente dans ce contexte.

## Langue

- Repondre TOUJOURS en francais, y compris pour les explications techniques.
- Les blocs de code, noms de fichiers et commandes restent en anglais.
- Les messages de commit suivent la convention Conventional Commits (en anglais).

## Style de reponse

- Reponses concises et actionnables, pas de paraphrase inutile.
- Privilegier les listes et tableaux aux longs paragraphes.
- Ne pas ajouter d'emojis sauf dans les fichiers agents (qui en utilisent deja).
- Ne pas proposer de modifications non demandees ("over-engineering").

## Securite Power BI

Ne JAMAIS lire, afficher ou referencer le contenu de :
- `*.pbix`, `.pbi/**`, `cache.abf`, `localSettings.json`
- `expressions.tmdl`, `**/partitions/*.tmdl`
- `.env*`, `secrets/**`

En cas de doute, consulter [powerbi-security.md](.claude/shared/powerbi-security.md).

## Conventions de nommage

- Agents : `kebab-case.md` dans `.claude/agents/`
- Skills : dossier `kebab-case/` avec `SKILL.md` dans `.claude/skills/`
- Documentation : `PascalCase.md` ou `UPPER_CASE.md` a la racine
- Pas d'espaces ni d'accents dans les noms de fichiers

## Edition des agents

- Ne JAMAIS modifier le frontmatter YAML (name, tools, model) sans demande explicite.
- Toujours preserver la structure existante d'un agent lors d'une modification.
- Apres toute modification d'agent, valider que le fichier reste un markdown valide.
- S'appuyer sur [TEMPLATE.md](.claude/agents/TEMPLATE.md) pour tout nouvel agent.

## Git

- Branche principale : `main`
- Nommage des branches : `feature/description` ou `fix/description`
- Commits : suivre le skill [git-commit](.claude/skills/git-commit/SKILL.md)
- Ne JAMAIS push sans confirmation explicite.
- Ne JAMAIS force-push sur `main`.

## Delegation aux agents

Quand la tache correspond a un agent existant, TOUJOURS deleguer :
- Documentation Power BI → `powerbi-documentation`
- Audit Power BI → `powerbi-audit`
- Revue de code → `code-reviewer`

Ne pas reproduire manuellement ce qu'un agent specialise fait deja.

## References obligatoires en fin de reponse

A la fin de CHAQUE reponse, tu DOIS inclure une section **References** contenant :

1. **Fichiers du projet** mentionnes ou utilises durant la reponse, sous forme de liens cliquables markdown :
   - Format : `[nom-du-fichier](chemin/relatif/depuis/la/racine)`
   - Exemple : `[powerbi-audit.md](.claude/agents/powerbi-audit.md)`

2. **Liens internet** consultes ou pertinents durant la reponse (documentation, sources, URLs GitHub, etc.) :
   - Format : `[description](https://url-complete)`

### Format attendu

```
---
**References :**
- Fichiers : [fichier1.md](chemin/fichier1.md), [fichier2.md](chemin/fichier2.md)
- Liens : [Description](https://url)
```

### Regles

- Si aucun fichier ni lien n'est pertinent, omettre la section silencieusement.
- Ne pas dupliquer les references deja presentes dans le corps de la reponse ; les regrouper uniquement en fin de reponse.
- Garder la section concise : lister uniquement les references directement pertinentes.
