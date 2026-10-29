# Konfiguracja — `~/.config/hwde/hwde.conf` (Hacker Lang)

Od wersji **0.2** `hwde.conf` nie jest plikiem INI, tylko **skryptem Hacker Langa** — tak jak `init.lua` w Neovim czy
awesome. Plik wykonuje interpreter `hl` (w HackerOS jest zawsze), więc obok zwykłych ustawień możesz używać warunków,
`::env`, `::set`, wyniku komend (`> polecenie |> @zmienna`), pętli i funkcji.

```hl
#!/usr/bin/hl
using <gen 2>

% appearance_mode: str = "dark"
% panel_height: int = 40

> hostname |> @host
? switch @host
| laptop
    % panel_height: int = 44
done
```

## Jak to działa
* Ustawienie to zmienna `sekcja_klucz` (`panel_height` = klucz `height` w sekcji `panel`). Brakujące mają wartości domyślne.
* HWDE wykonuje plik przez `hl`, po czym odczytuje wartości wszystkich zmiennych przypisanych w pliku (`% nazwa …`
  oraz `::set nazwa …`). Zmienne pomocnicze (np. `host`, `home`) są ignorowane — liczą się tylko nazwy z sekcjami z tabeli niżej.
* **Bez `hl`** (np. test poza HackerOS) działa uproszczony czytnik: tylko statyczne przypisania `% nazwa = wartość`.
* `hwde check` sprawdza składnię (`hl check`), `hwde config` wypisuje pełną, aktualną konfigurację jako plik HL.
* Powłoka sprawdza plik co sekundę i przeładowuje się sama; labwc dostaje zmiany przez `hwde apply`.

## Aplikacja Ustawienia a Twój kod
Ustawienia zapisują **wyłącznie blok** między znacznikami:

```hl
;; >>> hwde-settings >>>
…zmienne zarządzane przez Ustawienia…
;; <<< hwde-settings <<<
```

Wszystko poza blokiem (zwłaszcza **pod** nim) jest Twoje i nigdy nie jest ruszane; ponieważ HL wykonuje plik od góry,
Twoje przypisania pod blokiem nadpisują blok. Blok trzyma pełny stan, więc nie edytuj go ręcznie.
Stary plik INI (v0.1) jest przy pierwszym uruchomieniu automatycznie zamieniany na HL, a oryginał zostaje jako `hwde.conf.ini.bak`.

## Typy i cudzysłowy
Liczby: `% panel_height: int = 38`. Teksty: `% panel_left: str = "launcher,tasks"`. Przełączniki to `1`/`0`.
Wartości ze spacjami i znakami specjalnymi bierz w cudzysłów.

## Klucze

### general
`general_language` — `auto`, `pl`, `en`.

### appearance
| zmienna | domyślnie | opis |
|---|---|---|
| `appearance_mode` | `dark` | `dark`, `light`, `auto` (jasny 7–19, ciemny w nocy — także przełącza tapetę) |
| `appearance_accent` | `neutral` | `neutral`, `blue`, `teal`, `green`, `yellow`, `orange`, `red`, `pink`, `purple` albo `#rrggbb` |
| `appearance_radius` | `10` | zaokrąglenie elementów powłoki (px) |
| `appearance_font_scale` | `100` | skala czcionek (%) |
| `appearance_panel_opacity` | `94` | nieprzezroczystość panelu (%) |
| `appearance_animations` | `1` | animacje |
| `appearance_cursor_theme` | puste | motyw kursora (`XCURSOR_THEME`, po ponownym zalogowaniu) |
| `appearance_cursor_size` | `24` | rozmiar kursora |
| `appearance_gtk_sync` | `1` | `gsettings`: jasny/ciemny motyw i kursor dla aplikacji GTK |

### panel
`panel_position` (`top`/`bottom`), `panel_floating`, `panel_height`, `panel_margin`, `panel_left`/`panel_center`/`panel_right`
(moduły: `launcher`, `tasks`, `clock`, `date`, `status`, `power`, `lock`, `settings`), `panel_clock` (`24h`/`12h`),
`panel_seconds`, `panel_date`, `panel_max_tasks`.

### wallpaper — szczegóły w [WALLPAPERS.md](WALLPAPERS.md)
`wallpaper_mode` = `gradient` | `color` | `image` | `slideshow`; `wallpaper_color1`, `wallpaper_color2`, `wallpaper_angle`,
`wallpaper_preset`, `wallpaper_image`, `wallpaper_image_dark`, `wallpaper_image_light`, `wallpaper_fit`
(`fill`/`fit`/`stretch`/`center`/`tile`), `wallpaper_folder`, `wallpaper_interval` (min), `wallpaper_order`
(`random`/`sequential`), `wallpaper_outputs` (`"eDP-1=/a.png;HDMI-A-1=/b.png"`).

### windows (labwc)
`windows_titlebar` (np. `icon:iconify,max,close`), `windows_corner_radius`, `windows_border`, `windows_shadows`, `windows_gap`,
`windows_snap`, `windows_focus_follows_mouse`, `windows_workspaces`, `windows_font`, `windows_font_size`.

### input
`input_layout`, `input_variant`, `input_options` (XKB; po ponownym zalogowaniu), `input_repeat_rate`, `input_repeat_delay`,
`input_numlock`, `input_tap`, `input_natural_scroll`, `input_pointer_speed` (−1…1), `input_accel` (`adaptive`/`flat`),
`input_disable_while_typing`.

### display
`display_scale` — `"NAZWA:SKALA NAZWA:SKALA"`, np. `"eDP-1:1.5 HDMI-A-1:1"` (`wlr-randr`).

### night — nocne światło (`wlsunset`)
`night_enabled`, `night_temp` (2500–6500 K), `night_start` (`20:00`), `night_end` (`07:00`).

### notify, lock, apps
`notify_enabled`, `notify_dnd`, `notify_timeout`, `notify_max`; `lock_timeout`, `lock_screen_off` (minuty, 0 = wyłączone);
`apps_terminal`, `apps_browser`, `apps_files`, `apps_editor` (puste = autodetekcja).

### keys — skróty
`keys_<KLAWISZ> = "exec:polecenie"` albo `"action:NazwaAkcjiLabwc[:atrybut=wartość…]"`. W nazwie klawisza myślnik zapisujesz
jako `_`, a prawdziwy podkreślnik jako `__`: `W-t` → `keys_W_t`, `KP_Enter` → `keys_KP__Enter`. Modyfikatory: `W` Super,
`A` Alt, `C` Ctrl, `S` Shift. Pusta wartość wyłącza skrót domyślny.

```hl
% keys_W_Left: str = "action:SnapToEdge:direction=left"
% keys_XF86AudioMute: str = "exec:hwde osd mute"
% keys_W_q: str = ""
```

### autostart
`autostart_a001`, `autostart_a002`… — polecenia uruchamiane przy logowaniu (kolejność alfabetyczna).

## Uwagi o Hacker Langu w konfiguracji
* Warunki pisz jako `? switch @zmienna`, `> test …` + `? ok` albo `?~`; zwykłe `? @a > 5` nie jest porównaniem liczb.
* `% nazwa = @inna` **nie** podstawia zmiennej — użyj `::set nazwa @inna/…`.
* Tekst wypisany przez `~>` w konfiguracji jest ignorowany (HWDE czyta tylko zmienne).
* Błąd w pliku = `hl` nic nie zwróci i HWDE wczytuje tylko statyczne przypisania; sprawdź `hwde check`.
