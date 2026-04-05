# 📚 Analyse Pédagogique — `epitech-pre-eval`

> Rapport rédigé suite à une analyse critique du skill du point de vue pédagogique.  
> Date : 2026-04-04

---

## 🎯 Verdict général

Le skill a une **intention pédagogique correcte** (assister l'enseignant dans la pré-évaluation) mais présente des **biais critiques** qui le rendent risqué s'il remplace le jugement de l'enseignant.

> Il mesure des *signaux* (patterns dans le code), pas de la *qualité réelle*.

---

## 1. 🔴 Biais d'évaluation

### Problème A — grep valide le code sans l'exécuter
**Sévérité : 🔴 Critique**

L'analyse repose sur la recherche de patterns textuels (`grep`, `glob`, `view`). Un critère peut être marqué ✅ VALIDÉ alors que le code est présent mais non fonctionnel.

**Exemple concret :**
- Critère C5 "mots de passe hashés" → `bcrypt` trouvé dans `package.json` → ✅ VALIDÉ
- Mais `bcrypt` n'est peut-être jamais appelé dans le code réel

**Autre exemple :**
- Route `/auth/register` détectée dans un fichier → ✅
- Mais la route n'est pas enregistrée dans `app.ts` → elle n'existe pas à l'exécution

**Recommandation :**
- Ajouter un **niveau de confiance** par critère : `High / Medium / Low`
- `High` = pattern trouvé + tests existants détectés
- `Medium` = pattern trouvé, tests absents
- `Low` = pattern ambigu ou trouvé uniquement dans les dépendances
- Afficher ce niveau dans le rapport pour guider la vérification manuelle

---

### Problème B — Statuts binaires sur des critères graduels
**Sévérité : 🔴 Critique**

Les 4 statuts (✅/⚠️/❌/🚫) sont corrects en intention, mais le skill n'a aucun critère de granularité pour décider entre ✅ et ⚠️.

**Exemple :**
> Critère : "Route POST /auth/register fonctionnelle avec validation des champs"
- Valide 1 champ sur 3 → ⚠️ ou ❌ ?
- Valide tous les champs mais pas côté serveur → ⚠️ ou ❌ ?

Deux enseignants appliquant le skill sur le même code peuvent obtenir des résultats différents.

**Recommandation :**
- Associer à chaque critère `hints` une liste de **sous-vérifications** :
  ```json
  "sub_checks": [
    "La route existe et répond",
    "Les champs requis sont validés",
    "Les erreurs sont renvoyées avec le bon code HTTP"
  ]
  ```
- Le statut PARTIEL = au moins un sous-check validé, pas tous

---

### Problème C — Score estimé = illusion de certitude
**Sévérité : 🔴 Critique**

Le compte rendu rapide affiche `Score estimé : XX / YY pts`. Ce chiffre précis donne l'impression d'une mesure fiable alors qu'il est basé sur du pattern matching.

De plus, le script `generate_report.py` calcule :
```python
"partial": lambda r: round(r.get("points_max", 0) * 0.5, 1)  # Toujours 50%
```
Un critère PARTIEL vaut toujours 50%, qu'il soit complété à 20% ou à 80%.

**Recommandation :**
- Supprimer l'affichage numérique `37/50` du compte rendu rapide
- Remplacer par : `8 ✅ — 2 ⚠️ — 1 ❌ — 0 🚫`
- Ajouter une fourchette : `Fourchette estimée : 30–45 pts (±15 selon vérification manuelle)`
- Encadrer le rapport d'un avertissement visible :

```markdown
> ⚠️ Ce rapport détecte des patterns, pas l'exécution.
> Tous les critères ✅ doivent être testés manuellement avant de noter.
```

---

## 2. 🟡 Bad practices mal calibrées pour Epitech

### Problème A — TODO/FIXME listés comme bad practice
**Sévérité : 🟡 Moyen**

`bad-practices.md` liste :
```
TODO/FIXME non traités | 🟡 Moyen
```

**Contexte pédagogique :**
Un TODO lisible est souvent une **preuve que l'étudiant a itéré et priorisé**. Dans un projet de 2-3 mois (EIP, projet B2), c'est attendu et sain. Marquer ça comme bad practice pénalise la documentation de la pensée.

**Recommandation :**
- Retirer TODO/FIXME des bad practices ou les passer en `🟢 Mineur`
- Ne signaler que les TODO dans des zones critiques (authentification, sécurité)

---

### Problème B — Couverture inégale par langage
**Sévérité : 🟡 Moyen**

Les bad practices couvrent bien TypeScript/JavaScript, Java, C/C++, Python, Go, Rust.  
Mais sont **absents** :

| Langage | Présent ? | Impact |
|---------|-----------|--------|
| C# / .NET | ❌ | Projets Epitech fréquents |
| PHP | ❌ | Projets web B1/B2 |
| Kotlin / Android | ❌ | Projets mobile |
| Bash/Shell | ❌ | Scripts DevOps |

**Conséquence :** Un projet C# reçoit `0 mauvaises pratiques détectées` même si le code est chaotique. L'absence de détection peut être interprétée comme qualité.

**Recommandation :**
- Ajouter une section **"Langage non couvert"** dans le rapport :
  ```
  ⚠️ Aucune bad practice détectée pour le langage C# (non couvert par ce skill).
  L'absence de détection ne signifie pas absence de problèmes.
  ```
- Enrichir `bad-practices.md` avec C# et Kotlin a minima

---

### Problème C — Pas de calibration par niveau d'étude
**Sévérité : 🟡 Moyen**

Le skill applique la même grille à un étudiant B1 et à un EIP B3+.

| Bad practice | Sévérité actuelle | Sévérité adaptée B1 | Sévérité adaptée B3+ |
|---|---|---|---|
| Absence `.gitignore` | 🔴 Critique | 🔴 Critique ✅ | 🔴 Critique ✅ |
| `console.log` en prod | 🟡 Moyen | 🟢 Mineur | 🟡 Moyen |
| Pas de type hints Python | 🟡 Moyen | 🟢 Mineur | 🔴 Critique |
| TODO/FIXME | 🟡 Moyen | 🟢 Mineur | 🟢 Mineur |

**Recommandation :**
- Ajouter un paramètre optionnel `"student_level": "B1 | B2 | B3 | EIP"` dans le barème
- Adapter la sévérité des bad practices en fonction du niveau

---

### Problème D — Types de projets non couverts
**Sévérité : 🟡 Moyen**

Le skill est optimisé pour les **APIs REST Node.js** (c'est l'exemple fourni).

| Type de projet Epitech | Couverture réelle |
|------------------------|-------------------|
| API REST Node.js/TS | ✅ ~90% |
| Projet C Unix (B1) | 🟡 ~60% |
| Projet ML / IA | ❌ ~5% |
| Projet IoT / Embarqué | ❌ ~10% |
| Projet Mobile (Kotlin/Swift) | ❌ ~0% |
| Projet Infrastructure / DevOps | 🟡 ~30% |
| Projet Frontend React/Vue | 🟡 ~50% |

**Recommandation :**
- Documenter explicitement les types de projets couverts dans `README.md`
- Ajouter une section "Limites" dans le rapport généré :
  ```markdown
  ## ⚠️ Limites de cette analyse
  Ce skill est optimisé pour les projets web (Node.js, Java, Python).
  Pour les projets ML, IoT, Mobile ou Embarqué, l'analyse est partielle.
  ```

---

## 3. 🔴 Qualité du feedback pédagogique

### Problème A — Recommandations génériques
**Sévérité : 🔴 Critique**

Le template du rapport contient :
```markdown
## 💡 Recommandations pédagogiques
1. **[Critique]** ...
2. **[Important]** ...
3. **[Conseil]** ...
```

Aucune instruction dans le SKILL.md sur le *contenu* de ces recommandations. L'agent Copilot les génère librement, ce qui produit des conseils vagues :

> ❌ Mauvais : "L'authentification JWT doit être corrigée."  
> ✅ Bon : "Créer `middleware/authenticate.ts`, vérifier le header `Authorization: Bearer <token>`, retourner 401 si invalide, appliquer le middleware à toutes les routes `/api/*`."

**Recommandation :**
- Ajouter dans `SKILL.md` une instruction explicite :
  ```
  Chaque recommandation doit contenir :
  1. Le fichier/endroit précis où intervenir
  2. L'action concrète à effectuer (pas "améliorer X" mais "créer Y avec Z")
  3. Un exemple de code minimal si pertinent
  ```

---

### Problème B — Pas de distinction entre "erreur grave" et "point d'amélioration"
**Sévérité : 🟡 Moyen**

Tous les critères ❌ et ⚠️ sont traités avec le même ton dans le rapport. Mais pédagogiquement :
- ❌ NON VALIDÉ sur un critère `mandatory` = **bloque la note, à corriger impérativement**
- ⚠️ PARTIEL sur un critère de qualité = **bon début, à approfondir**

Ces deux cas méritent des formulations très différentes.

**Recommandation :**
- Ajouter dans le SKILL.md un guide de tone par statut :
  - 🚫 BLOQUANT : "Cette fonctionnalité est absente — elle est requise pour valider le projet."
  - ❌ NON VALIDÉ : "Nous n'avons pas trouvé d'implémentation de X. Voici comment l'aborder..."
  - ⚠️ PARTIEL : "Le travail est amorcé sur X. Il manque Y et Z pour atteindre la validation complète."
  - ✅ VALIDÉ : "X est correctement implémenté dans `fichier.ts` ligne N."

---

## 4. 🟡 Positionnement et garde-fous

### Problème — Le skill peut substituer l'enseignant sans le vouloir
**Sévérité : 🟡 Moyen**

Le workflow produit un rapport Excel complet avec score, statuts colorés et recommandations. Pour un enseignant pressé, ce rapport peut atterrir directement dans la notation officielle.

Le seul garde-fou est cette ligne discrète en haut du rapport :
> "⚠️ Ce rapport est une pré-évaluation, pas une note officielle"

**Recommandation :**
- Ajouter une **checklist de vérification manuelle** à la fin du rapport :
  ```markdown
  ## ✅ Checklist avant de noter
  - [ ] Tester l'exécution du projet (npm start, make, etc.)
  - [ ] Vérifier manuellement les critères 🚫 BLOQUANTS
  - [ ] Lire au moins 3 fichiers source clés
  - [ ] Demander à l'étudiant d'expliquer une partie de son code
  - [ ] Ne pas utiliser le score estimé comme note finale
  ```
- Ajouter dans le rapport Excel une colonne "À vérifier manuellement" (booléen, TRUE pour tous les critères ✅ sans tests détectés)

---

## 📊 Récapitulatif des problèmes

| Axe | Problème | Sévérité |
|-----|----------|----------|
| Biais évaluation | grep valide sans exécuter | 🔴 Critique |
| Biais évaluation | Statuts binaires sur critères nuancés | 🔴 Critique |
| Biais évaluation | Score estimé = fausse certitude | 🔴 Critique |
| Bad practices | TODO/FIXME comme bad practice | 🟡 Moyen |
| Bad practices | Couverture langages inégale (C#, Kotlin absents) | 🟡 Moyen |
| Bad practices | Pas de calibration par niveau d'étude | 🟡 Moyen |
| Bad practices | Types de projets non couverts (ML, IoT, Mobile) | 🟡 Moyen |
| Feedback | Recommandations trop génériques | 🔴 Critique |
| Feedback | Pas de distinction grave vs conseil | 🟡 Moyen |
| Positionnement | Aucune checklist de vérification manuelle | 🟡 Moyen |

---

## 🎯 Top 5 — Améliorations prioritaires

### 1. 🔴 Supprimer le score numérique — remplacer par fourchette + comptage statuts
Éliminer l'illusion de précision. `37/50` → `8✅ 2⚠️ 1❌ — fourchette : 30–45 pts`. Impact maximal, effort faible.

### 2. 🔴 Ajouter un niveau de confiance par critère (High / Medium / Low)
Indiquer à l'enseignant ce qu'il doit vérifier manuellement vs ce qui est quasi-certain. Aide à prioriser le temps de vérification.

### 3. 🔴 Structurer les recommandations pédagogiques (fichier + action + exemple)
Remplacer les recommandations vagues par des actions concrètes et localisées. L'étudiant doit savoir exactement quoi faire après lecture.

### 4. 🟡 Calibrer les bad practices par niveau (B1/B2/EIP) et retirer TODO/FIXME
Adapter la sévérité au contexte de l'étudiant. Ne pas pénaliser ce qui est normal à un niveau donné.

### 5. 🟡 Ajouter la checklist de vérification manuelle + documenter les limites du skill
Forcer l'enseignant à ne pas utiliser le rapport seul. Documenter explicitement les types de projets et langages non couverts.

---

## 💡 Recommandation de fond

Ce skill est **utile comme outil de préparation**, pas comme outil de notation.

Sa vraie valeur : **gagner du temps en ciblant les zones à vérifier** plutôt qu'en fournissant une évaluation clé en main. Reformuler cet objectif dès le `README.md` changerait la façon dont les enseignants l'utilisent.

> "Ce skill vous dit **où regarder**, pas **quoi noter**."
