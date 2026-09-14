# Handover: Impressum füllen + koeseo.com deployen

Für einen **lokalen** Claude-Code-Agenten auf Gökhans Maschine. Die Cloud-Session konnte
`koeseo.de` nicht erreichen (Egress-Policy blockt die Domain: `ERR_TUNNEL_CONNECTION_FAILED`)
und hatte keinen Zugriff auf Server oder Coolify. Beides hat die lokale Maschine.

---

## Prompt zum Kopieren

> Lies `site/HANDOVER.md` in diesem Repo und arbeite sie ab: Impressum-Platzhalter aus dem
> Impressum von koeseo.de füllen (alle drei Sprachfassungen), lokal verifizieren, committen,
> pushen, dann nach Abschnitt 4 auf koeseo.com deployen. Frag mich bei jeder Angabe nach,
> die du auf koeseo.de nicht eindeutig findest — Impressumsdaten nicht raten.

---

## 0. Stand

- Repo: `koeseo/koeseo`, Branch `claude/koeseo-one-page-site-0b45c1`, PR #1 (Draft)
- Die Seite liegt komplett unter `site/` — eine `index.html` ohne Build-Step, dazu
  `Dockerfile`, `nginx.conf`, `robots.txt`, `sitemap.xml`
- Dreisprachig DE/EN/TR über `data-t`-Attribute, Details in `site/README.md`
- Offen: **vier Impressum-Angaben, je dreimal** (DE/EN/TR) + Deploy

## 1. Daten beschaffen

Primärquelle ist das bestehende Impressum auf **https://koeseo.de/impressum** (lokal
erreichbar). Übernimm von dort wörtlich:

| Feld | Wofür |
| --- | --- |
| Straße + Hausnummer | Anschrift § 5 DDG |
| PLZ + Ort | Anschrift § 5 DDG |
| Telefonnummer | Kontaktangabe § 5 DDG |
| USt-IdNr. | § 27a UStG |

Zwei Punkte, die du **nicht** raten darfst:

1. **Ort.** Im Profil-README steht Bochum, in anderen Notizen Hagen. Nimm, was auf
   koeseo.de steht; steht dort nichts Eindeutiges, frag nach.
2. **Umsatzsteuer.** Gibt es keine USt-IdNr., weil Kleinunternehmer nach § 19 UStG, dann
   ersetze den ganzen Abschnitt (siehe 2.3) statt eine Nummer einzusetzen.

Prüf beim Lesen mit, ob auf koeseo.de noch etwas steht, das ins Impressum gehört und hier
fehlt (z. B. Berufshaftpflicht, Kammer, Aufsichtsbehörde, Registereintrag). Falls ja: dem
User zeigen, nicht still einbauen.

## 2. Platzhalter ersetzen

Alle in `site/index.html`. **Jedes Feld dreimal** — DE, EN, TR. Die `<!-- TODO: ... -->`
Kommentare direkt darüber danach mit entfernen.

### 2.1 Anschrift

| Zeile (ca.) | Platzhalter | ersetzen durch |
| --- | --- | --- |
| 263 | `[Straße Hausnummer]` | Straße + Nr. |
| 264 | `[PLZ] Bochum` | `<PLZ> <Ort>` |
| 343 | `[Street No.]` | Straße + Nr. |
| 344 | `[Postcode] Bochum` | `<PLZ> <Ort>` |
| 421 | `[Sokak No.]` | Straße + Nr. |
| 422 | `[Posta kodu] Bochum` | `<PLZ> <Ort>` |

Der Ländername bleibt jeweils in der Sprache des Blocks: `Deutschland` / `Germany` /
`Almanya`.

### 2.2 Telefon

`[Telefonnummer]` (263ff DE), `[Phone number]` (EN), `[Telefon numarası]` (TR) → dieselbe
Nummer, international geschrieben: `+49 ...`.

### 2.3 Umsatzsteuer

Mit USt-IdNr.: `[DE-USt-IdNr.]`, `[DE VAT ID]`, `[DE KDV No.]` → `DE123456789`.

Kleinunternehmer? Dann die drei `<h3>`-Blöcke „Umsatzsteuer" / „VAT" /
„Katma değer vergisi" jeweils ersetzen durch:

```html
<!-- DE -->
<h3>Umsatzsteuer</h3>
<p>Gemäß § 19 UStG wird keine Umsatzsteuer berechnet.</p>
<!-- EN -->
<h3>VAT</h3>
<p>No VAT is charged pursuant to § 19 of the German VAT Act (small business regulation).</p>
<!-- TR -->
<h3>Katma değer vergisi</h3>
<p>Alman KDV Kanunu'nun 19. maddesi (küçük işletme düzenlemesi) uyarınca KDV alınmaz.</p>
```

### 2.4 Kontrolle

```bash
grep -nE "TODO|\[Straße|\[PLZ|\[Telefonnummer|\[DE-USt|\[Street|\[Postcode|\[Phone|\[DE VAT|\[Sokak|\[Posta kodu|\[Telefon numarası|\[DE KDV" site/index.html
```

Muss **leer** sein. Danach die Adresse in allen drei Blöcken gegenlesen — sie muss dreimal
identisch sein (bis auf den Ländernamen).

## 3. Lokal verifizieren

```bash
python3 -m http.server 8080 --directory site
```

Dann im Browser auf http://localhost:8080 alle drei Sprachen durchklicken (Umschalter oben
rechts) und prüfen:

