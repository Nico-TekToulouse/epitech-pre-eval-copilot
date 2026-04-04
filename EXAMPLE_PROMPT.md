# Exemples de prompts — epitech-pre-eval

## Déclenchement automatique

Le skill se déclenche automatiquement dès que votre message contient :
`pré-évaluation`, `barème`, `corriger un projet étudiant`, `vérifier les critères`,
`noter un rendu`, `EIP`, `prépare mon évaluation`, `analyse de code Epitech`.

---

## Exemples basiques

```
Pré-évalue ce projet étudiant. Voici le barème : [coller le JSON]
Le code est sur GitHub : https://github.com/etudiant/mon-projet
```

```
Vérifie que ce projet respecte le barème Epitech.
Barème : [coller le JSON]
Code source : /Users/moi/projets/etudiant-api
```

```
Prépare mon évaluation de demain. Voici le rendu de l'étudiant :
- Repo : https://github.com/etudiant/eip-s8
- Barème : [coller ou uploader le fichier JSON]
```

---

## Avec un fichier ZIP

```
Pré-évalue ce projet. Le barème est joint en JSON et le code source
est dans l'archive ZIP jointe.
```

---

## Avec un chemin local

```
Analyse le projet de l'étudiant Jean Dupont.
Barème : /Users/moi/baremes/api-rest.json
Code source : /Users/moi/rendus/jean-dupont/
```

---

## EIP Epitech (format spécifique)

```
Pré-évalue cet EIP avec le barème EIP S8 ci-dessous.
L'étudiant est Marie Martin, groupe Innovation.
[coller le barème format EIP]
Repo : https://github.com/marie-martin/eip-projet
```

---

## Approfondir un critère après le rapport

```
Peux-tu approfondir l'analyse du critère C4 sur l'authentification JWT ?
```

```
Donne-moi plus de détails sur les mauvaises pratiques de sécurité détectées.
```

---

## Formats de barème dans les prompts

### Format canonique (recommandé)
```json
{
  "project": "API REST Node.js",
  "total_points": 50,
  "criteria": [
    {
      "id": "C1",
      "label": "Le projet démarre sans erreur",
      "points": 5,
      "category": "fonctionnel",
      "mandatory": true,
      "hints": ["package.json", "index.ts"]
    }
  ]
}
```

### Format simplifié EIP
```json
{
  "eip": "Mon Projet EIP",
  "groupe": "Team Alpha",
  "semestre": "S8",
  "barème": [
    { "critère": "Architecture micro-services", "points_max": 10, "obligatoire": true },
    { "critère": "Documentation technique", "points_max": 5, "obligatoire": false }
  ]
}
```

Voir `references/bareme-schema.md` pour tous les formats supportés.
