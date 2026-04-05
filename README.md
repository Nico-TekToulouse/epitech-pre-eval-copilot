# epitech-pre-eval — Skill GitHub Copilot CLI

[![Install with skills CLI](https://img.shields.io/badge/skills.sh-install-blue?logo=github)](https://skills.sh/Nico-TekToulouse/epitech-pre-eval)
[![Agent Skills](https://img.shields.io/badge/agent--skills-compatible-green)](https://github.com/vercel-labs/skills)

Pré-évalue automatiquement un projet étudiant Epitech à partir d'un **barème JSON** et du **code source** fourni.

## ⚡ Installation rapide

```bash
npx skills add Nico-TekToulouse/epitech-pre-eval -g -a github-copilot
```

> Prérequis : **Node.js** et **npm** (qui fournit `npx`) doivent être installés. Voir https://nodejs.org/ si nécessaire.

## Ce que ce skill produit

| Output | Description |
|--------|-------------|
| ⚡ **Compte rendu rapide** | Score estimé, comptage statuts, top 3 critiques — affiché en premier |
| 📋 **Rapport Markdown** | Analyse critère par critère avec extraits de code et bad practices |
| 📊 **Fichier Excel** | 3 onglets : Récapitulatif, Résultats par critère, Mauvaises pratiques |
| 💾 **Fichier `.md`** | Rapport détaillé sauvegardé localement |

## Fonctionnement

1. Fournir un **barème JSON** (voir `references/bareme-schema.md` pour les formats acceptés)
2. Fournir le **code source** (URL GitHub, archive ZIP, ou chemin local)
3. Le skill analyse le code critère par critère et génère les outputs

## Formats de barème supportés

- Format canonique (`project`, `criteria[]`)
- Format EIP Epitech (`eip`, `barème[]`)
- Format flat array
- Format avec sous-sections (`sections[]`)

Voir `references/bareme-schema.md` pour la documentation complète.

## Langages supportés pour la détection de mauvaises pratiques

TypeScript · JavaScript · Java · C · C++ · Python · Go · Rust

## Installation

> Installation via la commande `npx skills` (recommandée) :
>
> Prérequis : **Node.js** et **npm** (qui fournit `npx`) doivent être installés. Voir https://nodejs.org/ si nécessaire.
>
> ```bash
> npx skills add Nico-TekToulouse/epitech-pre-eval -g -a github-copilot
> ```

Voir [INSTALL.md](INSTALL.md) pour les autres méthodes d'installation.

## Exemples de prompts

Voir [EXAMPLE_PROMPT.md](EXAMPLE_PROMPT.md).

## Dépendances

- `bash` — Commandes système
- `python3` + `openpyxl` — Génération Excel
- `git` — Clonage de repos (optionnel)
- `unzip` — Extraction ZIP (optionnel)

## Structure du projet

```
epitech-pre-eval/
  SKILL.md              # Instructions du skill pour l'agent
  skill.yaml            # Métadonnées (triggers, outputs, deps)
  README.md             # Ce fichier
  INSTALL.md            # Guide d'installation
  EXAMPLE_PROMPT.md     # Exemples de prompts
  scripts/
    generate_report.py  # Génération du fichier Excel (.xlsx)
  references/
    bad-practices.md    # Liste des bad practices par langage
    bareme-schema.md    # Variantes de format JSON barème
    example-bareme.json # Exemple de barème complet
```

## ⚠️ Limites

Ce skill est un outil de **pré-évaluation assistée**, pas un correcteur automatique. Il a des limites importantes à connaître avant utilisation :

| Limite | Description |
|--------|-------------|
| **Pattern matching uniquement** | Le skill détecte des patterns textuels dans le code, il ne compile ni n'exécute le projet. Un pattern trouvé ≠ fonctionnalité fonctionnelle. |
| **Score non fiable seul** | La fourchette estimée est indicative (±15 pts). Ne jamais l'utiliser comme note finale sans vérification manuelle. |
| **Tests non exécutés** | Les tests détectés ne sont pas lancés. Un fichier de test peut exister sans passer. |
| **Dépendances confondues avec le code** | Un pattern dans `node_modules` ou `vendor` peut être comptabilisé. Niveau de confiance `Low` dans ce cas. |
| **Langages partiellement couverts** | C#, Kotlin, PHP, Bash ont une couverture de base. Les langages non listés reçoivent un avertissement explicite. |
| **Projets sans structure standard** | Les projets mono-fichier, notebooks Jupyter, ou projets embarqués peuvent donner des résultats imprécis. |
| **Code obfusqué ou minifié** | Les fichiers minifiés ou obfusqués ne sont pas analysables par pattern matching. |

> **Règle d'or** : utiliser ce rapport comme point de départ pour l'évaluation, jamais comme point d'arrivée. Toujours compléter avec une vérification manuelle et un échange avec l'étudiant.
