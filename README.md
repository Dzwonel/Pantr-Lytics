# PantrLytics (dodatek do Home Assistant)

Monitorowanie stanu zapasów z generowaniem etykiet na żądanie i drukowaniem IPP. Stworzony dla Home Assistant Ingress.

Licencja: Tylko do użytku osobistego (niekomercyjnego). Zobacz LICENCJE.

---

## Instalacja dodatku (Home Assistant)

1) **Dodaj repozytorium**: Nadzorca → Sklep z dodatkami → „Repozytoria” → `https://github.com/Dzwonel/Pantr-Lytics`.
2) **Instalacja**: Wybierz „PantrLytics” i zainstaluj.
3) **Mapowanie sieci (kluczowe dla kodów QR)**:
- Zmapuj port hosta na port kontenera `8099` (kontener zawsze nasłuchuje na 8099).
- Przykład: host `8099` → kontener `8099`. Niezależnie od tego, jaki port hosta mapujesz, musisz użyć go w `base_url`.
4) **Konfiguracja**:
- `base_url` (zalecane): Użyj bezpośredniego, osiągalnego adresu URL z poprawnym hostem i zmapowanym portem, np. `http://192.168.1.10:8099`. Unikaj adresów URL dla kodów QR. Jeśli używasz odwrotnego proxy z HTTPS, użyj tego adresu URL (np. `https://twoja.domena:443`).
- `ipp_host` / `ipp_printer` (opcjonalnie): Host IPP:port i nazwa kolejki do drukowania bezpośredniego. Jeśli nieustawione, akcje drukowania zwracają podgląd PNG.
- `serial_prefix` (opcjonalnie): Prefiks dla nowych numerów seryjnych/kodów kreskowych (domyślnie `USERconfigurable-`).
- `workers` (opcjonalnie): Pracownicy Gunicorn (domyślnie 2).
5) **Uruchom** dodatek. Otwórz przez „Open Web UI” (ingress) lub `http://<HA-host>:<mapowany-port>/`.
6) **Bezpieczny administrator**: Domyślne hasło administratora to `password`. Przejdź do Admin → „Hasło administratora” i natychmiast je zmień.
  
### Wyjaśnienie kodów QR i `base_url` (nie pomijaj)
- Kod QR koduje `base_url` + `/item/<id>`. Jeśli `base_url` jest błędny, skanowanie zakończy się niepowodzeniem (błąd 401, przekroczenie limitu czasu lub puste strony).
- Użyj adresu IP hosta HA w sieci LAN oraz zmapowanego portu: np. `http://192.168.1.10:8099`. Zawsze uwzględniaj port.
- Jeśli na początku używasz HTTPS (odwrotnego proxy), ustaw `https://twoja-domena:<port>`, aby kod QR pasował do punktu końcowego TLS.
- Adresy URL przychodzące (`/api/hassio_ingress/...`) wymagają krótkotrwałych tokenów i często przechodzą niepowodzenie podczas skanowania; preferowany jest adres bezpośrednio osiągalny.
- Po zmianie `base_url`, **ponownie wydrukuj etykiety**, aby kody QR zawierały nowy adres URL.

---

## Korzystanie z aplikacji (wszystkie strony i elementy sterujące)

### Strona główna (lista zapasów)
- Wyszukiwanie według nazwy, numeru seryjnego, kodu kreskowego, tagów, notatek, kategorii, lokalizacji, pojemnika, jednostki lub dat.
- Szybkie filtry: kategorie, lokalizacje, pojemniki, zużycie w ciągu, przyczyna wyczerpania oraz pokaż/ukryj wyczerpane.
- Kolumny: konfigurowalne w Administracja → Kolumny tabeli głównej (pokaż/ukryj/zmień kolejność).
- Ilość +/-: wyświetlana tylko dla jednostek oznaczonych jako **Regulowane** w Administracja → Jednostki. Po wyłączeniu, przyciski dla danej jednostki są ukrywane.

### Elementy
- **Utwórz**: Kliknij **Nowy element**; wprowadź nazwę, kategorię, lokalizację, pojemnik, ilość, jednostkę, stan, datę przydatności do spożycia, termin przydatności do spożycia, tagi, notatki i zdjęcia. Pola wymagane są zgodnie z ustawieniami administratora.
- **Edytuj**: Otwórz element → Edytuj. Można dodawać/zastępować zdjęcia; tagi/pola są aktualizowane.
- **Wyczerp/Odzyskaj**: Zaznacz wyczerpany element z podaniem przyczyny (ilość → 0). Odzyskaj przywraca ilość. Lista wyczerpanych elementów znajduje się pod napisem „Wyczerpany”.
- **Usuń**: Trwale usuń element i zdjęcia.

