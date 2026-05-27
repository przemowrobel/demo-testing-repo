## 1. Czym jest Git?

Git to rozproszony system kontroli wersji — pozwala śledzić zmiany w plikach, cofać się do wcześniejszych wersji i współpracować z innymi bez nadpisywania sobie nawzajem pracy.

---

## 2. Konfiguracja początkowa

```bash
git config --global user.name "Imię Nazwisko"
git config --global user.email "email@example.com"

# Sprawdzenie konfiguracji
git config --list
```

---

## 3. Tworzenie i klonowanie repozytorium

```bash
# Nowe repozytorium w istniejącym folderze
git init

# Klonowanie istniejącego repozytorium
git clone https://github.com/user/repo.git
```

---

## 4. Codzienny workflow — najważniejsze komendy

### Sprawdzanie stanu

```bash
git status          # co się zmieniło?
git log --oneline   # historia commitów (skrócona)
git diff            # różnice w niezaindeksowanych plikach
git diff --staged   # różnice w zaindeksowanych plikach (staged)
```

### Dodawanie i commitowanie zmian

```bash
git add plik.txt          # dodaj konkretny plik do staged
git add .                 # dodaj wszystkie zmiany
git commit -m "opis zmian"
```

> **Dobre praktyki commitów:** pisz krótko, konkretnie, w trybie rozkazującym, np. _"Dodaj walidację formularza logowania"_.

### Cofanie zmian

```bash
git restore plik.txt            # cofnij zmiany w pliku (przed staged)
git restore --staged plik.txt   # wycofaj plik ze staged (nie traci zmian)
git revert <hash>               # nowy commit cofający wskazany commit
git reset --soft HEAD~1         # cofnij ostatni commit, zmiany zostają w staged
git reset --hard HEAD~1         # cofnij ostatni commit i usuń zmiany (ostrożnie!)
```

---

## 5. Branche (gałęzie)

Branche pozwalają pracować nad funkcjonalnościami niezależnie od głównej linii kodu.

```bash
git branch                    # lista branchy
git branch nazwa-brancha      # utwórz nowy branch
git switch nazwa-brancha      # przełącz się na branch
git switch -c nazwa-brancha   # utwórz i przełącz się jednocześnie
```

### Usuwanie branchy lokalnych

```bash
git branch -d nazwa-brancha   # usuń branch (bezpieczne — tylko po zmergowaniu)
git branch -D nazwa-brancha   # wymuś usunięcie (nawet jeśli nie zmergowany)
```

> **Tip:** Flaga `-d` jest bezpieczna — Git nie pozwoli usunąć brancha, który nie został zmergowany. Użyj `-D` tylko gdy jesteś pewien, że chcesz wymusić usunięcie.

### Usuwanie branchy zdalnych

```bash
git push origin --delete nazwa-brancha   # usuń branch na zdalnym repo
git push origin :nazwa-brancha           # alternatywna (starsza) składnia
```

### Sprzątanie martwych referencji

Gdy zdalne branche zostają usunięte przez innych, lokalnie mogą zostawać "martwe" referencje do nich:

```bash
git fetch --prune   # pobierz zmiany i usuń martwe referencje

# Automatyczne pruning przy każdym fetch/pull
git config --global fetch.prune true
```

### Porządki z lokalnymi branchami

```bash
# Lista lokalnych branchy już zmergowanych do main (kandydaci do usunięcia)
git branch --merged main

# Usuń wszystkie lokalne zmergowane branche (oprócz main/master/develop)
git branch --merged main | grep -v -E "^\*|main|master|develop" | xargs git branch -d
```

### Mergowanie

```bash
git switch main
git merge nazwa-brancha
```

Jeśli Git nie może automatycznie połączyć zmian, powstaje **konflikt** — trzeba ręcznie wybrać, które zmiany zachować, a potem:

**VS Code (najszybsze rozwiązanie):**

1. VS Code ma wbudowany 3-way merge editor (od wersji 1.69)
2. Wystarczy skonfigurować jako domyślne narzędzie merge:

```bash
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd 'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'
```

Gdy będzie konflikt wystarczy wpisać w konsolę `git mergetool`

```bash
git add .
git commit
```

---

## 6. Praca ze zdalnym repozytorium (remote)

