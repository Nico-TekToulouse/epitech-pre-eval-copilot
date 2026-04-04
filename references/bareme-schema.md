# Variantes de schéma JSON barème

Ce fichier documente les variantes de structure JSON acceptées par le skill epitech-pre-eval.

---

## Schéma canonique (recommandé)

```json
{
  "project": "MyProject",
  "total_points": 100,
  "criteria": [
    {
      "id": "C1",
      "label": "L'application démarre sans erreur",
      "points": 10,
      "category": "fonctionnel",
      "mandatory": true,
      "hints": ["package.json scripts.start", "Makefile"]
    }
  ]
}
```

---

## Variante Epitech EIP (format simplifié)

```json
{
  "eip": "Nom EIP",
  "groupe": "Nom groupe",
  "semestre": "S8",
  "barème": [
    {
      "critère": "Description",
      "points_max": 5,
      "obligatoire": false
    }
  ]
}
```
→ Mapper `critère` → `label`, `points_max` → `points`, `obligatoire` → `mandatory`.

---

## Variante flat array

```json
[
  { "id": 1, "description": "...", "note_max": 4, "type": "code" },
  { "id": 2, "description": "...", "note_max": 2, "type": "doc" }
]
```
→ Mapper `description` → `label`, `note_max` → `points`, `type` → `category`.

---

## Variante avec sous-critères

```json
{
  "sections": [
    {
      "name": "Architecture",
      "weight": 30,
      "items": [
        { "id": "A1", "label": "...", "points": 10 }
      ]
    }
  ]
}
```
→ Aplatir les `items` de chaque section, préfixer l'id avec la section (`A-A1`).

---

## Champs optionnels reconnus

| Champ | Alias acceptés | Défaut si absent |
|-------|---------------|-----------------|
| `id` | `num`, `numero`, `ref` | Auto-généré (C1, C2...) |
| `label` | `description`, `critère`, `name`, `title` | Requis |
| `points` | `note_max`, `weight`, `score` | 1 |
| `category` | `type`, `domaine`, `section` | `"autre"` |
| `mandatory` | `obligatoire`, `required`, `bloquant` | `false` |
| `hints` | `keywords`, `fichiers`, `où_chercher` | `[]` |
