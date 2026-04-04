---
name: epitech-pre-eval
description: >
  Pré-évalue automatiquement un projet étudiant Epitech à partir d'un barème JSON et du code source fourni
  (repo GitHub, archive ZIP, ou clone local). Génère un compte rendu rapide, un rapport détaillé critère
  par critère et un fichier Excel exportable. Utiliser ce skill dès que l'utilisateur mentionne :
  "pré-évaluation", "barème", "corriger un projet étudiant", "vérifier les critères", "noter un rendu",
  "EIP", "regarde si l'étudiant a bien fait X", "vérifie que le projet respecte le barème",
  "prépare mon évaluation", "analyse de code Epitech".
location: user
---

# Epitech Pre-Eval Skill

Effectue une pré-évaluation pédagogique d'un projet étudiant en croisant le barème JSON et le code source
fourni. Produit : un **compte rendu rapide**, un **rapport Markdown détaillé** et un **fichier Excel**.

> ⚠️ Ce rapport est une pré-évaluation, pas une note officielle. Toujours rappeler ce point à l'utilisateur.

---

## Étape 0 — Collecter les inputs manquants

Si le barème ou le code source n'est pas fourni, utilise `ask_user` pour les demander :

- **Barème** : fichier JSON uploadé, collé dans le chat, ou chemin local
- **Code source** : URL GitHub, archive ZIP, ou chemin local

Ne pas supposer les deux disponibles. Attendre les réponses avant de continuer.

---

## Étape 1 — Parser le barème JSON

### Formats acceptés

Lire le barème fourni et normaliser les champs. Consulter les variantes de format :

```
references/bareme-schema.md
```

**Normalisation obligatoire :**
| Champ cible | Alias acceptés | Défaut |
|-------------|---------------|--------|
| `id` | `num`, `numero`, `ref` | Auto C1, C2… |
| `label` | `description`, `critère`, `name`, `title` | Requis |
| `points` | `note_max`, `weight`, `score` | 1 |
| `category` | `type`, `domaine`, `section` | `"autre"` |
| `mandatory` | `obligatoire`, `required`, `bloquant` | `false` |
| `hints` | `keywords`, `fichiers`, `où_chercher` | `[]` |

Si le JSON est malformé, signaler l'erreur précisément et demander une correction.

---

## Étape 2 — Préparer le code source

| Mode | Action |
|------|--------|
| **Repo GitHub** | `git clone <url> /tmp/student-project` |
| **Archive ZIP** | `unzip <fichier> -d /tmp/student-project` |
| **Clone local** | Utiliser le chemin fourni directement |

Après préparation, cartographier l'arborescence :

```bash
find /tmp/student-project -type f -not -path "*/node_modules/*" -not -path "*/.git/*" | head -80
```

**Détecter automatiquement le langage principal** :

```bash
# Compter les fichiers par extension
find /tmp/student-project -type f | grep -oE '\.[a-zA-Z]+$' | sort | uniq -c | sort -rn | head -10
```

→ Adapter les commandes d'analyse et la référence bad-practices au langage détecté.

**Cas limites :**
| Situation | Comportement |
|-----------|-------------|
| Repo privé / clone échoue | Signaler, demander un chemin local |
| ZIP corrompu / mauvais format | Signaler l'erreur, demander le fichier à nouveau |
| Dossier vide / aucun fichier source | Signaler et arrêter |
| Code source dans un sous-dossier | Détecter automatiquement (`src/`, `app/`, `projet/`) |

---

## Étape 3 — Analyser le code critère par critère

Pour chaque critère du barème, effectuer l'analyse suivante.

### 3a. Stratégie par catégorie

| Catégorie | Stratégie |
|-----------|-----------|
| `fonctionnel` | Chercher l'implémentation (fonctions, routes, classes, tests) |
| `qualité` | Analyser style, lisibilité, duplication, commentaires, nommage |
| `architecture` | Vérifier structure des dossiers, séparation des responsabilités |
| `sécurité` | Détecter injections, secrets en dur, manque de validation |
| `autre` | Analyser au cas par cas selon le `label` |

### 3b. Outils à utiliser (dans cet ordre de préférence)

**Préférer les outils Copilot natifs aux commandes bash brutes :**

1. **`grep` tool** — Recherche de patterns dans les fichiers
2. **`glob` tool** — Recherche de fichiers par nom/pattern
3. **`view` tool** — Lecture d'un fichier spécifique
4. **`bash` tool** — Pour les opérations complexes ou les commandes système

