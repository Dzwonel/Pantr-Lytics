# Moje etykiety inwentarza — dodatek Home Assistant

Ten katalog zawiera dodatek Home Assistant Supervisor dla aplikacji `PantrLytics`.

Użycie
- Dodaj ten katalog `pantrlytics/` do swojego repozytorium (dodatek na poziomie głównym).
- W Home Assistant dodaj adres URL repozytorium w Sklepie z dodatkami → Repozytoria.
- Zainstaluj dodatek i uruchom go. Ponieważ dodatek korzysta z Ingress, otwórz jego interfejs sieciowy z poziomu Home Assistant.

Uwagi
- Dodatek oczekuje, że aplikacja połączy się z `0.0.0.0` i domyślnie użyje portu 5000.
- Jeśli nie chcesz, aby Supervisor zbudował obraz, zbuduj i prześlij obraz do rejestru (np. GHCR) i odwołaj się do klucza `image` w pliku `config.json`.

Testowanie lokalne

```bash
docker build -t pantrlytics:local .
docker run --rm -p 5000:5000 pantrlytics:local
# następnie odwiedź http://localhost:5000
```
