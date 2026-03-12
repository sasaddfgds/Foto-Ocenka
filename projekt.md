Specyfikacja projektu: Platforma do oceniania zdjęć (Like/Dislike)
Opis: Aplikacja webowa do publikacji i oceniania obrazków. Pomysł jest prosty: userzy wrzucają fotki, reszta daje lajki albo dislajki. Główny ficzer to niestandardowe podejście do trzymania statyki, żeby totalnie wyzerować koszty hostingu obrazków.

 Stack technologiczny
Backend: PHP. Na start można polecieć na natywnych skryptach, ale composer to absolutny mus (choćby do autoloadingu klas i przyszłych paczek).

Frontend: Vanilla JS (ES6+). Żeby to jakoś sensownie budować i ułatwić sobie życie, wepniemy Vite.

Wygląd: HTML5 + CSS. Lecimy w semantykę i chociaż podstawową zgodność ze standardami WCAG (dostępność).

Baza danych: MariaDB (zarządzanie przez phpMyAdmin albo jakiegokolwiek innego klienta).

 Architektura i obsługa plików
Używamy tu GitHuba jako darmowego CDN-a. Rozwiązanie specyficzne, ale na pet-project — ujdzie.

Obrazy: Leżą wyłącznie w repozytorium na GitHubie. (Jeśli w przyszłości wpadnie moderacja, trzeba będzie ogarnąć tymczasowy zapis — katalog temp na serwerze przed pushem).

Rola Backendu: Działa wyłącznie jako proxy do zapisu. Odbiera plik od usera i za pomocą tokena pushuje go bezpośrednio do repo.

Rola Frontendu: Backend oddaje frontowi tylko metadane (linki) zapisane w bazie. Frontend ciągnie same obrazki bezpośrednio z GitHuba, omijając nasz serwer.

Analiza architektury: Używanie API GitHuba do hostowania obrazków to radykalnie darmowa prowizorka. Plusy — zerowe koszty za storage. Minusy — limity API GitHuba i potencjalny ban na repo, jeśli ruch będzie jak na Pornhubie. Alternatywa na przyszłość: storage kompatybilny z S3 (np. Cloudflare R2, mają cholernie spory darmowy tier).

 Logika biznesowa
Autoryzacja: Standardowa rejestracja i logowanie. Niezalogowani mogą tylko oglądać (albo w ogóle nic, jeśli zamkniemy widoki).

Strona główna (Feed): Wyświetlanie top 20 obrazków z największą liczbą lajków.

System ocen: User może dać tylko jeden lajk albo dislajk pod konkretną fotką. Ponowne kliknięcia albo cofają akcję, albo są twardo ignorowane (żeby jeden hejter nie nabił 40 dislajków).

Profile użytkowników: * Przy każdym zdjęciu jest awatar autora.

Po kliknięciu w awatar odpala się profil usera z galerią tylko jego wrzutek.

Bonus: Na profilu wyświetlamy zagregowane statystyki — łączną sumę wszystkich zebranych lajków i dislajków za wszystkie zdjęcia danego użytkownika.

Upload: User wysyła obrazek, backend generuje potrzebne metadane, wysyła plik na GitHuba, a link i dane autora zapisuje w bazie.

 Bonusy: Optymalizacja i Bezpieczeństwo
Optymalizacja obrazów: W locie konwertujemy wszystko do formatu .webp i robimy twardy resize do max 1920x1080. To krytyczne, żeby repo na GitHubie nie spuchło w dwa dni.

Rate Limiting: Zabezpieczenie przed spamem na endpoincie do uploadu. Np.: max 10 wrzutek na minutę od jednego zalogowanego usera.

Real-time: Używamy SSE (Server-Sent Events)  do odświeżania lajków/feedu w czasie rzeczywistym.

Analiza: SSE pasuje tu idealnie. Potrzebujemy tylko odbierać aktualizacje z serwera, dwukierunkowa komunikacja (WebSockets) to w tym przypadku kompletny overkill, a long-polling to już po prostu martwa technologia.

Bezpieczeństwo:

XSS: Twardy escape całego inputu od usera przy wyświetlaniu.

CSRF: Obowiązkowe tokeny do wszystkich zapytań POST/PUT/DELETE.

Hasła: Żadnego MD5, używamy natywnych funkcji PHP password_hash z solidnym algorytmem BCRYPT albo ARGON2.