```bash
git remote -v                          # lista zdalnych repozytoriów
git remote add origin <url>            # dodaj zdalne repo

git push -u origin main                # wypchnij branch i ustaw śledzenie
git push                               # kolejne pushe (po ustawieniu upstream)

git fetch                              # pobierz zmiany bez mergowania
git pull                               # pobierz i zmerguj (fetch + merge)
```

> **`git fetch` vs `git pull`:** `fetch` pobiera zmiany, ale ich nie aplikuje — możesz je przejrzeć przed mergem. `pull` robi to automatycznie.

---

## 7. Stash — odkładanie zmian na bok

Przydatne, gdy chcesz szybko przełączyć branch, ale masz niezacommitowane zmiany.

```bash
git stash               # schowaj bieżące zmiany
git stash list           # lista schowków
git stash pop            # przywróć ostatni schowek i usuń go
git stash apply          # przywróć, ale zostaw w liście
git stash drop           # usuń schowek
```

---

## 8. Rebase (opcjonalnie, dla zaawansowanych)

Alternatywa dla merge — przepisuje historię tak, jakby zmiany powstały na szczycie docelowego brancha.

```bash
git switch feature-branch
git rebase main
```

> **Zasada:** nigdy nie rebase'uj branchy, które są publiczne / współdzielone z innymi.

---

## 9. Cherry-pick — przenoszenie wybranych commitów

Cherry-pick pozwala zastosować zmiany z konkretnego commita na bieżącym branchu — bez mergowania całej gałęzi.

**Jak to działa:** Git oblicza diff wybranego commita (różnicę między nim a jego rodzicem) i aplikuje ten diff w nowym miejscu. Powstaje **nowy commit z innym hashem**, bo ma innego parenta i inny timestamp. Oryginał na source branchu zostaje nienaruszony.

### Podstawowe użycie

```bash
# Znajdź hash commita na innym branchu
git log feature-branch --oneline

# Przejdź na branch docelowy
git switch main

# Zastosuj wybrany commit
git cherry-pick <hash>
```

### Kilka commitów naraz

```bash
# Wybrane commity (nie muszą być ciągłe)
git cherry-pick abc123 def456 ghi789

# Zakres commitów (bez pierwszego)
git cherry-pick abc123..ghi789

# Zakres włącznie z pierwszym
git cherry-pick abc123^..ghi789
```

### Przydatne flagi

```bash
# Nie commituj od razu — zostaw zmiany w staging area
git cherry-pick -n <hash>

# Dodaje "cherry picked from..." w opisie commita
git cherry-pick -x <hash>

# Po rozwiązaniu konfliktów — kontynuuj
git cherry-pick --continue

# Porzuć całą operację
git cherry-pick --abort
```

> **Kiedy używać:** cherry-pick sprawdza się przy przenoszeniu hotfixa z feature brancha na main, albo gdy chcesz wyciągnąć jeden konkretny commit bez reszty historii gałęzi. Jeśli chcesz przenieść całą serię commitów, rozważ rebase lub merge.

---

## 10. Git Worktree — kilka katalogów roboczych naraz

Worktree pozwala mieć **wiele branchy aktywnych jednocześnie** w osobnych folderach — bez przełączania się ani stashowania zmian.

Przydatne gdy: pracujesz nad feature'em i nagle musisz zbadać buga na innym branchu, albo chcesz porównać dwa branche obok siebie.

### Podstawowe użycie

```bash
# Dodaj nowy katalog roboczy na istniejącym branchu
git worktree add ../nazwa-folderu nazwa-brancha

# Dodaj nowy katalog roboczy i utwórz nowy branch jednocześnie
git worktree add -b nowy-branch ../nazwa-folderu main

# Lista aktywnych worktree
git worktree list

# Usuń worktree (najpierw usuń folder, potem posprzątaj)
git worktree remove ../nazwa-folderu
# lub samo sprzątanie referencji:
git worktree prune
```

### Przykładowy workflow

```bash
# Jesteś w środku pracy nad feature'em
# Nagle zgłoszono buga na main — bez stashowania:

git worktree add ../hotfix main
cd ../hotfix

# Tutaj działasz na branchu main w osobnym folderze
git switch -c fix/critical-bug
# ... naprawiasz, commitujesz, pushuje ...

# Wracasz do swojej pracy
cd ../projekt
# feature branch nienaruszony — żadnego stasha

# Po skończeniu — posprzątaj
git worktree remove ../hotfix
```

