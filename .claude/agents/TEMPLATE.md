---
name: mon-agent
description: Description concise (1 phrase) de quand Claude doit déléguer à cet agent
tools: Read, Write, Edit, Bash, Grep, Glob
disallowedTools:
model: sonnet
permissionMode: default
---

# [Nom de votre agent]

[Description détaillée du rôle de l'agent - 2-3 phrases]

## 🎯 Objectif

[Que fait cet agent ? Pourquoi existe-t-il ? Quel problème résout-il ?]

## 🔧 Comportement attendu

Lorsque cet agent est invoqué, il doit :

1. [Première étape - ex: Analyser le contexte]
2. [Deuxième étape - ex: Identifier les fichiers concernés]
3. [Troisième étape - ex: Appliquer les transformations]
4. [Quatrième étape - ex: Valider les résultats]

## 📋 Règles et contraintes

### ✅ Autorisé

- [Action autorisée 1]
- [Action autorisée 2]
- [Action autorisée 3]

### ❌ Interdit

- [Action interdite 1 avec raison]
- [Action interdite 2 avec raison]
- [Action interdite 3 avec raison]

## 🔒 Fichiers et sécurité

### Fichiers INTERDITS

- ❌ `[pattern]` - [Raison]
- ❌ `[pattern]` - [Raison]

### Fichiers AUTORISÉS

- ✅ `[pattern]` - [Description]
- ✅ `[pattern]` - [Description]

## 💡 Exemples d'utilisation

### Cas 1 : [Nom du cas d'usage]

**Contexte** : [Décrivez la situation]

**Commande utilisateur** :
```
"[Ce que l'utilisateur dit pour déclencher cet agent]"
```

**Action de l'agent** :
1. [Étape 1]
2. [Étape 2]
3. [Étape 3]

**Résultat attendu** :
[Description du résultat]

### Cas 2 : [Nom du deuxième cas]

**Contexte** : [Décrivez la situation]

**Commande utilisateur** :
```
"[Ce que l'utilisateur dit]"
```

**Action de l'agent** :
1. [Étape 1]
2. [Étape 2]

**Résultat attendu** :
[Description du résultat]

## ⚙️ Configuration et dépendances

### Outils requis
- [Outil 1] - [Pourquoi]
- [Outil 2] - [Pourquoi]

### Commandes Bash autorisées (si applicable)
- ✅ `[commande]` - [Usage]
- ✅ `[commande]` - [Usage]

### Commandes Bash interdites
- ❌ `[commande]` - [Raison]
- ❌ `[commande]` - [Raison]

## 📝 Notes techniques

### Limitations
- [Limitation 1]
- [Limitation 2]

### Bonnes pratiques
- [Bonne pratique 1]
- [Bonne pratique 2]

### Points d'attention
- ⚠️ [Point d'attention important]
- ⚠️ [Point d'attention important]

## 🔄 En cas d'erreur

Si l'agent rencontre un problème :

1. **[Type d'erreur]** : [Comment réagir]
2. **[Type d'erreur]** : [Comment réagir]
3. **En cas de doute** : Demander confirmation à l'utilisateur plutôt que de deviner

## 📚 Ressources

- [Lien vers documentation pertinente]
- [Lien vers exemples]
- [Lien vers guides]

---

**Créé par** : [Votre nom]
**Date de création** : [Date]
**Version** : 1.0
**Dernière mise à jour** : [Date]