- Impressum zeigt in jeder Sprache genau **einen** Block, mit denselben Daten
- Sprachwahl überlebt einen Reload (localStorage)
- Mit deaktiviertem JavaScript erscheint Deutsch, nicht alle drei übereinander
- Mobil (DevTools, 375 px): kein horizontales Scrollen

Dann committen und pushen:

```bash
git add site/index.html
git commit -m "Impressum-Angaben eingesetzt (DE/EN/TR)"
git push origin claude/koeseo-one-page-site-0b45c1
```

PR #1 ist ein Draft — nach dem Review auf „Ready for review" setzen und mergen.

## 4. Deploy auf koeseo.com

Stack laut Setup: Coolify + Traefik + Docker auf `185.150.25.225`. Das Repo bringt alles
mit, was dafür nötig ist: `site/Dockerfile` (nginx:alpine) und `site/nginx.conf` mit
Security-Headern, CSP, gzip und Redirects für `/impressum` und `/kontakt`.

**Variante A — Coolify (empfohlen).** Neue Resource → *Dockerfile*:

| Feld | Wert |
| --- | --- |
| Repository | `koeseo/koeseo` |
| Branch | `master` (nach dem Merge) |
| Base Directory | `/site` |
| Dockerfile | `Dockerfile` |
| Port | `80` |
| Domain | `koeseo.com`, `www.koeseo.com` |

TLS über Traefik/Let's Encrypt wie bei den anderen Resources. Auto-Deploy on Push
aktivieren, dann trägt sich jede weitere Änderung selbst aus.

**Variante B — direkt auf dem Server**, falls Coolify außen vor bleiben soll:

```bash
ssh root@185.150.25.225
git clone https://github.com/koeseo/koeseo.git /opt/koeseo-site   # oder git pull
cd /opt/koeseo-site/site
docker build -t koeseo-site:latest .
docker run -d --name koeseo-site --restart unless-stopped \
  --label "traefik.enable=true" \
  --label "traefik.http.routers.koeseo.rule=Host(\`koeseo.com\`) || Host(\`www.koeseo.com\`)" \
  --label "traefik.http.routers.koeseo.tls.certresolver=letsencrypt" \
  --label "traefik.http.services.koeseo.loadbalancer.server.port=80" \
  --network <traefik-netzwerk> \
  koeseo-site:latest
```

Netzwerkname und Certresolver aus der bestehenden Traefik-Konfiguration übernehmen, nicht
aus diesem Dokument raten. Läuft auf koeseo.com schon etwas, vorher mit dem User klären,
was damit passiert.

**DNS:** `koeseo.com` und `www` müssen auf `185.150.25.225` zeigen.

## 5. Nach dem Deploy prüfen

```bash
curl -sSI https://koeseo.com/ | head -20                    # 200 + Security-Header
curl -sS  https://koeseo.com/ | grep -c 'data-t='           # > 0
curl -sSI https://koeseo.com/impressum | grep -i location   # 301 auf /#impressum
curl -sSI https://koeseo.com/robots.txt | head -3
```

Im Browser einmal alle drei Sprachen live durchgehen und in den DevTools auf dem Network-Tab
gegenchecken, dass **keine** externe Domain geladen wird — die Datenschutzerklärung auf der
Seite sagt genau das zu.

---

## Nachtrag 14.09.2026 — abgearbeitet

Erledigt vom lokalen Agenten, mit Messwerten:

- **Impressum:** Anschrift (Annastraße 27, 44793 Bochum) und USt-IdNr. DE327767879
  wörtlich aus https://koeseo.de/impressum, in allen drei Sprachfassungen. Die
  Telefonzeile ist entfallen — koeseo.de nennt keine Nummer, § 5 DDG verlangt sie
  nicht zwingend. Entscheidung von G-KHAAN am 14.09.
- **Kontakt:** info@koeseo.com bleibt, auf Ansage. Die Mail läuft weiter über
  All-Inkl (MX `w0176259.kasserver.com`), davon wurde nichts angefasst.
- **Nicht übernommen:** Steuernummer (bei vorhandener USt-IdNr. nicht nötig) und
  die Telefonnummer aus den Whois-Daten.
- **DNS:** koeseo.com lag bei Porkbun, delegierte aber auf `ns5/ns6.kasserver.com`;
  die Porkbun-Zone war leer. Alle Records wurden zuerst 1:1 nachgebaut
  (MX, SPF, DMARC, brevo-code, mail/smtp/imap/pop/webmail/autodiscover/autoconfig
  weiterhin auf 85.13.130.144), nur apex und www zeigen jetzt auf 185.150.25.225.
  Danach Nameserver-Wechsel auf Porkbun. Mail blieb dabei unverändert.
- **Deploy:** Coolify-Resource `koeseo-com` (Projekt KoeHub, production, Dockerfile
  aus `/site`, Port 80), Traefik + Let's Encrypt, Auto-Deploy per GitHub-Webhook.
- **Zwei Korrekturen aus der Live-Messung:** `/impressum` leitete auf *http* um
  (nginx sah hinter Traefik kein TLS → `absolute_redirect off`), und www lieferte
  die Seite ein zweites Mal statt umzuleiten. Der Kontrast von `--fg-faint` lag bei
  3,66:1 und damit unter dem Minimum; jetzt 5,5:1.
- **Gemessen nach dem Deploy:** kein Element unter 4,5:1, kein horizontales Scrollen
  bei 375 px, keine externe Domain im Netzwerk-Tab, ohne JavaScript erscheint nur
  die deutsche Fassung.