### Ograniczenia

- Każdy branch może być aktywny tylko w **jednym** worktree naraz — próba checkout tego samego brancha w dwóch miejscach zakończy się błędem.
- Wszystkie worktree współdzielą ten sam `.git` — commity, tagi i remote są wspólne.
- Worktree nie nadaje się do długotrwałych równoległych prac — do tego lepszy jest osobny klon repozytorium.

---

## 11. Przydatne narzędzia

```bash
git log --oneline --graph --all    # wizualizacja historii branchy
git blame plik.txt                 # kto zmienił każdą linię?
git cherry-pick <hash>             # zastosuj wybrany commit na bieżącym branchu
git tag v1.0.0                     # oznacz wersję tagiem
```

---

## 12. `.gitignore`

Plik `.gitignore` w katalogu głównym repozytorium mówi Gitowi, które pliki/foldery ignorować.

```
node_modules/
.env
dist/
*.log
.idea/
```

---

## 13. Pull Requesty na GitHub

Pull Request (PR) to mechanizm GitHub (nie samego Gita) umożliwiający **code review** i kontrolowane mergowanie zmian.

### Workflow krok po kroku

1. **Utwórz branch** pod nową funkcjonalność:
    
    ```bash
    git switch -c feature/nowa-funkcja
    ```
    
2. **Wprowadź zmiany**, commituj i wypchnij:
    
    ```bash
    git add .
    git commit -m "Dodaj nową funkcję X"
    git push -u origin feature/nowa-funkcja
    ```
    
3. **Otwórz Pull Request** na GitHubie:
    
    - Wejdź na stronę repozytorium → zakładka _Pull requests_ → _New pull request_
    - Wybierz base branch (np. `main`) i compare branch (twój feature branch)
    - Dodaj tytuł i opis zmian
    - Przypisz reviewerów
4. **Code review:**
    
    - Reviewerzy komentują, sugerują zmiany, zatwierdzają lub odrzucają
    - Autor może dorzucać kolejne commity na ten sam branch — PR aktualizuje się automatycznie
5. **Merge PR** — po zatwierdzeniu kliknij _Merge pull request_ na GitHubie. Opcje:
    
    - **Merge commit** — zachowuje pełną historię
    - **Squash and merge** — wszystkie commity z PR spłaszcza w jeden
    - **Rebase and merge** — przepisuje commity na szczyt base brancha
6. **Usuń branch** — po merge'u GitHub zaproponuje usunięcie brancha (dobra praktyka)
    

### Dobre praktyki PR-ów

- Małe, skupione PR-y (jedna funkcjonalność = jeden PR)
- Opisowy tytuł i szczegółowy opis (co, dlaczego, jak testować)
- Linkuj powiązane issues: `Closes #123`
- Nie merguj własnych PR-ów bez review (chyba że projekt jednoosobowy)
- Korzystaj z draft PR-ów dla pracy w toku

---

## Ściągawka — najczęstsze komendy

| Komenda                             | Opis                                        |
| ----------------------------------- | ------------------------------------------- |
| `git init`                          | Nowe repozytorium                           |
| `git clone <url>`                   | Klonuj repozytorium                         |
| `git status`                        | Sprawdź stan                                |
| `git add .`                         | Dodaj wszystko do staged                    |
| `git commit -m "msg"`               | Zapisz zmiany                               |
| `git push`                          | Wypchnij na remote                          |
| `git pull`                          | Pobierz i zmerguj                           |
| `git switch -c <branch>`            | Nowy branch + przełącz                      |
| `git merge <branch>`                | Zmerguj branch                              |
| `git branch -d <branch>`            | Usuń lokalny branch (po merge)              |
| `git branch -D <branch>`            | Wymuś usunięcie lokalnego brancha           |
| `git push origin --delete <branch>` | Usuń branch zdalny                          |
| `git fetch --prune`                 | Pobierz + usuń martwe zdalne referencje     |
| `git stash`                         | Schowaj zmiany                              |
| `git log --oneline`                 | Krótka historia                             |
| `git cherry-pick <hash>`            | Zastosuj wybrany commit na bieżącym branchu |
| `git worktree add <path> <branch>`  | Nowy katalog roboczy dla brancha            |
| `git worktree list`                 | Lista aktywnych worktree                    |