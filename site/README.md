# koeseo.com — One Page

Statische Single-File-Seite, zweisprachig DE/EN. Kein Build-Step, keine externen
Ressourcen (keine Google Fonts, kein Analytics, keine Cookies) — damit ist die
Datenschutzerklärung auf der Seite auch tatsächlich zutreffend.

## Sprachumschaltung

Beide Sprachfassungen stehen im HTML (`data-t="de"` / `data-t="en"`), CSS blendet
die nicht aktive aus. Der Umschalter setzt `data-lang` auf `<html>` und merkt sich
die Wahl in `localStorage`. Ohne JavaScript greift die deutsche Fassung als Default.
Erstbesuch ohne gespeicherte Wahl: Sprache nach `navigator.language`.

Neue Texte immer als Paar anlegen:

```html
<span data-t="de">Deutscher Text</span><span data-t="en">English text</span>
```

## Vor dem Livegang ausfüllen

In `index.html` sind vier Felder als Platzhalter markiert — **jeweils zweimal**,
im deutschen und im englischen Impressum-Block (jeweils mit `TODO`-Kommentar):

| Feld | Platzhalter DE | Platzhalter EN |
| --- | --- | --- |
| Straße + Hausnummer | `[Straße Hausnummer]` | `[Street No.]` |
| PLZ + Ort | `[PLZ] Bochum` | `[Postcode] Bochum` |
| Telefon | `[Telefonnummer]` | `[Phone number]` |
| USt-IdNr. | `[DE-USt-IdNr.]` | `[DE VAT ID]` |

Kleinunternehmer nach § 19 UStG? Dann den Abschnitt „Umsatzsteuer" / „VAT" ersetzen
durch: „Gemäß § 19 UStG wird keine Umsatzsteuer berechnet."

Das englische Impressum trägt den Hinweis, dass die deutsche Fassung rechtlich
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
