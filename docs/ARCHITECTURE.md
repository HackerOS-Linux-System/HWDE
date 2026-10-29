# Architektura

```
labwc  ── okna, dekoracje, skróty, wejście (rc.xml, themerc-override generowane przez HWDE)
  └─ autostart: hwde autostart
        ├─ skala ekranu (wlr-randr), agent polkit, polecenia [autostart]
        └─ hwde shell  (restartowany po awarii)
             ├─ okna layer-shell: panel (1 na monitor), tapeta, powiadomienia
             ├─ wątek D-Bus: org.freedesktop.Notifications (własny, minimalny protokół)
             └─ ext-idle-notify → hwde lock / wygaszanie ekranu
  krótkie procesy: hwde launcher | popup | power | lock | osd | settings
```

Jedna binarka `hwde` z podpolecenia­mi. Pliki źródłowe (`cmd/`):

| plik | zawartość |
|---|---|
| `main.hcs` | dyspozytor podpoleceń |
| `sys.hcs` | warstwa natywna (`native {Rust}`): czas, unix_chkpwd, instancje (pidfile), klient i serwer D-Bus |
| `conf.hcs` | `hwde.conf` jako skrypt Hacker Langa (wykonanie przez `hl`, zapis bloku `hwde-settings`, migracja INI, kopie zapasowe), tłumaczenia `tr()`, motyw (paleta szaro-czarna/biała), CSS |
| `wallpaper.hcs` | tapety: presety, obrazy (`swaybg`), pokaz slajdów, per monitor, `hwde wallpaper` |
| `sysinfo.hcs` | dźwięk, jasność, sieć, Bluetooth, bateria, zasilanie, aplikacje domyślne |
| `shell.hcs` | panel, moduły, tapeta, powiadomienia, pętla powłoki |
| `popups.hcs` | launcher, szybkie ustawienia, kalendarz, zasilanie, OSD |
| `lock.hcs` | ekran blokady |
| `labwc.hcs` | generator `rc.xml`, `themerc-override`, `menu.xml`, `environment`, `autostart`; `hwde autostart` |
| `settings.hcs` | aplikacja Ustawienia (9 stron, wyszukiwarka) |

Wydajność: powłoka śpi w `poll` i budzi się co sekundę (zegar); proces ~14 MB RSS. Popupy żyją tylko, gdy są otwarte.
Powiadomienia mogą pojawić się z opóźnieniem do 1 s (tyk powłoki), bo biblioteka gui nie ma jeszcze budzenia z wątku.

## Znane ograniczenia
* Brak ikon aplikacji w launcherze (gui rysuje tylko wbudowane ikony Lucide) — pokazywane są awatary z inicjałem.
* Brak obsługi akcji i markupu powiadomień (tylko tytuł, treść, pilność).
* Brak apletów tray (StatusNotifierItem) i widżetu sieci/Bluetooth z listą urządzeń — użyj `nmtui`/`bluetoothctl` lub własnych narzędzi.
* Tapeta-obraz przez `swaybg`; gradient/kolor rysuje sama powłoka.

## Konfiguracja w Hacker Langu (0.2)
`conf_load()` skanuje `hwde.conf` w poszukiwaniu nazw zmiennych (`% nazwa`, `::set nazwa`), uruchamia `hl -c` z krótkim
programem, który importuje plik (`<< hwde.conf`) i drukuje wartość każdej zmiennej (trzy linie na zmienną: nazwa, `::type`, wartość —
typ `nil` oznacza zmienną nieustawioną). Dzięki temu warunki i obliczenia w pliku są wykonywane przez interpreter, a HWDE dostaje gotowe wartości.
