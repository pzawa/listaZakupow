# CLAUDE.md – notatki dla Claude Code

Plik wczytuje się automatycznie na początku każdej sesji. Opis funkcji dla ludzi jest w `README.md`;
tutaj są decyzje, konwencje i pułapki, których nie widać od razu w kodzie.
**Aktualizuj ten plik po każdej zmianie, która wprowadza nową decyzję, konwencję lub pułapkę.**

## Projekt w skrócie

- Współdzielona lista zakupów dla 2 osób, używana głównie na telefonach jako PWA („Dodaj do ekranu głównego”).
- Czysty HTML + CSS + JS (moduły ES), **bez bundlera, bez npm, bez frameworka**. Cała aplikacja to jeden plik `index.html` (style w `<style>`, logika w `<script type="module">`).
- Firebase JS SDK ładowany z CDN (`https://www.gstatic.com/firebasejs/10.12.2/...`). Przy zmianie wersji – zmień ją we wszystkich importach naraz.
- Hosting: GitHub Pages z gałęzi `main` (root). Firebase project: `shopping-list-c45c2`.
- Lokalnie: `npx serve .` (moduły ES nie działają przez `file://`).

## Pliki

- `index.html` – cały UI i logika.
- `firebase-config.js` – konfiguracja Firebase (dane publiczne, nie są sekretem; dostęp chronią reguły i logowanie).
- `firestore.rules` – reguły bezpieczeństwa. W repo są **przykładowe e-maile** (`ja@example.com`, `maz@example.com`); prawdziwe są wpisane tylko w konsoli Firebase. Nie commituj prawdziwych adresów.
- `manifest.json` – manifest PWA (kolory zgodne z motywem granatowym).

## Dane w Firestore

| Kolekcja | Pola | Uwagi |
|---|---|---|
| `items` | `name` (string ≤200), `done` (bool), `order` (number), `createdAt` | Lista zakupów |
| `products` | `name`, `count` (number), `lastUsed` | Pamięć produktów do podpowiedzi; ID dokumentu = `encodeURIComponent(fold(name))` |
| `cards` | `name` (≤50), `image` (data URL `data:image/jpeg;base64,...`, ≤1 000 000 znaków), `createdAt` | Karty klienta |

**Każda zmiana pól/kolekcji wymaga zmiany `firestore.rules`**, a potem reguły trzeba ręcznie wkleić w konsoli Firebase i kliknąć Publish. Zawsze o tym przypominaj.

## Ważne decyzje i pułapki

- **Offline:** `initializeFirestore(app, { localCache: persistentLocalCache() })` – lista i karty działają w sklepie przy słabym zasięgu. Nie zamieniaj na `getFirestore()`.
- **Kolejność produktów:** pole `order`. Przy przeciąganiu nowe `order` = środek między sąsiadami (`placeItem`), więc zapisujemy tylko jeden dokument. Stare dokumenty bez `order` sortują się wg `createdAt` (`sortKey`). Przeciąganie za uchwyt ⇅, na komputerze także ↑/↓.
- **Podpowiedzi:** funkcja `fold()` ignoruje wielkość liter i polskie znaki (ł → l, NFD + usunięcie diakrytyków). Maks. 6 podpowiedzi: najpierw pasujące od początku, potem wg `count`. Nie podpowiadamy produktów, które już są na liście (nieodhaczone). `pointerdown` na liście podpowiedzi ma `preventDefault`, żeby klawiatura na telefonie się nie chowała.
- **Karty klienta w Firestore, nie w Storage:** Firebase Storage nie jest dostępny dla nowych projektów na darmowym planie Spark. Dlatego screen jest w przeglądarce kompresowany do JPEG (`compressImage`, limit `MAX_IMAGE_CHARS = 900_000`, by zmieścić się w limicie 1 MB dokumentu) i zapisywany jako data URL. Przezroczyste PNG dostają białe tło.
- **Zakładki** (Lista / Karty) – ostatnio wybrana zapamiętywana w `localStorage` (`tab`).
- **Plan darmowy (Spark):** nie proponuj rozwiązań wymagających płatnego planu (Cloud Functions, Storage itp.).

## Konwencje

- Cały interfejs, komentarze w kodzie, README i komunikaty commitów **po polsku**.
- Kolorystyka granatowa – zmienne CSS w `:root` (`--navy`, `--primary`, `--accent`, `--bg`, ...). Nowe kolory dodawaj jako zmienne.
- DOM budowany przez `document.createElement` / `textContent` (nie `innerHTML` z danymi użytkownika).
- Odmiana liczebników przez helper `plural(n, one, few, many)`.
- Po dodaniu/zmianie funkcji zaktualizuj sekcję „Funkcje” w `README.md`.

## Historia zmian (najważniejsze)

- 2026-11: start – lista z Firebase Firestore, logowanie e-mail/hasło, PWA.
- Pasek postępu zakupów z licznikiem pozostałych produktów.
- Karty klienta, granatowa kolorystyka, podpowiedzi produktów.
- Zmiana kolejności produktów (przeciąganie), nowy wygląd zakładek, README.
- 2026-11-25: dodany ten plik `CLAUDE.md`.
