# HTLNet auf Raspberry Pi · Aruba REST

Version **2.0.1**, Raspberry Pi OS **64 Bit**, Docker Compose, Port **8090**.

- Aruba AOS-CX: Status, LLDP-Nachbarn und PoE-Watt, soweit vom Switch gemeldet.
- Lokale Webseite: Topologie, Status-Kacheln/Liste und Netzwerk-Handbuch; neue Messwerte automatisch.
- Lokale Portsteuerung: VLAN unmarkiert/markiert, ein/aus und PoE-Neustart; Vorschau und Bestätigung „Wirklich?“.
- Core-, Stacking-, LAG- und Switch-Verbindungen sind geschützt. Änderungen werden rückgelesen, gespeichert und protokolliert.
- Täglicher Online-Abgleich ab 06:00 Europe/Vienna. Windows bleibt unverändert, UniFi beim UniFi Controller.

## Installation

[Download und vollständige Anleitung](https://github.com/wolfmayr72/htlnet-raspberry-pi/releases/tag/v2.0.1)

Das Paket enthält die Anleitung, Beispielkonfiguration, lesbare Python-Quellen und Tests. Container: `ghcr.io/wolfmayr72/htlnet-raspberry-pi:2.0.1` (ARM64/AMD64 nach erfolgreichem GitHub-Lauf).

In `config/agent-config.json`: Online-Schlüssel, eigenes lokales Kennwort (mindestens 12 Zeichen), Switch-Konto unter `aruba`, unterstützte API-Version und vertrauenswürdiges Switch-Zertifikat. **Zuerst nur lesen** (`portControlEnabled: false`); nach erfolgreicher Prüfung bewusst freischalten. Eine alte SNMP-Konfiguration muss vor dem Wechsel angepasst werden.

Zugriff: `http://<Pi-IP>:8090`, Benutzer `htlnet`, eigenes Kennwort. Konfiguration und Netzwerkstände bleiben privat auf dem Pi. Keine Portfreigabe ins Internet; HTTP-Anmeldung nur im vertrauenswürdigen Verwaltungsnetz, sonst HTTPS/VPN verwenden. Schreibzugriffe auf Switches verlangen geprüfte Zertifikate.

**Vor Ort noch zu prüfen:** echte Aruba-Abfragen, Firmware-Unterstützung, Portänderungen und Pi-Betrieb. Automatische Tests und Bedienung mit einem simulierten Switch sind geprüft. Online-Aufträge an den Pi sind noch nicht enthalten; Portsteuerung erfolgt nur auf der lokalen Pi-Webseite.
