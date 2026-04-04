# Mauvaises pratiques par langage

Référence pour la détection automatique de bad practices dans le code étudiant.

---

## Pratiques communes (tous langages)

| Pattern | Sévérité | Grep/détection |
|---------|----------|----------------|
| Credentials/API keys en dur | 🔴 Critique | `grep -rn "password\s*=\|api_key\s*=\|secret\s*="` |
| TODO/FIXME non traités | 🟢 Mineur | `grep -rn "TODO\|FIXME\|HACK\|XXX"` | Ne signaler que les TODO dans des zones critiques (authentification, sécurité) |
| Code commenté en masse | 🟡 Moyen | Blocs de `//` ou `/* */` > 5 lignes consécutives |
| Absence de .gitignore | 🔴 Critique | `ls .gitignore` → not found |
| node_modules / binaires commités | 🔴 Critique | `find . -name "node_modules" -type d` dans le repo |
| Fichiers de config d'IDE commités | 🟢 Mineur | `.vscode/`, `.idea/` dans le repo |
| Magic numbers sans constante | 🟡 Moyen | Nombres literals dans le code métier |

---

## TypeScript / JavaScript

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `any` abusif | 🟡 Moyen | `grep -rn ": any"` |
| `console.log` en production | 🟡 Moyen | `grep -rn "console\.log"` |
| Callbacks imbriqués (callback hell) | 🟡 Moyen | Imbrication > 3 niveaux |
| `var` au lieu de `const`/`let` | 🟢 Mineur | `grep -rn "\bvar\b"` |
| Absence de gestion de Promise rejetée | 🔴 Critique | `.then(` sans `.catch(` ou `try/await` |
| `==` au lieu de `===` | 🟡 Moyen | `grep -rn "[^=!]==[^=]"` |
| Imports `*` non nécessaires | 🟢 Mineur | `grep -rn "import \*"` |
| Pas de types sur les paramètres de fonction | 🟡 Moyen | `function x(param)` sans type |

---

## Java

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `e.printStackTrace()` seul | 🟡 Moyen | `grep -rn "printStackTrace"` |
| `catch (Exception e)` trop large | 🟡 Moyen | `grep -rn "catch.*Exception"` |
| Nullcheck absent sur objet récupéré | 🔴 Critique | Retour d'Optional sans `.isPresent()` |
| `System.out.println` en prod | 🟡 Moyen | `grep -rn "System\.out\.print"` |
| Classe sans constructeur privé (singleton) | 🟢 Mineur | Vérification pattern |
| Champs publics non encapsulés | 🟡 Moyen | `grep -rn "public [A-Za-z]* [a-z]"` dans les entités |
| Absence de `@Override` | 🟢 Mineur | Méthodes surchargées non annotées |

---

## C / C++

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `malloc` sans `free` (leak) | 🔴 Critique | `grep -c "malloc"` vs `grep -c "free"` |
| Buffer overflow potentiel (`gets`, `strcpy`) | 🔴 Critique | `grep -rn "\bgets\b\|strcpy\|sprintf[^_]"` |
| Retour de pointeur local | 🔴 Critique | `return &variable_locale` |
| Valeur de retour ignorée | 🟡 Moyen | Appels sans vérification du retour |
| `void*` abusif | 🟡 Moyen | `grep -rn "void \*"` |
| Absence de `const` sur paramètres non modifiés | 🟢 Mineur | Paramètres pointeurs sans `const` |
| Macros sans parenthèses | 🟡 Moyen | `#define MACRO x+y` sans parenthèses |

---

## Python

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `except:` nu (bare except) | 🟡 Moyen | `grep -rn "except:"` |
| `print()` de debug laissé | 🟢 Mineur | `grep -rn "^print("` |
| Mutable default argument | 🔴 Critique | `def func(x=[]):` |
| Import `*` | 🟡 Moyen | `grep -rn "from .* import \*"` |
| Pas de type hints (si attendu) | 🟡 Moyen | Fonctions sans annotations |
| Comparaison avec `==` à `None` | 🟢 Mineur | `== None` au lieu de `is None` |

---

## Go

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| Erreur ignorée (`_`) | 🔴 Critique | `grep -rn ", _\b"` |
| `panic()` en production | 🟡 Moyen | `grep -rn "panic("` |
| Goroutine leak (sans cancel/done) | 🔴 Critique | `go func` sans context |
| `fmt.Println` de debug | 🟢 Mineur | `grep -rn "fmt\.Println"` |
| Absence de gestion du contexte | 🟡 Moyen | Fonctions HTTP sans `context.Context` |

---

## Rust

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `.unwrap()` sans justification | 🟡 Moyen | `grep -rn "\.unwrap()"` |
| `.expect()` avec message vide | 🟢 Mineur | `grep -rn '\.expect("")'` |
| `unsafe` non documenté | 🔴 Critique | `grep -rn "unsafe"` sans commentaire |
| `clone()` excessif | 🟢 Mineur | `grep -c "\.clone()"` élevé |

---

## Architecture & Organisation

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| Logique métier dans les controllers/routes | 🔴 Critique | Fichier route > 100 lignes avec logique |
| Absence de structure de dossiers claire | 🟡 Moyen | Tout à la racine `src/` |
| Absence de séparation config/code | 🔴 Critique | Hardcoded URLs/ports |
| Tests absents | 🟡 Moyen | `find . -name "*.test.*" -o -name "*.spec.*"` → 0 |
| Un seul fichier monolithique | 🟡 Moyen | Fichier > 500 lignes |
| Fonctions trop longues | 🟡 Moyen | Fonction > 50 lignes (heuristique) |

---

