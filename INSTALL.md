# Installation — epitech-pre-eval

## Méthode recommandée — `npx skills`

### Prérequis

- Node.js et npm installés (nécessaires pour utiliser `npx`)
- GitHub Copilot CLI installé et configuré

```bash
npx skills add Nico-TekToulouse/epitech-pre-eval -g -a github-copilot
```

## Méthode manuelle — Prérequis

- GitHub Copilot CLI installé et configuré
- Python 3.8+
- `git` (pour le clonage de repos)
- `unzip` (pour les archives ZIP)

## 1. Installer la dépendance Python

```bash
pip install openpyxl
```

## 2. Installer le skill dans Copilot CLI

```bash
# Depuis le répertoire parent du skill
gh copilot skill install ./epitech-pre-eval
```

Ou copier manuellement le dossier dans votre répertoire de skills Copilot :

```bash
cp -r epitech-pre-eval ~/.config/gh-copilot/skills/
```

## 3. Vérifier l'installation

```bash
gh copilot skill list
# → epitech-pre-eval doit apparaître dans la liste
```

## 4. Tester le skill

```bash
gh copilot chat "Pré-évalue ce projet étudiant avec ce barème : ..."
```

Le skill se déclenche automatiquement dès que vous mentionnez :
`pré-évaluation`, `barème`, `corriger un projet étudiant`, `vérifier les critères`, etc.

## Résolution de problèmes

| Problème | Solution |
|----------|---------|
| `openpyxl` non trouvé | `pip install openpyxl` ou `pip3 install openpyxl` |
| Skill non détecté | Vérifier que `skill.yaml` est présent et bien formé |
| Rapport Excel vide | Vérifier la structure du JSON résultats (voir `SKILL.md` Étape 6) |
| Clone échoue (repo privé) | Cloner manuellement puis fournir le chemin local |
