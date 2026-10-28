# Konfiguracja — `~/.config/hwde/hwde.conf`

Składnia INI: `[sekcja]`, `klucz = wartość`, komentarze `#`. Brakujące klucze mają wartości domyślne.
`hwde config` wypisuje pełną, aktualną konfigurację. Powłoka sprawdza plik co sekundę i przeładowuje się sama;
labwc dostaje zmiany przez `hwde apply` (robią to Ustawienia po ok. 0,6 s od ostatniej zmiany).

## general
| klucz | wartości | opis |
|---|---|---|
| `language` | `auto`, `pl`, `en` | język interfejsu (`auto` = z `LANG`) |

## appearance
| klucz | domyślnie | opis |
|---|---|---|
| `mode` | `dark` | `dark`, `light`, `auto` (jasny 7–19, ciemny w nocy) |
| `accent` | `neutral` | `neutral`, `blue`, `teal`, `green`, `yellow`, `orange`, `red`, `pink`, `purple` albo `#rrggbb` |
| `radius` | `10` | zaokrąglenie elementów powłoki (px) |
| `font_scale` | `100` | skala czcionek powłoki (%) |
| `panel_opacity` | `94` | nieprzezroczystość tła panelu (%) |
| `animations` | `1` | animacje hover/press/focus |

## panel
| klucz | domyślnie | opis |
|---|---|---|
| `position` | `top` | `top` / `bottom` |
| `floating` | `1` | pływający, zaokrąglony panel z marginesem |
| `height`, `margin` | `38`, `6` | wymiary (px) |
| `left`, `center`, `right` | `launcher,tasks` / `clock` / `status,power` | moduły: `launcher`, `tasks`, `clock`, `date`, `status`, `power`, `lock`, `settings` |
| `clock` | `24h` | `24h` / `12h` |
| `seconds`, `date` | `0`, `1` | sekundy, data obok zegara |
| `max_tasks` | `8` | ile okien na pasku zadań |

## wallpaper
`mode` = `gradient` (kolory `color1` → `color2`, kąt `angle`), `color` (jeden kolor) lub `image` (plik `image`, wymaga `swaybg`).
Puste `color1`/`color2` = kolory zależne od motywu.

## windows (labwc)
`titlebar` (np. `icon:iconify,max,close`, `close,iconify,max:` — lewe:prawe), `corner_radius`, `border`, `shadows`, `gap`,
`snap`, `focus_follows_mouse`, `workspaces`, `font`, `font_size`.

## input
`layout`, `variant`, `options` (XKB; zmiana po ponownym zalogowaniu — trafia do `~/.config/labwc/environment`),
`repeat_rate`, `repeat_delay`, `numlock`, `tap`, `natural_scroll`, `pointer_speed` (−1…1), `accel` (`adaptive`/`flat`),
`disable_while_typing`.

## display
`display.scale.NAZWA = 1.5` — skala monitora, stosowana przez `wlr-randr` przy starcie sesji i w Ustawieniach
(nazwy monitorów: `wlr-randr`).

## notify
`enabled`, `dnd` (nie przeszkadzać), `timeout` (s), `max` (ile naraz). Pilne (urgency = critical) nie znikają same.

## lock
`timeout` — minuty bezczynności do blokady (0 = wyłączone), `screen_off` — minuty do wygaszenia ekranu (`wlopm`).

## apps
`terminal`, `browser`, `files`, `editor` — polecenia używane przez `hwde run …`. Puste = autodetekcja.

## keys — skróty klawiszowe
`KLAWISZ = exec:polecenie` albo `KLAWISZ = action:NazwaAkcjiLabwc[:atrybut=wartość…]`.
Modyfikatory labwc: `W` Super, `A` Alt, `C` Ctrl, `S` Shift (`W-S-q`). Przykłady:

```ini
[keys]
W-t = exec:hwde run terminal
W-Left = action:SnapToEdge:direction=left
W-2 = action:GoToDesktop:to=2
XF86AudioMute = exec:hwde osd mute
# pusta wartość wyłącza skrót domyślny:
W-q =
```

## autostart
Dowolne klucze `a001`, `a002`… z poleceniami uruchamianymi przy logowaniu (kolejność alfabetyczna):

```ini
[autostart]
a001 = nm-applet --indicator
a002 = sh -c 'sleep 2 && firefox'
```
