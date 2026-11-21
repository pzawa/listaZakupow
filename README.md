# 🛒 Lista zakupów

Lekka, współdzielona lista zakupów dla 2 osób. Czysty HTML + JS, baza **Firebase Firestore** (darmowy plan Spark), hosting **GitHub Pages**. Zmiany synchronizują się na żywo między telefonami.

## Konfiguracja Firebase (jednorazowo, ok. 10 min)

1. Wejdź na https://console.firebase.google.com → **Dodaj projekt** (Google Analytics można wyłączyć).
2. **Build → Authentication → Get started → Sign-in method → Email/Password → Włącz.**
3. **Authentication → Users → Add user**: dodaj dwa konta (Ty i mąż) z hasłami.
4. **Build → Firestore Database → Create database** → wybierz lokalizację `eur3 (europe-west)` → tryb produkcyjny.
5. **Firestore → Rules**: wklej zawartość pliku `firestore.rules`, **podmień dwa adresy e-mail** na wasze i kliknij **Publish**.
6. **Ustawienia projektu (⚙️) → Twoje aplikacje → ikona `</>`** → zarejestruj aplikację webową → skopiuj obiekt `firebaseConfig` do pliku `firebase-config.js`.
7. **Authentication → Settings → Authorized domains** → dodaj `TWOJ-LOGIN.github.io`.

## Publikacja na GitHub Pages

1. Wypchnij repozytorium na GitHub.
2. Repo → **Settings → Pages → Source: Deploy from a branch → `main` / `(root)` → Save**.
3. Po minucie aplikacja będzie pod adresem `https://TWOJ-LOGIN.github.io/listaZakupow/`.

Na telefonie: otwórz stronę → „Dodaj do ekranu głównego” – działa jak aplikacja.

## Lokalne uruchomienie

Moduły ES wymagają serwera (nie działa przez `file://`), np.:

```
npx serve .
```
lub w PhpStorm: prawy klik na `index.html` → Open in Browser.
