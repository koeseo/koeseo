# koeseo.com — One Page

Statische Single-File-Seite. Keine Build-Step, keine externen Ressourcen
(keine Google Fonts, kein Analytics, keine Cookies) — damit ist die
Datenschutzerklärung auf der Seite auch tatsächlich zutreffend.

## Vor dem Livegang ausfüllen

In `index.html` sind vier Felder als Platzhalter markiert (jeweils mit `TODO`-Kommentar):

| Feld | Platzhalter |
| --- | --- |
| Straße + Hausnummer | `[Straße Hausnummer]` |
| PLZ + Ort | `[PLZ] Bochum` |
| Telefon | `[Telefonnummer]` |
| USt-IdNr. | `[DE-USt-IdNr.]` |

Kleinunternehmer nach § 19 UStG? Dann den Abschnitt „Umsatzsteuer" ersetzen durch:
„Gemäß § 19 UStG wird keine Umsatzsteuer berechnet."

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