### Jednostki (obsługuje przyciski +/-)
- Administracja → Jednostki: dodaj/edytuj/usuń jednostki, przeciągnij, aby zmienić kolejność, przełącz **Regulowane**, aby kontrolować +/- widoczność.
- Każda jednostka już znajdująca się na przedmiotach (np. gramy) jest automatycznie dodawana do listy (domyślnie nie można jej zmienić), więc można ją przełączać.

### Etykiety
- Strona elementu: **Podgląd etykiety** (PNG) lub **Drukuj etykietę** (z liczbą kopii). Wiele kopii to jedno zadanie CUPS.
- Kod QR używa `base_url`. Jeśli skanowanie się nie powiedzie, popraw `base_url`/port i wydrukuj ponownie.

### Strona raportów
- **Horyzont**: 7/14/30/60/wszystkie dni dla ryzyka wygaśnięcia.
- **Wskaźniki KPI**: Wygasłe, ≤7 dni, ≤14 dni, ≤30 dni, ≤60 dni, suma w widoku.
- **Użycie w ramach zgodności**: % elementów w przewidzianym przedziale czasowym.
- **Wodospad starzenia**: Słupki rozkładu wieku.
- **Mapa cieplna**: Kategoria × Liczba lokalizacji dla elementów wygasających.
- **Wyczerpania**: Liczba, średnia liczba dni w magazynie, powody (kliknij, aby przejść do szczegółów), lista ostatnio wyczerpanych zapasów.
- **Nadchodzące ryzyko (25 najpopularniejszych)**: Najszybciej wygasające zapasy posortowane według liczby pozostałych dni.
  
### Sekcje administracyjne (wszystko konfigurowalne)
- **Kolumny głównej tabeli**: Pokaż/ukryj i zmień kolejność kolumn na stronie głównej.
- **Pola wymagane**: Wybierz, które pola są obowiązkowe przy tworzeniu/edycji.
- **Motyw**: Przełączanie między trybem jasnym i ciemnym.
- **Nagłówek aplikacji**: Własny tekst na stronie głównej.
- **Kategorie / Pojemniki / Lokalizacje / Użycie w**: Dodawaj, edytuj (modalnie), usuwaj i przeciągaj, aby zmienić kolejność. Zmieniaj nazwy i aktualizuj istniejące elementy.
- **Jednostki**: Dodawaj/edytuj/usuwaj, zmieniaj kolejność, przełączaj **Regulowane** (sterowanie przyciskami +/-). Automatycznie przechwytuje wszystkie jednostki widoczne przy elementach, aby można było nimi zarządzać.
- **Hasło administratora**: Zmień hasło domyślne.
  
### Kopia zapasowa, przywracanie i CSV
- **Strona kopii zapasowej**:
- Twórz kopie zapasowe w formacie ZIP (baza danych, zdjęcia, opcje, eksport CSV) i pobieraj.
- Przywracaj z pliku ZIP utworzonego przez aplikację.
- Usuń wszystkie elementy/zdjęcia (nieodwracalne).
- **Eksport/import CSV**:
- Eksport: wybierz pola i pobierz.
- Import: nagłówki muszą być zgodne z eksportem; brakujące numery seryjne są generowane automatycznie. Kategorie/pojemniki/lokalizacje/jednostki są wstawiane ponownie.

--

## Konfiguracja drukarki (IPP/CUPS przez sieć lokalną)

1) Udostępnij drukarkę przez CUPS/IPP:
- Włącz udostępnianie: `cupsctl --remote-admin --remote-any --share-printers`.
- Dodaj drukarkę w CUPS (`http://<cups-host>:631/admin`) i wydrukuj stronę testową.
- Zanotuj nazwę kolejki i port hosta (domyślnie 631).
2) W konfiguracji dodatku:
- `ipp_host`: np. `192.168.1.50:631`
- `ipp_printer`: nazwa kolejki, np. `DYMO_LabelWriter_450`
3) Uruchom ponownie dodatek. Wydrukuj z elementu; IPP wysyła bezpośrednio. Jeśli nieosiągalny/nieustawiony, otrzymasz plik PNG.
4) Rozwiązywanie problemów: sprawdź dostępność sieci LAN, nazwę kolejki, współdzielenie/zaporę CUPS i sprawdź logi dodatków pod kątem błędów IPP.

--

## Import/eksport CSV (Strona zapasowa)

- Eksport wybranych pól do pliku CSV.
- Import oczekuje pasujących nagłówków. Brakujące numery seryjne są generowane automatycznie; kategorie/pojemniki/lokalizacje/jednostki są wstawiane ponownie, jeśli są nowe.
- Komunikat o powodzeniu pokazuje dodane/pominięte elementy.

--
## Tworzenie lokalne (opcjonalnie)

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r app/requirements.txt
export DATA_DIR="$(pwd)/data" # opcjonalnie: przechowuj dane w repozytorium zamiast w /data
./run.sh # obsługuje http://localhost:8099
```
