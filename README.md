# HTLNet auf Raspberry Pi

Öffentliches Raspberry-Pi-Paket für Raspberry Pi OS **64 Bit** mit Docker Compose. Die Online-App bleibt erhalten. Lokale Switch-Abfragen, Topologie, Status und Netzwerk-Handbuch; täglicher Online-Abgleich ab **06:00 österreichischer Zeit**, inklusive Sommerzeit. Port **8090**.

## Paket direkt auf dem Pi laden

Nach erfolgreichem Durchlauf unter [Actions](../../actions) steht das Downloadpaket unter [Releases](../../releases/latest) bereit:

```sh
mkdir -p ~/htlnet && cd ~/htlnet
curl --fail --location https://github.com/wolfmayr72/htlnet-raspberry-pi/releases/latest/download/htlnet-raspberry-pi.tar.gz -o paket.tar.gz
tar -xzf paket.tar.gz
mkdir -p config data
cp config.example.json config/agent-config.json
nano config/agent-config.json
```

Eintragen: `token` aus Setup der Online-App, `localPassword` (mindestens 12 Zeichen) und lokale Switch-Zugangsdaten unter `snmpv3`. Abfrageintervall zunächst 15 Minuten. `cloudUploadEnabled: false` deaktiviert den täglichen Online-Abgleich.

```sh
sudo chown -R 10001:10001 data config
sudo chmod 700 data config
sudo chmod 600 config/agent-config.json
docker compose up -d --build
```

Ansicht: `http://<IP-des-Pi>:8090`, Benutzername **htlnet**, selbst gewähltes Kennwort. Beim ersten Start benötigt der Pi Internet und eine veröffentlichte Online-App mit dem Startpaket-Empfangspunkt. Anschließend bleibt die lokale Ansicht ohne Internet verfügbar. Keine Patchplanung oder Online-Bearbeitung. Für neue Messwerte Seite neu laden.

## Fertiges Container-Abbild

GitHub Actions baut zusätzlich ARM64 und AMD64 unter `ghcr.io/wolfmayr72/htlnet-raspberry-pi:latest`. GitHub legt neue Container-Pakete zunächst privat an: Der Kontoinhaber muss das Paket auf GitHub unter **Packages → htlnet-raspberry-pi → Package settings → Change visibility → Public** freigeben. Erst danach ist der Abruf ohne Anmeldung möglich.

Danach auf dem Pi:

```sh
printf '%s\n' 'HTLNET_IMAGE=ghcr.io/wolfmayr72/htlnet-raspberry-pi:latest' > .env
docker compose pull
docker compose up -d --no-build
```

Updates: `docker compose pull && docker compose up -d --no-build`. Ohne öffentliches Container-Abbild funktioniert weiterhin das Bauen aus dem Downloadpaket.

## Sicherheit und Betrieb

Zugangsdaten und Netzwerkpläne gehören **niemals** in dieses öffentliche Projekt. `config/` und `data/` bleiben ausschließlich auf dem Pi. Nur im vertrauenswürdigen Hausnetz, keine Portfreigabe ins Internet; für Fernzugriff VPN. Die lokale Anmeldung über HTTP ist nicht verschlüsselt.

Der tägliche Lauf lädt den aktuellen Plan, fragt Switches frisch ab und überträgt einmal online; nach verpasstem Start wird nach 06:00 nachgeholt. Höchstens ein Übertragungsversuch pro Tag, auch nach Neustarts. Bei Fehlern erfolgt der nächste Versuch am Folgetag. Lokale Abfragen verursachen keine Online-Aufrufe; die Online-App verursacht weiterhin laufende Betriebskosten.

Die vollständige Anleitung sowie die lesbaren Python-Quellen befinden sich im Downloadpaket. Echtes Raspberry-Pi-Gerät, Switch-Abfragen vor Ort und tägliche Online-Übertragung müssen nach Installation geprüft werden.