Exemples d'utilisation :
```
# Chercher une feature : utiliser le grep tool avec pattern et glob
pattern: "mot_clé", glob: "**/*.ts"

# Vérifier l'existence d'un fichier : utiliser le glob tool
pattern: "**/middleware/auth*"

# Lire un fichier : utiliser le view tool
path: /tmp/student-project/src/auth/login.ts

# Compter les occurrences (bash si nécessaire)
grep -c "pattern" fichier
```

### 3c. Détecter les mauvaises pratiques pendant l'analyse

En plus de vérifier le critère, signaler les mauvaises pratiques observées.
Consulter la liste complète par langage :

```
references/bad-practices.md
```

**Pratiques à détecter systématiquement (tous langages) :**
- Secrets / credentials en dur dans le code
- Absence de gestion d'erreurs
- Fonctions trop longues (> 50 lignes)
- Duplication de code évidente
- Variables mal nommées (`data`, `temp`, `x`, `tmp`)
- `node_modules` ou binaires commités
- Absence de `.gitignore`
- Code mort / commenté en production
- TODO/FIXME laissés sans traitement

### 3d. Statuts

| Statut | Signification |
|--------|--------------|
| ✅ **VALIDÉ** | Critère clairement rempli, implémentation correcte |
| ⚠️ **PARTIEL** | Implémentation présente mais incomplète ou avec défauts |
| ❌ **NON VALIDÉ** | Critère absent ou implémentation incorrecte |
| 🚫 **BLOQUANT** | Critère `mandatory: true` non validé |

> **Règle du doute** : si incertain qu'un critère est rempli → classer en ⚠️ PARTIEL.

---

## Étape 4 — Générer le compte rendu rapide

Avant le rapport détaillé, afficher un **résumé court** pour permettre un point rapide :

```markdown
## ⚡ Compte rendu rapide — [Nom du projet]

**Score estimé :** XX / YY pts

| ✅ Validés | ⚠️ Partiels | ❌ Non validés | 🚫 Bloquants |
|------------|------------|----------------|--------------|
| X          | X          | X              | X            |

### 🔴 Top 3 — Points critiques à corriger avant l'évaluation
1. **[C?]** Libellé — raison courte
2. **[C?]** Libellé — raison courte
3. **[C?]** Libellé — raison courte

*Rapport détaillé ci-dessous. Fichiers générés : `pre-eval-[projet]-[date].md` + `.xlsx`*
```

---

## Étape 5 — Générer le rapport Markdown détaillé

```markdown
# 📋 Pré-évaluation — [Nom du projet]
> ⚠️ Ce rapport est une pré-évaluation indicative, pas une note officielle.

**Étudiant :** [si fourni]  
**Date :** [date du jour]  
**Score estimé :** XX / YY points  
**Langage principal détecté :** [langage]

---

## 🔴 Points bloquants
> Critères mandatory non validés — à corriger impérativement avant l'évaluation.

- [C1] **Libellé** — Raison du blocage

---

## 📊 Résultats par critère

### [Catégorie : Fonctionnel]

#### ✅ C2 — Libellé (10 pts)
Implémentation trouvée dans `src/auth/login.ts` ligne 42.

#### ⚠️ C3 — Libellé (8 pts) — PARTIEL
Implémentation présente mais incomplète :
```typescript
function doSomething() {
  // manque la gestion d'erreur
}
```
**Problème :** Aucun `try/catch`, les erreurs réseau ne sont pas propagées.

#### ❌ C4 — Libellé (5 pts) — NON VALIDÉ
Aucune implémentation trouvée. Recherche sur `mot_clé` → 0 résultats.

---

## ⚠️ Points d'alerte (mauvaises pratiques)

| Fichier | Ligne | Problème | Sévérité |
|---------|-------|----------|----------|
| `src/config.ts` | 12 | API key en dur | 🔴 Critique |
| `src/utils.ts` | 88 | Fonction de 120 lignes | 🟡 Moyen |

---

## 💡 Recommandations pédagogiques

Chaque recommandation **doit** être structurée avec les trois éléments suivants :

1. **Localisation précise** : indiquer le fichier exact et, si possible, la ligne ou la fonction concernée
2. **Action concrète** : décrire précisément ce que l'étudiant doit faire (pas "corriger l'auth" mais "ajouter la vérification du token dans `src/auth/middleware.ts` ligne 45")
3. **Exemple de code minimal** : fournir un snippet de 3–10 lignes si cela aide à comprendre la correction attendue

**Format à respecter pour chaque recommandation :**
````
### [C?] Libellé du critère
**Fichier :** `src/chemin/vers/fichier.ts` (ligne X)
**Action :** Description précise de ce qui doit être fait
**Exemple :**
```typescript
// Avant
const token = req.headers.authorization;  // pas de validation

