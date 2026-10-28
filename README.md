# HWDE — HackerOS Wayland Desktop Environment

Lekkie, nowoczesne i w pełni dostosowywalne środowisko graficzne dla **labwc**, napisane w całości w
**HackerScript** z biblioteką **gui** (pakiety zarządzane przez **bit**). Nazwa pochodzi z HackerOS, ale HWDE jest
**do codziennego użytku dla każdego** — zwykły pulpit, bez „hakerskich” akcentów.

* **Kompozytor:** labwc (okna, dekoracje, skróty, wejście). HWDE dokłada **powłokę**: panel, launcher, powiadomienia,
  szybkie ustawienia, kalendarz, menu zasilania, OSD, ekran blokady i tapetę.
* **Wbudowana jest tylko jedna aplikacja: Ustawienia.** Wszystko inne to Twoje programy.
* **Domyślny motyw:** szaro-czarny (ciemny) i biało-jasnoszary (jasny), z neutralnym akcentem; akcent można zmienić.
* **Lekko:** jedna binarka ~3 MB, ~14 MB RAM i ~0,2 % CPU w bezczynności (pomiar: powłoka na labwc headless).
* **Dostosowywalne:** jeden plik `hwde.conf`, własny `custom.css`, moduły panelu, skróty, autostart, wstawki `rc.xml`.

## Co jest w środku

| Polecenie | Co robi |
|---|---|
| `hwde shell` | panel (na każdym monitorze), tapeta, demon powiadomień (`org.freedesktop.Notifications`), blokada po bezczynności |
| `hwde launcher` | wyszukiwarka aplikacji (`.desktop`), historia użycia, `> polecenie` uruchamia komendę |
| `hwde popup quick` | szybkie ustawienia: Wi-Fi, Bluetooth, motyw, nie przeszkadzać, głośność, jasność, bateria |
| `hwde popup calendar` | kalendarz |
| `hwde power` | blokada / uśpienie / wylogowanie / restart / wyłączenie (klawisze L S O R P) |
| `hwde lock` | ekran blokady (`ext-session-lock`, hasło sprawdza `unix_chkpwd`) |
| `hwde osd volume +5` … | OSD głośności/jasności (pod klawisze multimedialne) |
| `hwde settings` | aplikacja Ustawienia (8 stron) |
| `hwde apply` | generuje konfigurację labwc z `hwde.conf` i przeładowuje labwc |
| `hwde notify "Tytuł" "Treść"` | wysyła powiadomienie |
| `hwde run terminal\|browser\|files\|editor\|screenshot` | uruchamia aplikację wg konfiguracji lub autodetekcji |

Okna pomocnicze (launcher, popupy, OSD) to osobne, krótkie procesy — ten sam skrót drugi raz je zamyka.
Resztę działającą ciągle (panel, powiadomienia) robi jeden proces `hwde shell`.

## Wymagania

* **labwc** ≥ 0.7 (przetestowane na 0.7.1), Wayland z `wlr-layer-shell` i `ext-session-lock`
* Do budowania: **HackerScript 0.5** (`hackerc`) + **Rust ≥ 1.86**; opcjonalnie **bit**
* Opcjonalnie (brak = dana funkcja po prostu znika): `wpctl` lub `pactl` (dźwięk), `brightnessctl`, `nmcli`,
  `bluetoothctl`/`rfkill`, `swaybg` (tapeta-obraz), `wlr-randr` (skala ekranu), `wlopm` (wygaszanie ekranu),
  `grim` + `slurp` (zrzuty), agent polkit, `xdg-desktop-portal-wlr`, `libxkbcommon` (układy klawiatury w aplikacjach gui)
* Blokada ekranu wymaga `unix_chkpwd` (pakiet z PAM: `pam`/`libpam-modules`) — jest w każdej typowej dystrybucji

## Budowanie i instalacja

```sh
scripts/build.sh                 # bit build, a bez bita: hackerc + cargo; wynik: ./hwde
sudo scripts/install.sh          # /usr/local + sesja "HWDE" w /usr/share/wayland-sessions
# albo tylko dla siebie:
scripts/install.sh --user
```

Wybierz sesję **HWDE** w menedżerze logowania albo z TTY uruchom `hwde-session`.
Test bez wylogowania (okno w bieżącej sesji Wayland): `scripts/nested.sh`.

Biblioteka `gui` leży w `vendor/gui` — to oryginalne `gui` z jedną małą poprawką (`patches/gui-key-events.patch`):
klawisze Esc i strzałki ↑/↓ są przekazywane aplikacji także wtedy, gdy fokus ma pole tekstowe (potrzebne w launcherze).
`Bit.hk` wskazuje ją jako zależność `path`.

## Konfiguracja w skrócie

Plik: `~/.config/hwde/hwde.conf` (INI). Aplikacja **Ustawienia** edytuje go na żywo — powłoka przeładowuje się sama,
a labwc dostaje świeże `rc.xml`/`themerc-override`. Pełny opis kluczy: [docs/CONFIG.md](docs/CONFIG.md),
sztuczki (CSS, moduły panelu, skróty): [docs/CUSTOMIZATION.md](docs/CUSTOMIZATION.md), budowa: [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

Domyślne skróty: `Super+Enter` terminal · `Super+D`/`Super+Spacja` launcher · `Super+E` pliki · `Super+B` przeglądarka ·
`Super+,` ustawienia · `Super+L` blokada · `Super+Q`/`Alt+F4` zamknij · `Super+F` pełny ekran · `Super+↑/↓/←/→` okno ·
`Alt+Tab` przełączanie · `Super+1…4` pulpity · `Ctrl+Alt+Del` zasilanie · `PrtSc` zrzut · klawisze głośności/jasności.

## Co przetestowano

Na labwc 0.7.1 (backend headless) z prawdziwym klientem Wayland: panel i tapeta (dark/light), launcher z nawigacją
klawiaturą i uruchamianiem aplikacji, lista okien, popupy (szybkie ustawienia, kalendarz, zasilanie), ekran blokady
(render, `ext-session-lock`), serwer powiadomień D-Bus (`busctl`, własny klient `hwde notify`), Ustawienia z kliknięciami
(przełączniki, suwaki, zmiana strony) i zapisem na żywo, generowanie `rc.xml`/motywu bez błędów w logu labwc.
**Nie przetestowano** na fizycznym sprzęcie: dźwięk, jasność, Wi-Fi/Bluetooth, bateria, wielomonitorowość,
poprawne odblokowanie hasłem (protokół `unix_chkpwd` sprawdzony ręcznie), skala ułamkowa.

## Licencja
MIT.
