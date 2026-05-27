# Zarządzanie sesjami i branchami w Claude Code — praktyczny przewodnik

## Czym są sesje?

Każda rozmowa z Claude Code to **sesja** — plik JSONL przechowywany lokalnie w:
```
~/.claude/projects/<ścieżka-projektu>/
```

Sesja zawiera pełną historię rozmowy, użyte narzędzia i snapshoty plików sprzed edycji. Sesje są **niezależne** — każda nowa sesja startuje z czystym kontekstem.

---

## Tworzenie i nazewnictwo sesji

```bash
# Nowa sesja bez nazwy (auto-generowana)
claude

# Nowa sesja z nazwą od razu
claude -n auth-refactor

# Zmiana nazwy w trakcie sesji
/rename auth-refactor
```

Nadawaj nazwy sesjom od razu — znacznie ułatwia późniejsze wznowienie.

---

## Wznawianie sesji

```bash
# Wznów ostatnią sesję w bieżącym katalogu
claude --continue

# Otwórz interaktywny picker sesji
claude --resume
```

### Picker — skróty klawiaturowe

| Skrót | Akcja |
|-------|-------|
| `↑` / `↓` | Nawigacja po sesjach |
| `Enter` | Wznów zaznaczoną sesję |
| `Space` | Podgląd zawartości sesji |
| `/` lub litera | Wyszukiwanie po nazwie |
| `Ctrl+R` | Zmień nazwę sesji |
| `Ctrl+B` | Filtruj do sesji z bieżącego git brancha |
| `Ctrl+W` | Sesje ze wszystkich worktrees |
| `Ctrl+A` | Sesje ze wszystkich projektów |
| `Esc` | Wyjdź z wyszukiwania / pickera |

### Wznowienie z wnętrza sesji

```
/resume    ← otwiera picker bez wychodzenia z Claude
```

---

## Branching / forking sesji

Branch = nowa sesja z całą historią do bieżącego punktu. Oryginał pozostaje nienaruszony.

```bash
# Z wnętrza sesji — utwórz branch
/branch
/branch nazwa-brancha

# Alias
/fork

# Z linii poleceń — wznów ostatnią sesję jako fork
claude --continue --fork-session
```

### Kiedy używać?

- Chcesz wypróbować dwa różne podejścia do tego samego problemu
- Chcesz zachować "checkpoint" przed ryzykowną zmianą
- Eksperymentujesz z architekturą i chcesz móc wrócić

### Typowy workflow

```
1. Pracujesz nad zadaniem w sesji "feature-x"
2. Dochodzisz do rozwidlenia — dwa możliwe podejścia
3. /branch podejscie-a        ← tworzysz branch, kontynuujesz w nim
4. Podejście A nie wychodzi
5. /resume                    ← wracasz do pickera
6. Wybierasz oryginalną sesję "feature-x"
7. /branch podejscie-b        ← próbujesz drugie podejście
```

---

## Worktrees — równoległa praca na tym samym repo

Worktrees to izolowane kopie repo w osobnych katalogach, każda z własną sesją Claude.

```bash
# Utwórz worktree z nazwą
claude --worktree feature-payments

# Utwórz z auto-nazwą
claude --worktree

# W drugim terminalu — osobny worktree
claude --worktree bugfix-login
```

Worktrees tworzone są w `.claude/worktrees/<nazwa>/`, każdy na osobnym git branchu `worktree-<nazwa>`.

**Sprzątanie automatyczne:**
- Brak zmian → worktree usuwany automatycznie
- Są zmiany → Claude pyta czy zachować

---

## Praktyczne scenariusze

### Scenariusz 1: Długa praca rozłożona na dni

```bash
# Dzień 1 — zacznij i nazwij sesję
claude -n pfbp-554-deploy-fix

# Dzień 2 — wróć do pracy
claude --resume pfbp-554-deploy-fix
# lub po prostu:
claude --continue   # jeśli to ostatnia sesja w tym katalogu
```

### Scenariusz 2: Code review + nowa funkcja równolegle

```bash
# Terminal 1 — review PR
claude --worktree review-pr-123

# Terminal 2 — nowa funkcja
claude --worktree feature-new-endpoint
```

### Scenariusz 3: Eksperyment z architekturą

```bash
# Jesteś w sesji "main-work", doszedłeś do decyzji architektonicznej
/branch architektura-a
# ... próbujesz podejście A ...
# nie wyszło, wróć
/resume
# wybierz "main-work"
/branch architektura-b
# ... próbujesz podejście B ...
```

---

## Ograniczenia

| Operacja | Dostępna? |
|----------|-----------|
| Lista sesji | Tylko przez picker (`claude --resume`) |
| Merge sesji | Nie — brak takiej operacji |
| Usuwanie przez CLI | Nie — ręcznie w `~/.claude/projects/` |
| Uprawnienia z poprzedniej sesji | Nie przenoszone przy wznowieniu |

### Usuwanie sesji ręcznie

```bash
# Lokalizacja plików sesji
ls ~/.claude/projects/
```

Każda sesja to plik `.jsonl`. Można je usunąć ręcznie lub wyczyścić przez ustawienia Claude Code.

---

## Zarządzanie kontekstem

Gdy sesja robi się długa, Claude automatycznie kompaktuje historię (usuwa stare wyniki narzędzi, streszcza rozmowę). Można to wymusić ręcznie:

```
/compact                     ← kompaktuj z domyślnym focus
/compact "zachowaj architekturę auth"   ← z instrukcją co zachować
/context                     ← sprawdź co zajmuje kontekst
```
 