// Après
const token = req.headers.authorization?.split(' ')[1];
if (!token) return res.status(401).json({ error: 'Unauthorized' });
```
````

**Guide de ton par statut :**
| Statut | Ton recommandé |
|--------|---------------|
| 🚫 **BLOQUANT** | Direct et urgent : "Ce critère est bloquant. Sans cette correction, le projet ne peut pas être validé." |
| ❌ **NON VALIDÉ** | Factuel et constructif : "La fonctionnalité X est absente. Voici comment l'implémenter..." |
| ⚠️ **PARTIEL** | Encourageant et précis : "La base est là, mais il manque Y. Une petite modification suffit..." |
| ✅ **VALIDÉ** | Confirmatif et bref : "Implémentation correcte détectée dans `fichier`. Vérifier manuellement si confiance faible." |

**Exemple de recommandations structurées :**

### [C1 — 🚫 BLOQUANT] Authentification JWT absente
**Fichier :** `src/routes/api.ts` — aucun middleware d'auth détecté
**Action :** Ajouter un middleware JWT sur toutes les routes protégées.
**Exemple :** `router.use('/api', verifyToken);` dans `src/routes/index.ts`

### [C2 — ⚠️ PARTIEL] Gestion des erreurs incomplète
**Fichier :** `src/controllers/user.ts` (lignes 23–45)
**Action :** Entourer les appels async d'un try/catch et retourner un statut HTTP approprié.
**Exemple :** `try { ... } catch (e) { res.status(500).json({ error: e.message }) }`

---

## 📈 Récapitulatif

| Statut | Nombre | Points |
|--------|--------|--------|
| ✅ Validé | X | XX pts |
| ⚠️ Partiel | X | ~XX pts |
| ❌ Non validé | X | 0 pts |
| 🚫 Bloquant | X | — |
| **Total estimé** | | **XX / YY pts** |
```

**Règles de rédaction :**
- Toujours en français, ton pédagogique et bienveillant
- Citer le fichier et la ligne source de chaque conclusion
- Limiter les extraits de code à 10–20 lignes max
- Focus sur la partie problématique dans les extraits

---

## Étape 6 — Générer le fichier Excel

Appeler le script Python pour générer le fichier Excel :

```bash
# Sauvegarder les résultats au format JSON intermédiaire
# Structure attendue par le script :
# {
#   "project": "...", "student": "...", "date": "...",
#   "criteria_results": [
#     {"id": "C1", "label": "...", "category": "...", "points_max": 10,
#      "status": "validated|partial|failed|blocking", "points_obtained": 10, "remarks": "..."}
#   ],
#   "bad_practices": [
#     {"file": "...", "line": 12, "problem": "...", "severity": "critical|medium|minor"}
#   ]
# }
python3 scripts/generate_report.py \
  --results /tmp/eval-results.json \
  --output /tmp/pre-eval-[projet]-[date].xlsx
```

**Structure du classeur Excel :**

- **Onglet "Résultats"** : une ligne par critère — id, label, catégorie, points max, statut, points obtenus, remarques (couleurs : vert/orange/rouge/violet)
- **Onglet "Mauvaises pratiques"** : fichier, ligne, problème, sévérité (surlignage couleur par sévérité)
- **Onglet "Récapitulatif"** : score total, comptage par statut, liste des bloquants, date

---

## Étape 7 — Output final

1. Afficher le **compte rendu rapide** (Étape 4)
2. Afficher le **rapport Markdown détaillé** (Étape 5)
3. Générer et présenter le **fichier Excel** (Étape 6)
4. Sauvegarder le rapport Markdown dans un fichier `pre-eval-[projet]-[date].md`
5. Proposer d'approfondir un critère spécifique si demandé

---

## Notes importantes

- **Ne pas noter à la place de l'enseignant** : toujours rappeler que c'est une pré-évaluation.
- **Transparence** : citer toujours le fichier et la ligne source de chaque conclusion.
- **Doute = partiel** : si incertain → ⚠️ PARTIEL.
- **Langage du rapport** : toujours en français, ton pédagogique et bienveillant.
- **Extraits de code** : 10–20 lignes max, focus sur la partie problématique.
