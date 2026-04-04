# epitech-pre-eval — Skill GitHub Copilot CLI

Pré-évalue automatiquement un projet étudiant Epitech à partir d'un **barème JSON** et du **code source** fourni.

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

Voir [INSTALL.md](INSTALL.md).

## Exemples de prompts

Voir [EXAMPLE_PROMPT.md](EXAMPLE_PROMPT.md).

## Dépendances

- `bash` — Commandes système
- `python3` + `openpyxl` — Génération Excel
- `git` — Clonage de repos (optionnel)
- `unzip` — Extraction ZIP (optionnel)

## Structure du projet

```
epitech-pre-eval-copilot/
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
