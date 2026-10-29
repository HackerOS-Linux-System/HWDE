# Tapety (HWDE 0.2)

| tryb (`wallpaper_mode`) | kto rysuje | opis |
|---|---|---|
| `gradient` | powłoka HWDE | gradient `color1` → `color2` pod kątem `angle`; w Ustawieniach 12 gotowych presetów |
| `color` | powłoka HWDE | jeden kolor (`color1`) |
| `image` | `swaybg` | jeden obraz (`image`), opcjonalnie osobny dla motywu ciemnego/jasnego |
| `slideshow` | `swaybg` | zmiana obrazu z folderu co `interval` minut (losowo lub po kolei) |

Bez `swaybg` tryby obrazkowe zamieniają się na gradient. Obsługiwane formaty: PNG, JPEG, WebP, BMP (zależnie od `swaybg`).

## Dopasowanie
`wallpaper_fit`: `fill` (wypełnij i przytnij), `fit` (z paskami w kolorze `color1`), `stretch`, `center`, `tile`.

## Obraz osobno dla motywu
`wallpaper_image_dark` / `wallpaper_image_light` (puste = użyj `wallpaper_image`). Przy `appearance_mode = auto`
tapeta zmienia się razem z porą dnia.

## Pokaz slajdów
`wallpaper_folder` (puste = wszystkie obrazy z `~/.local/share/hwde/wallpapers`, `/usr/share/hwde/wallpapers`,
`/usr/share/backgrounds`, `~/Pictures/Wallpapers`, `~/Pictures`), `wallpaper_interval` (minuty), `wallpaper_order`.
Bieżący obraz zapisuje plik `$XDG_RUNTIME_DIR/hwde-wallpaper`; jego czas modyfikacji odmierza odstęp,
więc restart powłoki nie zaczyna cyklu od nowa.

## Osobno dla monitora
`% wallpaper_outputs: str = "eDP-1=~/a.png;HDMI-A-1=~/b.png"` (nazwy z `wlr-randr`). Monitory bez wpisu dostają wspólną tapetę.

## Polecenia
```
hwde wallpaper set <plik>             obraz
hwde wallpaper color <#rrggbb>        jeden kolor
hwde wallpaper gradient <#c1> <#c2> [kąt]
hwde wallpaper preset <id>            graphite midnight ocean forest sunset aurora rose ember slate sand mint paper
hwde wallpaper folder <katalog>       pokaz slajdów z katalogu
hwde wallpaper slideshow              włącz pokaz slajdów
hwde wallpaper next | prev | random   kolejny / poprzedni / losowy obraz
hwde wallpaper list | current | reload
```
Domyślnie `Super+W` = `hwde wallpaper next`. Polecenia zapisują ustawienia do bloku `hwde-settings` w `hwde.conf`.
