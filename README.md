# 🏠 Das ultimative Home Assistant Familien- & Haussteuerungs-Dashboard (Kiosk-System)

Dieses Projekt enthält die vollständige Dokumentation zum Aufbau eines intelligenten, familienfreundlichen Smart-Home-Displays. Es verbindet eine **umfangreiche Haussteuerung** (Licht, Heizung, Rollläden, Geräte) mit wichtigen **Familienfunktionen** (Google Kalender, Einkaufsliste, Putzplan, Pinnwand) auf einem Wand-Touchscreen.

---

## 🏗️ System-Architektur & Hardware

Das System wird aus Sicherheits- und Performance-Gründen strikt getrennt:
1. **Server-Zentrale (Pi 4 oder 5 empfohlen):** Hier läuft das "Gehirn" (Home Assistant OS). Er verwaltet alle Geräte, Automationen und Daten.
2. **Display-Kiosk (Pi 3B, 4 oder 5):** Hier läuft ein schlankes Raspberry Pi OS, das ausschließlich als interaktiver Touch-Monitor dient.

### 🛒 Einkaufsliste & Kauf-Links
* **Server-Zentrale:** [Raspberry Pi 5 (8GB RAM)](https://www.berrybase.de/raspberry-pi-5-8gb-ram) oder [Raspberry Pi 4 (4GB+ RAM)](https://www.pollin.de/entwicklerboards/raspberry-pi/) für eine flüssige Haussteuerung.
* **Display-Pi:** Ein günstigerer [Raspberry Pi 5 / 4](https://buyzero.de/products/raspberry-pi-5) oder ein bereits vorhandener Raspberry Pi 3B. Alternativer [Preisvergleich über Idealo](https://www.idealo.de/preisvergleich/OffersOfProduct/203479148_-5-raspberry-pi-foundation.html).
* **Touchscreen (Option 1 - Empfehlung):** Das offizielle [Raspberry Pi Touch Display 2 (7 Zoll HD)](https://www.rasppishop.de/offizielles-7-Zoll-Raspberry-Pi-Touch-Display-2) – alternativ im [BerryBase Shop](https://www.berrybase.de/raspberry-pi-touch-display-2-7-portrait).
* **Touchscreen (Option 2 - Günstige HDMI/USB Alternative):** [Elecrow 7 Zoll HDMI Touchscreen](https://www.amazon.de/raspberry-pi-display-7-zoll/s?k=raspberry+pi+display+7+zoll) für universellen HDMI-Anschluss.
* **Sichere Netzteile:** Das offizielle [Raspberry Pi 27W USB-C Netzteil](https://www.amazon.de/smart-home-komponente-Raspberry-Starter-Kit-Netzteil-Geh%C3%A4use/dp/B0CW1Q5413) (Verhindert Abstürze durch Touchscreen-Stromspitzen).
* **Speichermedien:** Langlebige [SanDisk Max Endurance MicroSD-Karte (32GB+)](https://westerndigital.com) für hohen Schreib-Verschleiß.

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

## 🧩 Schritt 3: Alle Haus- und Familien-Funktionen auf dem Server einrichten

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
1. **[Mushroom Cards](https://github.com):** Für moderne, große Touch-Buttons für Lampen, Jalousien, Steckdosen, Heizung und Staubsauger.
2. **[Atomic Calendar Revive](https://github.com):** Für die Kalenderliste.
3. **[Kiosk Mode](https://github.com):** Blendet alle Menüleisten auf dem Touchscreen aus.
4. **[Garbage Collection Card](https://github.com):** Für Müllabfuhr-Termine.
5. **[Chores Card](https://github.com):** Für den interaktiven Familien-Putzplan.

### 3. Google Family Kalender verknüpfen (API)
1. Rufe die [Google Cloud Console](https://google.com) auf und erstelle ein neues Projekt.
2. Suche nach **Google Calendar API** und aktiviere diese.
3. Gehe auf **OAuth-Zustimmungsbildschirm**, wähle "Extern" und trage deine E-Mail-Adresse ein.
4. Klicke auf **Anmeldedaten** -> **Anmeldedaten erstellen** -> **OAuth-Client-ID**.
5. Wähle als Anwendungstyp **Webanwendung** und füge unter **Autorisierte Umleitungs-URIs** exakt diesen Link hinzu: `home-assistant.io`
6. Kopiere **Client-ID** und **Client-Secret**.
7. Wechsel zu Home Assistant -> **Einstellungen** -> **Geräte & Dienste** -> **Integration hinzufügen** -> **Google Calendar** und füge deine Daten dort ein.

### 4. Helfer für Zusatzfunktionen anlegen
Gehe auf *Einstellungen -> Geräte & Dienste -> Helfer -> Helfer erstellen*:
* Erstelle einen **Text-Helfer** (`input_text.essensplan`) für den wöchentlichen Menüplan.
* Erstelle einen **Text-Helfer** (`input_text.pinnwand`) für Familien-Notizen.

---

## 🎨 Schritt 4: Das komplette Haussteuerungs- & Familien-Dashboard (YAML)

Erstelle unter **Einstellungen** -> **Dashboards** ein neues Dashboard mit dem Namen `dashboard-familie`. Klicke oben rechts auf die drei Punkte -> **Dashboard bearbeiten** -> **Raw-Konfigurationseditor** und füge diesen extrem erweiterten Code ein. Er trennt das Dashboard sauber in **Haussteuerung (Lichter etc.)** und **Familienorganisation (Kalender etc.)**.

```yaml
views:
  # TAB 1: DAS HAUPT-DASHBOARD FÜR DIE GANZE HAUSSTEUERUNG
  - title: Haussteuerung
    path: home
    type: sections
    cards:
      # SPALTE 1: STATUS, SCHNELLZUGRIFF & WETTER
      - type: grid
        cards:
          - type: weather-forecast
            entity: weather.home
            forecast_type: daily
            name: Wetter vor Ort
          - type: custom:mushroom-chips-card
            chips:
              - type: template
                icon: mdi:trash-can
                content: "Müll: {{ states('sensor.garbage_collection') }}"
                icon_color: green
              - type: template
                icon: mdi:flash
                content: "{{ states('sensor.smart_meter_power') }}W"
                icon_color: amber
              - type: template
                icon: mdi:calendar
                content: "Kalender öffnen"
                icon_color: purple
                tap_action:
                  action: navigate
                  navigation_path: /dashboard-familie/kalender-vollbild

      # SPALTE 2: KOMPLETTE LICHTSTEUERUNG (Mushroom)
      - type: grid
        title: Beleuchtung
        cards:
          - type: custom:mushroom-light-card
            entity: light.wohnzimmer_licht
            name: Wohnzimmer
            show_brightness_control: true
            use_light_color: true
            layout: vertical
          - type: custom:mushroom-light-card
            entity: light.kuche_licht
            name: Küche
            show_brightness_control: true
            use_light_color: true
            layout: vertical
          - type: custom:mushroom-light-card
            entity: light.schlafzimmer_licht
            name: Schlafzimmer
            show_brightness_control: true
            use_light_color: true
            layout: vertical

      # SPALTE 3: HEIZUNG, STECKDOSEN & KLIMA
      - type: grid
        title: Klima & Energie
        cards:
          - type: custom:mushroom-climate-card
            entity: climate.wohnzimmer_thermostat
            name: Heizung Wohnzimmer
            hvac_modes:
              - heat
              - 'off'
            show_temperature_control: true
          - type: custom:mushroom-entity-card
            entity: switch.steckdose_fernseher
            name: Fernseher Strom
            tap_action:
              action: toggle
            icon: mdi:television

      # SPALTE 4: ROLLLÄDEN (JALOUSIEN) & GERÄTE
      - type: grid
        title: Jalousien & Geräte
        cards:
          - type: custom:mushroom-cover-card
            entity: cover.wohnzimmer_rollladen
            name: Rollladen Wohnzimmer
            show_buttons_control: true
          - type: custom:mushroom-vacuum-card
            entity: vacuum.saugroboter
            name: Saugroboter
            commands:
              - start
              - dock

  # TAB 2: DAS EXTREM ERWEITERTE FAMILIEN-ORGANISATIONS-DASHBOARD
  - title: Familienplaner
    path: familienplaner
    type: sections
    cards:
      # SPALTE 1: TERMINLISTE & EINKAUFSLISTE
      - type: grid
        cards:
          - type: custom:atomic-calendar-revive
            entities:
              - entity: calendar.family
            name: Nächste Termine
            showCurrentEventLine: true
            maxDaysToShow: 5
            tap_action:
              action: navigate
              navigation_path: /dashboard-familie/kalender-vollbild
          - type: todo-list
            entity: todo.shopping_list
            title: Einkaufsliste

      # SPALTE 2: ESSENSPLAN, PUTZPLAN & PINNWAND (Interaktiv!)
      - type: grid
        cards:
          - type: custom:mushroom-template-card
            primary: "📅 Was gibt's zu essen?"
            secondary: "{{ states('input_text.essensplan') }}"
            icon: mdi:food-apple
            icon_color: red
            tap_action:
              action: more-info
              entity: input_text.essensplan
          - type: custom:chores-card
            title: Putzplan / Haushalt
            entities:
              - entity: sensor.chore_staubsaugen
              - entity: sensor.chore_mull_raustragen
          - type: custom:mushroom-template-card
            primary: "📌 Familien-Pinnwand"
            secondary: "{{ states('input_text.pinnwand') }}"
            icon: mdi:note-text
            icon_color: purple
            tap_action:
              action: more-info
              entity: input_text.pinnwand

  # TAB 3: KALENDER IM VOLLBILD-MONATSMODUS
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
