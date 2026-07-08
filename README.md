# Karta dostawy — porządkowanie danych

Prosta aplikacja webowa (jeden plik `index.html`, bez serwera i bez Pythona) do czyszczenia
i formatowania eksportów „Karta dostawy" z systemu ERP. Pracownik wchodzi na link, przeciąga
plik `.xls`/`.xlsx`, a aplikacja:

1. wykrywa tytuł dokumentu i wykrywa kolumny (Materiał, Dostawca, Ilość, Cena, Data/Status)
   na podstawie nagłówków — działa nawet, gdy układ nieznacznie się różni między plikami,
2. wyciąga wszystkie pozycje materiałowe w jedną płaską, czystą tabelę,
3. pozwala pobrać wynik jako **.xlsx** (do dalszej edycji) i jako **PDF sformatowany pod druk A4**.

Całość działa **lokalnie w przeglądarce pracownika** — plik nigdy nie jest wysyłany na żaden
serwer, więc hosting może być całkowicie darmowy (GitHub Pages).

## Jak to opublikować (5 minut, za darmo)

### 1. Załóż repozytorium na GitHub
- Wejdź na github.com → **New repository** → nadaj nazwę (np. `karta-dostawy`) → **Create repository**.

### 2. Wgraj te pliki
Wgraj do repozytorium całą zawartość tego folderu, zachowując strukturę:
```
index.html
.github/workflows/deploy.yml
README.md
```
Najprościej: na stronie repo kliknij **Add file → Upload files** i przeciągnij wszystkie pliki
i foldery (GitHub sam zachowa ścieżkę `.github/workflows/deploy.yml`).

Jeśli wolisz z terminala:
```bash
git init
git add .
git commit -m "Karta dostawy - pierwsza wersja"
git branch -M main
git remote add origin https://github.com/TWOJA-NAZWA/karta-dostawy.git
git push -u origin main
```

### 3. Włącz GitHub Pages z workflow
- W repozytorium wejdź w **Settings → Pages**.
- W sekcji **Build and deployment → Source** wybierz **GitHub Actions** (nie „Deploy from a branch").
- To wszystko — nic więcej nie trzeba klikać.

### 4. Poczekaj na pierwsze wdrożenie
- Wejdź w zakładkę **Actions** w repozytorium — zobaczysz uruchomiony workflow „Deploy do GitHub Pages".
- Po ok. 30–60 sekundach dostanie zielony ✓.
- Wróć do **Settings → Pages** — na górze pojawi się link, coś w stylu:
  `https://TWOJA-NAZWA.github.io/karta-dostawy/`

Ten link wyślij pracownikom — to jest cały interfejs. Każda kolejna zmiana w `index.html`
wypchnięta na branch `main` automatycznie się opublikuje (workflow uruchomi się sam).

## Jak to działa technicznie

- Parsowanie i zapis Excela: biblioteka [`xlsx-js-style`](https://github.com/gitbrent/xlsx-js-style)
  (fork SheetJS z obsługą formatowania komórek), wczytywana z CDN w `<script>`.
- Eksport do PDF: `jsPDF` + `jspdf-autotable`, też z CDN.
- Cała logika czyszczenia danych to zwykły JavaScript w `index.html` — żadnego backendu,
  żadnej bazy danych, żadnych kluczy API.

## Jeśli układ pliku źródłowego się zmieni

Parser szuka kolumn po treści nagłówków („Materiał", „Dostawca", „Ilość", „Cena",
„Data dostawy"), a nie po sztywnych numerach kolumn — więc drobne przesunięcia (dodatkowa
pusta kolumna, inna kolejność) zwykle nie są problemem. Jeśli jednak pojawi się plik o
naprawdę innym układzie i parser zwróci błąd „Nie znaleziono żadnych pozycji" albo dziwne
dane w podglądzie — wyślij mi przykładowy plik, dostosuję logikę wykrywania kolumn
(funkcja `findColumns` i `processMatrix` w `index.html`).

## Ograniczenia

- Aplikacja czyta **pierwszy arkusz** pliku. Jeśli dane są na innym arkuszu, trzeba by
  dodać wybór arkusza (mogę to dograć, jeśli będzie potrzebne).
- To narzędzie działa offline po wczytaniu strony raz (poza pierwszym pobraniem bibliotek
  z CDN) — jeśli pracownicy pracują bez internetu, biblioteki trzeba by hostować lokalnie
  w repozytorium zamiast z CDN (też mogę to przygotować).
