# koeseo.com — One Page

Statische Single-File-Seite, dreisprachig DE/EN/TR. Kein Build-Step, keine externen
Ressourcen (keine Google Fonts, kein Analytics, keine Cookies) — damit ist die
Datenschutzerklärung auf der Seite auch tatsächlich zutreffend.

## Sprachumschaltung

Alle drei Sprachfassungen stehen im HTML (`data-t="de"` / `"en"` / `"tr"`), CSS blendet
die nicht aktiven aus:

```css
html[data-lang="de"] [data-t]:not([data-t="de"]){display:none}
```

Der Umschalter setzt `data-lang` und `lang` auf `<html>` und merkt sich die Wahl in
`localStorage`. Ohne JavaScript greift die deutsche Fassung als Default. Erstbesuch ohne
gespeicherte Wahl: Sprache nach `navigator.language`, Fallback Englisch.

Neue Texte immer als Dreier-Satz anlegen:

```html
<span data-t="de">Deutscher Text</span><span data-t="en">English text</span><span data-t="tr">Türkçe metin</span>
```

Eine weitere Sprache kostet: eine CSS-Zeile, ein Button, der Sprachcode in `LANGS` im
Skript — und die Übersetzungen.

## Vor dem Livegang ausfüllen

In `index.html` sind vier Felder als Platzhalter markiert — **jeweils dreimal**, im
deutschen, englischen und türkischen Impressum-Block (jeweils mit `TODO`-Kommentar):

| Feld | DE | EN | TR |
| --- | --- | --- | --- |
| Straße + Hausnummer | `[Straße Hausnummer]` | `[Street No.]` | `[Sokak No.]` |
| PLZ + Ort | `[PLZ] Bochum` | `[Postcode] Bochum` | `[Posta kodu] Bochum` |
| Telefon | `[Telefonnummer]` | `[Phone number]` | `[Telefon numarası]` |
| USt-IdNr. | `[DE-USt-IdNr.]` | `[DE VAT ID]` | `[DE KDV No.]` |

Kleinunternehmer nach § 19 UStG? Dann den Abschnitt „Umsatzsteuer" / „VAT" /
„Katma değer vergisi" ersetzen durch: „Gemäß § 19 UStG wird keine Umsatzsteuer berechnet."

Die englische und die türkische Fassung tragen den Hinweis, dass die deutsche rechtlich
maßgeblich ist.

## Deploy

**Coolify:** Neue Resource → Dockerfile, Base Directory `/site`, Port 80.

**Lokal testen:**

```bash
python3 -m http.server 8080 --directory .
```

**Docker:**

```bash
docker build -t koeseo-site . && docker run -p 8080:80 koeseo-site
```
