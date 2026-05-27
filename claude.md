# Claude Code — Ściągawka z komend

> **Źródło:** [docs.claude.com/en/docs/claude-code/overview](https://docs.claude.com/en/docs/claude-code/overview)  
> Weryfikuj z oficjalną dokumentacją, gdyż narzędzie jest aktywnie rozwijane.

---

## Spis treści

1. [Uruchamianie CLI](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#1-uruchamianie-cli)
2. [Flagi i opcje CLI](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#2-flagi-i-opcje-cli)
3. [Komendy slash (tryb interaktywny)](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#3-komendy-slash-tryb-interaktywny)
4. [Zarządzanie pamięcią i kontekstem](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#4-zarz%C4%85dzanie-pami%C4%99ci%C4%85-i-kontekstem)
5. [Komendy dotyczące uprawnień](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#5-komendy-dotycz%C4%85ce-uprawnie%C5%84)
6. [Praca z kodem i Git](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#6-praca-z-kodem-i-git)
7. [Konfiguracja i ustawienia](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#7-konfiguracja-i-ustawienia)
8. [Tryb nieinteraktywny (CI/CD)](https://claude.ai/chat/e84d0bea-6bc9-414a-87e6-668849ac9ff7#8-tryb-nieinteraktywny-cicd)

---

## 1. Uruchamianie CLI

|Komenda|Opis|
|---|---|
|`claude`|Uruchamia interaktywny tryb REPL (czat)|
|`claude "zapytanie"`|Jednorazowe zapytanie bez trybu interaktywnego|
|`claude -p "zapytanie"`|To samo co wyżej (`--print`) — wypisuje wynik i kończy|
|`claude --continue`|Wznawia ostatnią rozmowę|
|`claude -c`|Skrót dla `--continue`|
|`claude --resume`|Wznawia wybraną rozmowę (wyświetla listę)|
|`claude --version`|Wyświetla wersję narzędzia|
|`claude --help`|Wyświetla pomoc|

---

## 2. Flagi i opcje CLI

### Model i zachowanie

|Flaga|Opis|
|---|---|
|`--model <nazwa>`|Wybiera model (np. `claude-opus-4-5`, `claude-sonnet-4-5`)|
|`--max-turns <n>`|Maksymalna liczba tur agenta (domyślnie brak limitu)|
|`--output-format <format>`|Format wyjścia: `text` (domyślny), `json`, `stream-json`|
|`--verbose`|Szczegółowe logi (przydatne do debugowania)|
|`--no-color`|Wyłącza kolorowanie wyjścia (np. do potoków)|

### Kontekst i katalogi

|Flaga|Opis|
|---|---|
|`--add-dir <ścieżka>`|Dodaje dodatkowy katalog do kontekstu projektu|

### Narzędzia (Tools)

|Flaga|Opis|
|---|---|
|`--allowedTools <lista>`|Lista dozwolonych narzędzi, np. `Bash,Read,Write`|
|`--disallowedTools <lista>`|Lista zablokowanych narzędzi|
|`--dangerously-skip-permissions`|⚠️ Pomija wszystkie monity o uprawnienia (tylko zaufane środowiska!)|

### Wejście i wyjście

|Flaga|Opis|
|---|---|
|`--input-format <format>`|Format wejścia: `text` (domyślny) lub `stream-json`|
|`-` (stdin)|Odczytuje zapytanie ze standardowego wejścia, np. `echo "..." \| claude -p -`|

**Przykłady:**

```bash
# Jednorazowe zadanie z konkretnym modelem
claude -p "Napisz testy do funkcji parse_csv" --model claude-opus-4-5

# Tryb nieinteraktywny z wejściem z potoku
cat błędy.log | claude -p - --output-format json

# Ogranicz narzędzia do odczytu i basha
claude --allowedTools Bash,Read "Sprawdź zależności projektu"

# Wznów ostatnią rozmowę i kontynuuj pracę
claude --continue "Dodaj teraz dokumentację do tych funkcji"
```

---

## 3. Komendy slash (tryb interaktywny)

Wpisywane bezpośrednio w oknie czatu REPL (zaczynają się od `/`).

### Ogólne

|Komenda|Opis|
|---|---|
|`/help`|Wyświetla listę wszystkich komend slash|
|`/exit` lub `/quit`|Wychodzi z Claude Code|
|`/clear`|Czyści historię bieżącej rozmowy|
|`/compact`|Kompresuje rozmowę (tworzy podsumowanie, zwalnia okno kontekstu)|
|`/cost`|Wyświetla zużycie tokenów i szacowany koszt sesji|
|`/status`|Pokazuje status konta i sesji|
|`/release-notes`|Wyświetla informacje o ostatnich aktualizacjach|

### Projekt i pamięć

|Komenda|Opis|
|---|---|
|`/init`|Inicjalizuje plik `CLAUDE.md` w bieżącym projekcie|
|`/memory`|Wyświetla aktualnie załadowane pliki pamięci (CLAUDE.md itp.)|

### Konfiguracja

|Komenda|Opis|
|---|---|
|`/config`|Wyświetla i pozwala edytować konfigurację|
|`/model`|Przełącza model w trakcie sesji|
|`/permissions`|Wyświetla i zarządza uprawnieniami narzędzi|
|`/terminal-setup`|Konfiguruje integrację z terminalem (np. skróty klawiszowe)|

### Praca z kodem

|Komenda|Opis|
|---|---|
|`/review`|Prosi Claude o przegląd kodu (code review)|
|`/pr_comments`|Wyświetla komentarze z pull requesta|

### Konto

|Komenda|Opis|
|---|---|
|`/login`|Loguje się do konta Anthropic|
|`/logout`|Wylogowuje się|
|`/doctor`|Sprawdza środowisko (Node.js, zależności, połączenie)|

### Tryb edytora

|Komenda|Opis|
|---|---|
|`/vim`|Włącza tryb edycji vi/vim w polu wejściowym|

---

## 4. Zarządzanie pamięcią i kontekstem

Claude Code używa plików `CLAUDE.md` jako trwałej pamięci projektu.

|Akcja|Jak to zrobić|
|---|---|
|Utwórz pamięć projektu|`/init` — tworzy `CLAUDE.md` w katalogu głównym|
|Dodaj notatkę do pamięci|Poproś: _„Zapamiętaj, że używamy konwencji X"_|
|Wyświetl załadowane pliki|`/memory`|
|Globalna pamięć użytkownika|`~/.claude/CLAUDE.md` — stosuje się do wszystkich projektów|
|Kompresja kontekstu|`/compact` — skraca historię, gdy zbliżasz się do limitu|

**Hierarchia plików CLAUDE.md:**

```
~/.claude/CLAUDE.md          # globalne (użytkownik)
~/projekt/CLAUDE.md          # projekt (główny katalog)
~/projekt/src/CLAUDE.md      # podkatalog (opcjonalny)
```

---

## 5. Komendy dotyczące uprawnień

Claude Code prosi o zgodę przed wykonaniem potencjalnie niebezpiecznych operacji.

|Akcja|Opis|
|---|---|
|`y` / `Enter`|Zezwól na tę operację (jednorazowo)|
|`n`|Odrzuć operację|
|`a`|Zezwól na wszystkie podobne operacje w tej sesji|
|`s`|Pomiń (skip) tę operację|
|`/permissions`|Zarządzaj trwałymi zezwoleniami|
|`--dangerously-skip-permissions`|⚠️ Pomija wszystkie monity (niebezpieczne!)|

---

## 6. Praca z kodem i Git

Claude Code rozumie kontekst Git i może bezpośrednio pracować z repozytoriami.

**Przydatne polecenia naturalne (wpisywane jako zapytanie):**

```
# Code review bieżących zmian
"Przejrzyj moje zmiany i zaproponuj ulepszenia"

# Automatyczny commit
"Zrób commit z sensowną wiadomością opisującą zmiany"

# Praca z PR
"Podsumuj zmiany w tym pull requeście"
/pr_comments

# Debugowanie
"Znajdź błąd powodujący ten stack trace: <wklej błąd>"

# Pisanie testów
"Napisz testy jednostkowe dla pliku auth.py"
```

---

## 7. Konfiguracja i ustawienia

### Pliki konfiguracyjne

|Plik|Opis|
|---|---|
|`~/.claude/settings.json`|Globalne ustawienia użytkownika|
|`.claude/settings.json`|Ustawienia projektu (lokalne)|
|`.claude/settings.local.json`|Lokalne ustawienia projektu (nie commitować!)|
|`CLAUDE.md`|Pamięć i instrukcje dla projektu|

### Kluczowe opcje w `settings.json`

```json
{
  "model": "claude-sonnet-4-5",
  "theme": "dark",
  "autoUpdates": true,
  "permissions": {
    "allow": ["Bash(git:*)", "Read(**)", "Write(src/**)"],
    "deny": ["Bash(rm -rf:*)"]
  },
  "env": {
    "ANTHROPIC_API_KEY": "sk-ant-..."
  }
}
```

### Zmienne środowiskowe

|Zmienna|Opis|
|---|---|
|`ANTHROPIC_API_KEY`|Klucz API (wymagany, jeśli niezalogowany)|
|`ANTHROPIC_MODEL`|Domyślny model|
|`CLAUDE_CODE_MAX_OUTPUT_TOKENS`|Limit tokenów wyjściowych|
|`BASH_DEFAULT_TIMEOUT_MS`|Timeout dla poleceń Bash (ms)|
|`BASH_MAX_TIMEOUT_MS`|Maksymalny timeout dla Bash (ms)|
|`HTTP_PROXY` / `HTTPS_PROXY`|Konfiguracja proxy|

---

## 8. Tryb nieinteraktywny (CI/CD)

Idealny do automatyzacji, skryptów i pipeline'ów.

```bash
# Podstawowe użycie
claude -p "Zadanie do wykonania"

# Z wejściem z potoku
echo "Sprawdź bezpieczeństwo tego kodu: $(cat app.py)" | claude -p -

# Format JSON (do dalszego przetwarzania)
claude -p "Wygeneruj changelog" --output-format json | jq '.result'

# Strumieniowanie JSON
claude -p "Analizuj logi" --output-format stream-json

# Ograniczenie liczby tur agenta
claude -p "Zadanie złożone" --max-turns 10

# Pełna automatyzacja (bez pytań o uprawnienia)
claude -p "Zadanie" --dangerously-skip-permissions
```

**Przykładowy skrypt CI:**

```bash
#!/bin/bash
# Automatyczny code review przy każdym pushu
DIFF=$(git diff HEAD~1)
REVIEW=$(echo "Przejrzyj te zmiany:\n$DIFF" | claude -p - --output-format json)
echo "$REVIEW" | jq -r '.result'
```

---

## Szybka ściągawka

```
claude                          → tryb interaktywny
claude "zadanie"               → jednorazowe zadanie
claude -p "zadanie"            → wynik bez interakcji
claude -c                      → kontynuuj ostatnią rozmowę
claude --resume                → wybierz rozmowę do wznowienia
claude --model opus            → użyj konkretnego modelu

W trybie REPL:
/init      → utwórz CLAUDE.md
/compact   → zwolnij kontekst
/cost      → sprawdź zużycie
/clear     → wyczyść rozmowę
/help      → pełna lista komend
```

---

_Aktualna dokumentacja: [docs.claude.com/en/docs/claude-code/overview](https://docs.claude.com/en/docs/claude-code/overview)_