# HTLNet auf Raspberry Pi

Öffentliches Raspberry-Pi-Paket für Raspberry Pi OS **64 Bit** mit Docker Compose. Die Online-App bleibt erhalten. Lokale Switch-Abfragen, Topologie, Status und Netzwerk-Handbuch; täglicher Online-Abgleich ab **06:00 österreichischer Zeit**, inklusive Sommerzeit. Port **8090**.

Das Downloadpaket und das fertige Container-Abbild sind **ohne GitHub-Anmeldung öffentlich abrufbar**. ARM64 (Raspberry Pi) und AMD64 sind erfolgreich erstellt und geprüft.

## Direkt auf dem Pi einrichten

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
printf '%s\n' 'HTLNET_IMAGE=ghcr.io/wolfmayr72/htlnet-raspberry-pi:latest' > .env
docker compose pull
docker compose up -d --no-build
```

Ansicht: `http://<IP-des-Pi>:8090`, Benutzername **htlnet**, selbst gewähltes Kennwort. Beim ersten Start benötigt der Pi Internet und eine aktualisierte, veröffentlichte Online-App. Anschließend bleibt die lokale Ansicht ohne Internet verfügbar. Keine Patchplanung oder Online-Bearbeitung. Für neue Messwerte Seite neu laden.

Updates: `docker compose pull && docker compose up -d --no-build`. Alternativ aus dem Downloadpaket selbst bauen: `docker compose up -d --build`.

## Sicherheit und Betrieb

Zugangsdaten und Netzwerkpläne gehören **niemals** in dieses öffentliche Projekt. `config/` und `data/` bleiben ausschließlich auf dem Pi. Nur im vertrauenswürdigen Hausnetz, keine Portfreigabe ins Internet; für Fernzugriff VPN. Die lokale Anmeldung über HTTP ist nicht verschlüsselt.

Der tägliche Lauf lädt den aktuellen Plan, fragt Switches frisch ab und überträgt einmal online; nach verpasstem Start wird nach 06:00 nachgeholt. Höchstens ein Übertragungsversuch pro Tag, auch nach Neustarts. Bei Fehlern erfolgt der nächste Versuch am Folgetag. Lokale Abfragen verursachen keine Online-Aufrufe; die Online-App verursacht weiterhin laufende Betriebskosten.

Die vollständige Anleitung und lesbaren Python-Quellen befinden sich im [Downloadpaket](../../releases/latest). Hinweise darin zur noch ausstehenden GitHub-Veröffentlichung stammen aus der ursprünglichen Vorbereitung und sind durch diese Veröffentlichung erledigt. Echtes Raspberry-Pi-Gerät, Switch-Abfragen vor Ort und tägliche Online-Übertragung müssen nach Installation geprüft werden.
