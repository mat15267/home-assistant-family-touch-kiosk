# 🏠 Das ultimative Home Assistant Familien-Dashboard & Kiosk-System

Dieses Projekt enthält die vollständige Dokumentation zum Aufbau eines intelligenten, familienfreundlichen Smart-Home-Displays. Es besteht aus zwei separaten Raspberry Pis: Einem dedizierten Home Assistant Server und einem an der Wand montierten 7-Zoll-Touch-Display (Kiosk).

---

## 🏗️ System-Architektur & Hardware

Das System wird aus Sicherheits- und Performance-Gründen strikt getrennt:
1. **Server-Zentrale (Pi 4 oder 5 empfohlen):** Hier läuft das "Gehirn" (Home Assistant OS). Er verwaltet alle Geräte, Automationen und Daten.
2. **Display-Kiosk (Pi 3B, 4 oder 5):** Hier läuft ein schlankes Raspberry Pi OS, das ausschließlich als interaktiver Touch-Monitor dient.

### 🛒 Einkaufsliste / Hardware-Komponenten
* **Server:** [Raspberry Pi 4 (min. 4GB RAM)](https://raspberrypi.com) oder [Pi 5](https://raspberrypi.com) + offizielles Netzteil.
* **Display-Pi:** Raspberry Pi 3B, 4 oder 5.
* **Touchscreen:** [7-Zoll-Touchscreen mit HDMI (Bild) und USB (Touch-Daten)](https://raspberrypi.com).
* **Speichermedien:** 2x MicroSD-Karten (Min. 32GB, empfohlen: [SanDisk Max Endurance](https://westerndigital.com)).
* **Kabel:** Passende HDMI-Kabel und USB-Verbindungskabel für das Display.

---

## ⚠️ WICHTIGE WARNHINWEISE

> [!WARNING]
> **⚡ Strom-Unterversorgung (Undervoltage):** Touchscreens ziehen viel Strom über die USB-Ports des Raspberry Pi. Nutze für beide Pis zwingend die offiziellen Netzteile. Ein billiges Handyladegerät führt zu unvorhersehbaren Abstürzen und zerstörten SD-Karten.

> [!CAUTION]
> **💾 SD-Karten-Verschleiß:** Home Assistant schreibt permanent Logdateien. Verwende billige SD-Karten, geben diese oft nach wenigen Monaten den Geist auf. Nutze "High Endurance"-Karten oder schließe eine SSD über USB an den Server an.

---

## 🛠️ Schritt 1: Installation des Home Assistant Servers

Auf dem Haupt-Pi installieren wir das offizielle Home Assistant OS (HAOS).

1. Lade den **Raspberry Pi Imager** herunter: [://raspberrypi.com](https://www.://raspberrypi.com/)
2. Starte den Imager und klicke auf **Gerät wählen** (z.B. Raspberry Pi 4).
3. Klicke auf **Betriebssystem wählen** -> `Other specific-purpose OS` -> `Home assistants and home automation` -> `Home Assistant OS`.
4. Wähle deine SD-Karte aus und klicke auf **Weiter/Schreiben**.
5. Stecke die geflashte Karte in den Server-Pi, verbinde ihn per **Netzwerkkabel (LAN)** mit deinem Router und schließe den Strom an.
6. Warte ca. 10 Minuten. Öffne an deinem Computer einen Browser und rufe folgende Adresse auf:
   ```text
   http://homeassistant.local:8123
   ```
   *(Alternativ die IP-Adresse deines Pis nutzen).* Folget den Anweisungen auf dem Bildschirm, um dein Administratorkonto zu erstellen.

---

## 📺 Schritt 2: Einrichtung des extra Display-Pis (Kiosk-Modus)

Auf diesem Pi installieren wir **nicht** Home Assistant, sondern ein normales Betriebssystem, welches sich beim Starten automatisch mit der Server-Zentrale verbindet.

### 🔌 Hardware-Verkabelung
1. Verbinde den HDMI-Ausgang des Pis mit dem HDMI-Eingang des 7" Displays.
2. Verbinde den USB-Port des Displays (oft beschriftet mit *Touch*) mit einem USB-Port des Pis.
3. Schließe das offizielle Netzteil an den Pi an.

### 💾 Betriebssystem & Kiosk-Software
1. Öffne den **Raspberry Pi Imager**.
2. Wähle **Raspberry Pi OS (64-bit)** (mit Desktop-Oberfläche).
3. Klicke auf das **Zahnrad-Symbol (Einstellungen)**, um dein WLAN-Passwort, Spracheinstellungen und SSH direkt vorab zu konfigurieren. Schreibe die Karte.
4. Stecke die Karte in den Display-Pi und starte ihn. Öffne das Terminal auf dem Pi oder logge dich per SSH ein.

#### ⚙️ Konfiguration für Raspberry Pi 4 und Pi 5 (Wayland-Grafiksystem)
1. Installiere das Tool, um den Mauszeiger bei Inaktivität unsichtbar zu machen:
   ```bash
   sudo apt update && sudo apt install unclutter -y
   ```
2. Öffne die Autostart-Konfiguration:
   ```bash
   nano ~/.config/wayfire.ini
   ```
3. Scrolle ganz nach unten und füge folgenden Block hinzu (Ersetze die IP-Adresse mit der IP deines Home Assistant Servers):
   ```ini
   [autostart]
   kiosk = chromium-browser --kiosk --noerrdialogs --disable-infobars --no-first-run http://DEINE_HA_SERVER_IP:8123/dashboard-familie?kiosk
   unclutter = unclutter -idle 0.1 -root
   ```
4. Speichern mit `Strg + O`, Bestätigen mit `Enter`, Verlassen mit `Strg + X`.

#### ⚙️ Konfiguration für Raspberry Pi 3B (Älteres X11-Grafiksystem)
Da der Pi 3B weniger Leistung hat, optimieren wir hier den Autostart:
1. Öffne die LXDE-Autostartdatei:
   ```bash
   nano ~/.config/lxsession/LXDE-pi/autostart
   ```
2. Füge diese Zeilen ein, um den Bildschirmschoner zu blockieren und Chromium direkt im Vollbild zu starten:
   ```bash
   @xset s off
   @xset -dpms
   @xset s noblank
   @chromium-browser --kiosk --noerrdialogs --disable-infobars http://DEINE_HA_SERVER_IP:8123/dashboard-familie?kiosk
   ```

---

## 🧩 Schritt 3: Funktionen & Integrationen auf dem Server einrichten

Gehe an deinen Computer und öffne die Home Assistant Oberfläche (`http://homeassistant.local:8123`).

### 1. HACS installieren (Erweiterte Funktionen)
Der Home Assistant Community Store (HACS) schaltet alle benutzerdefinierten Karten frei.
* **Offizielle Anleitung:** [hacs.xyz](https://hacs.xyz)
* **Terminal-Befehl:** Installiere das Add-on "Advanced SSH & Web Terminal" in Home Assistant. Öffne das Terminal und gib diesen Befehl ein:
  ```bash
  wget -O - hacs.xyz | bash -
  ```
* Starte Home Assistant danach neu.

### 2. Design-Karten über HACS herunterladen
Navigiere in Home Assistant zu **HACS** -> **Frontend** -> Klicke unten rechts auf das "+"-Symbol und suche nach folgenden Karten:
1. **[Mushroom Cards](https://github.com):** Für touch-optimierte Schalter.
2. **[Atomic Calendar Revive](https://github.com):** Für die Kalenderliste.
3. **[Kiosk Mode](https://github.com):** Blendet Menüleisten automatisch aus.
4. **[Garbage Collection Card](https://github.com):** Für Müllabfuhr-Termine.
5. **[Card Mod](https://github.com):** Ermöglicht optische CSS-Anpassungen.

### 3. Google Family Kalender verknüpfen (API)
1. Rufe die [Google Cloud Console](https://google.com) auf und erstelle ein neues Projekt.
2. Suche nach **Google Calendar API** und aktiviere diese.
3. Gehe auf **OAuth-Zustimmungsbildschirm**, wähle "Extern" und trage deine E-Mail-Adresse ein.
4. Klicke auf **Anmeldedaten** -> **Anmeldedaten erstellen** -> **OAuth-Client-ID**.
5. Wähle als Anwendungstyp **Webanwendung** und füge unter **Autorisierte Umleitungs-URIs** exakt diesen Link hinzu: `home-assistant.io`
6. Kopiere **Client-ID** und **Client-Secret**.
7. Wechsel zu Home Assistant -> **Einstellungen** -> **Geräte & Dienste** -> **Integration hinzufügen** -> **Google Calendar** und füge deine Daten dort ein.

### 4. Weitere Alltagsfunktionen aktivieren
* **Wetter:** Gehe auf *Einstellungen -> Geräte & Dienste -> Integration hinzufügen* und wähle **MET.no** (Standard-Wetter).
* **Einkaufsliste:** Aktiviere die integrierte **To-do-Liste**-Funktion in Home Assistant (erstellt automatisch `todo.shopping_list`).
* **ÖPNV (Abfahrtszeiten):** Suche unter Integrationen nach **Deutsche Bahn** oder installiere die passende Verkehrsverbund-Integration via HACS.

---

## 🎨 Schritt 4: Das komplette All-in-One Dashboard-Layout (YAML)

Erstelle unter **Einstellungen** -> **Dashboards** ein neues Dashboard mit dem Namen `dashboard-familie`. Klicke oben rechts auf die drei Punkte -> **Dashboard bearbeiten** -> **Raw-Konfigurationseditor** und füge diesen Code ein:

```yaml
views:
  - title: Hauptansicht
    path: home
    type: sections
    cards:
      # SEKTION 1: Wetter & Familie
      - type: grid
        cards:
          - type: weather-forecast
            entity: weather.home
            forecast_type: daily
            name: Wetter zu Hause
          - type: custom:mushroom-chips-card
            chips:
              - type: template
                icon: mdi:trash-can
                content: "Müll: {{ states('sensor.garbage_collection') }}"
                icon_color: green

      # SEKTION 2: Beleuchtung (Touch-freundlich)
      - type: grid
        cards:
          - type: custom:mushroom-light-card
            entity: light.wohnzimmer_licht
            name: Wohnzimmer Licht
            show_brightness_control: true
            use_light_color: true
            layout: vertical
          - type: custom:mushroom-light-card
            entity: light.kuche_licht
            name: Küchenlicht
            show_brightness_control: true
            use_light_color: true
            layout: vertical

      # SEKTION 3: Kalender-Vorschau & Einkaufsliste
      - type: grid
        cards:
          - type: custom:atomic-calendar-revive
            entities:
              - entity: calendar.family
            name: Familien-Termine
            showCurrentEventLine: true
            maxDaysToShow: 5
            tap_action:
              action: navigate
              navigation_path: /dashboard-familie/kalender-vollbild
          - type: todo-list
            entity: todo.shopping_list
            title: Einkaufsliste

  - title: Kalender Vollbild
    path: kalender-vollbild
    type: panel
    cards:
      - type: custom:atomic-calendar-revive
        entities:
          - entity: calendar.family
        showMonth: true
        enableDashboardMode: true
```

---

## 🤖 Schritt 5: Automatisierung (Display nachts ausschalten)

Um Strom zu sparen und das Display zu schonen, schalten wir den Bildschirm nachts automatisch über Home Assistant ab. Voraussetzung hierfür ist das über HACS installierte Tool **[Browser Mod](https://github.com)**.

Erstelle eine neue Automation unter *Einstellungen -> Funktionen & Automationen -> Automation erstellen -> Leere Automation* und füge diesen YAML-Code ein:

```yaml
alias: "Display: Nachtabschaltung"
description: "Schaltet das Kiosk-Display nachts aus und morgens an"
trigger:
  - platform: time
    at: "22:30:00"
    id: ausschalten
  - platform: time
    at: "06:30:00"
    id: einschalten
condition: []
action:
  - choose:
      - conditions:
          - condition: trigger
            id: ausschalten
        sequence:
          - action: browser_mod.screen_off
            target:
              device_id: DEINE_BROWSER_MOD_DEVICE_ID
      - conditions:
          - condition: trigger
            id: einschalten
        sequence:
          - action: browser_mod.screen_on
            target:
              device_id: DEINE_BROWSER_MOD_DEVICE_ID
mode: single
```

---


## 🔗 Hilfreiche Links & Quellen
* **Home Assistant OS Download:** [home-assistant.io/installation](https://home-assistant.io)
* **HACS Dokumentation:** [hacs.xyz](https://hacs.xyz)
* **Raspberry Pi Hardware-Guides:** [://raspberrypi.com](https://www.://raspberrypi.com/)