## Sécurité

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| SQL concaténé (injection) | 🔴 Critique | `grep -rn "SELECT.*\+" ou "query.*\+"` |
| Pas de validation des inputs | 🔴 Critique | Routes sans middleware de validation |
| CORS `*` ouvert | 🟡 Moyen | `grep -rn "origin.*\*\|Access-Control.*\*"` |
| JWT secret faible/en dur | 🔴 Critique | `grep -rn "jwt.*secret\|JWT_SECRET"` dans le code |
| Pas de rate limiting | 🟡 Moyen | Absence de middleware limiteur |

---

## Utilisation dans le skill

Quand le langage principal est détecté, se référer à la section correspondante.
Pour les projets multi-langages, appliquer les sections de chaque langage présent.

**Commandes de détection rapide :**
```bash
# Détecter le langage principal
find /tmp/student-project -type f | grep -oE '\.[a-zA-Z]+$' | sort | uniq -c | sort -rn | head -5

# Vérifier l'absence de .gitignore
ls /tmp/student-project/.gitignore 2>/dev/null || echo "MANQUANT"

# Détecter node_modules commité
find /tmp/student-project -name "node_modules" -type d -not -path "*/.git/*"

# Détecter credentials en dur
grep -rn "password\s*=\|api_key\s*=\|secret\s*=\|JWT_SECRET\s*=" /tmp/student-project \
  --include="*.ts" --include="*.js" --include="*.py" --include="*.java" --include="*.go"
```

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| SQL concaténé (injection) | 🔴 Critique | `grep -rn "SELECT.*\+" ou "query.*\+"` |
| Pas de validation des inputs | 🔴 Critique | Routes sans middleware de validation |
| CORS `*` ouvert | 🟡 Moyen | `grep -rn "origin.*\*\|Access-Control.*\*"` |
| JWT secret faible/en dur | 🔴 Critique | `grep -rn "jwt.*secret\|JWT_SECRET"` dans le code |
| Pas de rate limiting | 🟡 Moyen | Absence de middleware limiteur |

---

## C# (.NET)

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `Console.WriteLine` de debug laissé | 🟢 Mineur | `grep -rn "Console\.WriteLine"` |
| Catch vide ou `catch (Exception)` trop large | 🟡 Moyen | `grep -Przn "catch\s*\(\s*Exception\s*\)|catch\s*\{\s*\}"` |
| Chaînes de connexion en dur | 🔴 Critique | `grep -rn "ConnectionString\s*=\s*\""` |
| Pas de `using` pour les ressources IDisposable | 🟡 Moyen | `new SqlConnection` sans `using` |
| `async void` (hors event handlers) | 🔴 Critique | `grep -rn "async void"` |
| Magic strings sans constante | 🟡 Moyen | Chaînes littérales répétées dans la logique métier |
| Accès direct aux champs publics | 🟡 Moyen | `grep -rn "public [A-Z][a-z]* [a-z]"` dans les modèles |

---

## Kotlin

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| `!!` (null assertion) non justifié | 🟡 Moyen | `grep -rn "!!"` |
| `lateinit var` sans vérification | 🟡 Moyen | `grep -rn "lateinit var"` |
| `println` de debug laissé | 🟢 Mineur | `grep -rn "println("` |
| Catch vide ou trop large | 🟡 Moyen | `grep -rn "catch (e: Exception)"` sans log |
| Coroutine leak (sans `SupervisorJob`) | 🔴 Critique | `CoroutineScope` sans gestion d'annulation |
| Secrets en dur dans les fichiers Kotlin | 🔴 Critique | `grep -Ern "val.*=.*\"[A-Za-z0-9+/]{20}"` |

---

## PHP

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| SQL par concaténation (injection) | 🔴 Critique | `grep -rn "query.*\\\$_"` ou `"SELECT.*\\."`  |
| `$_GET`/`$_POST` non validés | 🔴 Critique | `grep -rn "\\\$_GET\|\\\$_POST"` sans `filter_input` |
| `echo $_` direct (XSS) | 🔴 Critique | `grep -rn "echo \\\$_"` |
| `error_reporting(0)` en prod | 🟡 Moyen | `grep -rn "error_reporting(0)"` |
| Mots de passe en clair (pas de bcrypt) | 🔴 Critique | `grep -rn "md5\|sha1"` sur des mots de passe |
| `var_dump` / `print_r` de debug | 🟢 Mineur | `grep -rn "var_dump\|print_r"` |
| Inclusion dynamique non sécurisée | 🔴 Critique | `include \$_GET` ou `require \$_POST` |

---

## Bash / Shell

| Pattern | Sévérité | Détection |
|---------|----------|-----------|
| Absence de `set -euo pipefail` | 🟡 Moyen | Scripts sans ces options de sécurité en en-tête |
| Variables non quotées (`$VAR` vs `"$VAR"`) | 🟡 Moyen | `grep -rn "\$[A-Z_]*[^"]"` — risque word splitting |
| `eval` avec des données externes | 🔴 Critique | `grep -rnw "eval"` |
| Absence de vérification du code de retour | 🟡 Moyen | Commandes critiques sans `|| exit 1` ou `if` |
| Credentials en dur dans les scripts | 🔴 Critique | `grep -rn "PASSWORD=\|TOKEN=\|SECRET="` |
| Fichiers temporaires sans `trap` de nettoyage | 🟡 Moyen | `mktemp` sans `trap ... EXIT` |

---

## Langages non couverts

Si le langage principal du projet n'est pas listé ci-dessus, afficher l'avertissement suivant dans le rapport :

> ⚠️ **Aucune bad practice détectée pour le langage `[LANGAGE]` (non couvert).**
> L'absence de détection ne signifie **pas** l'absence de problèmes.
> Une revue manuelle du code est fortement recommandée.
