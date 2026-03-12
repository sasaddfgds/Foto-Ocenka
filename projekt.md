Specyfikacja Projektu: ImageRate (Like/Dislike Platform)
Aplikacja webowa do publikacji i oceniania obrazków. Kluczowy koncept: całkowite wyzerowanie kosztów hostingu poprzez wykorzystanie infrastruktury zewnętrznej jako darmowego CDN-a.

🛠 Stack Technologiczny
Backend: PHP (Native + Composer do autoloadingu i paczek). Żadnych ciężkich frameworków, czysta wydajność.

Frontend: Vanilla JS (ES6+). Do budowania używamy Vite — bo życie jest za krótkie na konfigurowanie Webpacka.

Wygląd: Semantyczny HTML5 + CSS. Zgodność z WCAG (dostępność), żeby każdy mógł hejtować zdjęcia bez barier.

Baza danych: MariaDB.

🏗 Architektura i Obsługa Plików
Projekt opiera się na "partyzanckim" wykorzystaniu GitHuba jako storage'u.

GitHub jako CDN: Obrazy nie obciążają naszego serwera. Leżą w dedykowanym repozytorium.

Backend (Proxy): Odbiera plik od usera, wykonuje push przez API GitHuba bezpośrednio do repo.

Frontend: Dostaje od bazy danych tylko URL-e. Przeglądarka ciągnie grafiki prosto z serwerów GitHuba.

Analiza: Rozwiązanie radykalnie tanie (0 PLN). Przy ogromnym ruchu grozi banem od GitHuba.
Skalowalność: W razie sukcesu migracja na Cloudflare R2 (ogromny darmowy tier, pełna kompatybilność z S3).

⚙️ Mechanika Systemu
Autoryzacja: Rejestracja/Logowanie. Goście mają tylko tryb "Read-only".

Feed: Top 20 obrazków sortowanych po rankingu (Like/Dislike).

System Ocen: Jeden głos na usera. Ponowne kliknięcie to undo. Logika po stronie backendu blokuje nabijanie statystyk przez boty.

Profile: Galeria autorska + zagregowane statystyki popularności (suma wszystkich ocen użytkownika).

Upload: Automatyczne generowanie metadanych i natychmiastowa wysyłka do "CDN".

⚡ Optymalizacja i Bezpieczeństwo
Żadnej prowizorki w kwestiach krytycznych:

Obróbka obrazu: Obowiązkowa konwersja do .webp + twardy resize (max 1920x1080). Oszczędzamy miejsce i transfer.

Rate Limiting: Max 10 wrzutek/min na usera. Spamowanie uploadem zabija każde API.

Real-time: Odświeżanie ocen przez SSE (Server-Sent Events). WebSockety to w tym przypadku przerost formy nad treścią (overkill).

Bezpieczeństwo (Zero tolerancji):
XSS: Bezwzględny escape całego inputu przy renderowaniu.

CSRF: Tokeny dla każdej akcji zmieniającej stan (POST/PUT/DELETE).

Hasła: Zapomnij o MD5/SHA1. Używamy password_hash z algorytmem Argon2 lub BCRYPT.
