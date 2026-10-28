# Dostosowywanie

## 1. Ustawienia (GUI)
`hwde settings` (albo `Super+,`) — osiem stron: Wygląd, Panel, Okna, Wejście, Ekran i dźwięk, Powiadomienia i blokada,
Skróty i aplikacje, System. Wszystko zapisuje do `hwde.conf`.

## 2. Własny CSS — `~/.config/hwde/custom.css`
Ładowany po wbudowanym arkuszu i przeładowywany po zapisie. Obsługiwane: selektory `typ`, `.klasa`, `#id`, listy po
przecinku, `:hover` `:active` `:focus`; właściwości `background` (kolor lub `linear-gradient(kąt, c1, c2)`), `color`,
`border-radius`, `border`, `padding`, `margin`, `gap`, `font-size`, `font-weight`, `box-shadow`, `opacity`, `backdrop-filter`…
Panel ma id `#hwde-panel`. Przykład w `examples/custom.css`. Pełny opis: `vendor/gui/docs/STYLING.md`.

## 3. Moduły panelu
`panel.left/center/right` to listy modułów w dowolnej kolejności, np. panel w stylu docka na dole:

```ini
[panel]
position = bottom
floating = 1
left = launcher
center = tasks
right = status,clock,power
```

## 4. labwc
`rc.xml`, `themerc-override`, `menu.xml`, `environment` i `autostart` w `~/.config/labwc` są **generowane** z `hwde.conf`
(nie edytuj ich ręcznie — zostaną nadpisane). Własne fragmenty XML (np. `windowRules`) wstaw do
`~/.config/hwde/rc-extra.xml` — trafiają na koniec `rc.xml` (przykład: `examples/rc-extra.xml`).

## 5. Powiadomienia z terminala i skryptów
`hwde notify "Tytuł" "Treść"` albo dowolny klient `org.freedesktop.Notifications` (`notify-send`, aplikacje).

## 6. Uruchamianie dodatkowych programów
Sekcja `[autostart]`, a dla pasków/widżetów innych autorów — warstwy layer-shell działają obok powłoki HWDE.
