🏠 Das ultimative Home Assistant Familien-Dashboard & Kiosk-SystemDieses Open-Source-Projekt enthält die vollständige Dokumentation zum Aufbau eines intelligenten, familienfreundlichen Smart-Home-Systems. Es besteht aus zwei separaten Raspberry Pis: Einem dedizierten Home Assistant Server und einem an der Wand montierten 7-Zoll-Touch-Display (Kiosk).🏗️ System-Architektur & HardwareDas System wird aus Sicherheits- und Performance-Gründen strikt getrennt:Server-Zentrale (Pi 4 oder 5 empfohlen): Hier läuft das "Gehirn" (Home Assistant OS). Er verwaltet alle Geräte, Automationen und Daten.Display-Kiosk (Pi 3B, 4 oder 5): Hier läuft ein schlankes Raspberry Pi OS, das ausschließlich als interaktiver Touch-Monitor dient.Einkaufsliste / Hardware-KomponentenServer: Raspberry Pi 4 (min. 4GB RAM) oder Pi 5 + offizielles Netzteil.Display: Raspberry Pi 3B, 4 oder 5.Touchscreen: 7-Zoll-Touchscreen mit HDMI (Bild) und USB (Touch-Daten).Speichermedien: 2x MicroSD-Karten (Min. 32GB, empfohlen: SanDisk Max Endurance).Kabel: Passende HDMI-Kabel und USB-Verbindungskabel für das Display.⚠️ WICHTIGE WARNHINWEISE (Bitte unbedingt lesen!)⚡ Strom-Unterversorgung (Undervoltage): Touchscreens ziehen viel Strom über die USB-Ports des Raspberry Pi. Nutze für beide Pis zwingend die offiziellen Netzteile. Ein billiges Handyladegerät führt zu unvorhersehbaren Abstürzen und zerstörten SD-Karten.💾 SD-Karten-Verschleiß: Home Assistant schreibt permanent Logdateien. Verwende billige SD-Karten, geben diese oft nach wenigen Monaten den Geist auf. Nutze "High Endurance"-Karten oder schließe eine SSD über USB an den Server an.🔥 Hitzeentwicklung: Wenn der Display-Pi hinter dem Touchscreen in ein Wandgehäuse eingebaut wird, droht Hitzestau. Verwende passive Kühlkörper aus Kupfer.🛠️ Schritt 1: Installation des Home Assistant ServersAuf dem Haupt-Pi installieren wir das offizielle Home Assistant OS (HAOS).Lade den Raspberry Pi Imager herunter und installiere ihn: raspberrypi.comStarte den Imager und klicke auf Gerät wählen (z.B. Raspberry Pi 4).Klicke auf Betriebssystem wählen -> Other specific-purpose OS -> Home assistants and home automation -> Home Assistant OS.Wähle deine SD-Karte aus und klicke auf Weiter/Schreiben.Stecke die geflashte Karte in den Server-Pi, verbinde ihn per Netzwerkkabel (LAN) mit deinem Router und schließe den Strom an.Warte ca. 10 Minuten. Öffne an deinem Computer einen Browser und rufe die Adresse auf: http://homeassistant.local:8123 (Alternativ die IP-Adresse deines Pis). Folge den Anweisungen auf dem Bildschirm, um dein Administratorkonto zu erstellen.📺 Schritt 2: Einrichtung des extra Display-Pis (Kiosk-Modus)Auf diesem Pi installieren wir nicht Home Assistant, sondern ein normales Betriebssystem, welches sich beim Starten automatisch mit der Server-Zentrale verbindet.Hardware-VerkabelungVerbinde den HDMI-Ausgang des Pis mit dem HDMI-Eingang des 7" Displays.Verbinde den USB-Port des Displays (oft beschriftet mit Touch) mit einem USB-Port des Pis.Schließe das offizielle Netzteil an den Pi an.Betriebssystem & Kiosk-SoftwareÖffne den Raspberry Pi Imager.Wähle Raspberry Pi OS (64-bit) (mit Desktop-Oberfläche).Klicke auf das Zahnrad-Symbol (Einstellungen), um dein WLAN-Passwort, Spracheinstellungen und SSH direkt vorab zu konfigurieren. Schreibe die Karte.Stecke die Karte in den Display-Pi und starte ihn. Öffne das Terminal (Eingabeaufforderung) auf dem Pi oder logge dich per SSH ein.Konfiguration für Raspberry Pi 4 und Pi 5 (Wayland-Grafiksystem):Installiere das Tool, um den Mauszeiger bei Inaktivität unsichtbar zu machen:bashsudo apt update && sudo apt install unclutter -y
Verwende Code mit Vorsicht.Öffne die Autostart-Konfiguration:bashnano ~/.config/wayfire.ini
Verwende Code mit Vorsicht.Scrolle ganz nach unten und füge folgenden Block hinzu (Ersetze die IP-Adresse mit der IP deines Home Assistant Servers):ini[autostart]
kiosk = chromium-browser --kiosk --noerrdialogs --disable-infobars --no-first-run http://DEINE_HA_SERVER_IP:8123/dashboard-familie?kiosk
unclutter = unclutter -idle 0.1 -root
Verwende Code mit Vorsicht.Speichern mit Strg + O, Bestätigen mit Enter, Verlassen mit Strg + X.Konfiguration für Raspberry Pi 3B (Älteres X11-Grafiksystem):Da der Pi 3B weniger Leistung hat, optimieren wir hier den Autostart:Öffne die LXDE-Autostartdatei:bashnano ~/.config/lxsession/LXDE-pi/autostart
Verwende Code mit Vorsicht.Füge diese Zeilen ein, um den Bildschirmschoner zu blockieren und Chromium direkt im Vollbild zu starten:bash@xset s off
@xset -dpms
@xset s noblank
@chromium-browser --kiosk --noerrdialogs --disable-infobars http://DEINE_HA_SERVER_IP:8123/dashboard-familie?kiosk
Verwende Code mit Vorsicht.🧩 Schritt 3: Funktionen auf dem Server einrichtenGehe an deinen Computer und öffne die Home Assistant Oberfläche (http://homeassistant.local:8123).1. HACS installieren (Erweiterte Funktionen)Der Home Assistant Community Store (HACS) schaltet alle benutzerdefinierten Karten frei.Offizielle Anleitung: hacs.xyz/docs/setup/downloadKurzanleitung: Installiere das Add-on "Advanced SSH & Web Terminal" in Home Assistant. Öffne das Terminal und gib diesen Befehl ein: wget -O - hacs.xyz | bash -. Starte Home Assistant danach neu.2. Design-Karten über HACS herunterladenNavigiere in Home Assistant zu HACS -> Frontend -> Klicke unten rechts auf das "+"-Symbol und suche nach:Mushroom Cards: Bietet große, touch-optimierte Schalter für deine Lichter.Atomic Calendar Revive: Die beste, sich automatisch aktualisierende Kalenderkarte.Kiosk Mode: Blendet die obere und seitliche Menüleiste automatisch aus, sobald die URL den Zusatz ?kiosk enthält.3. Google Family Kalender verknüpfen (Automatische Updates)Damit sich der Kalender im Hintergrund permanent ohne dein Zutun aktualisiert, nutzen wir die offizielle Google Calendar API.Rufe die Google Cloud Console auf und erstelle ein neues Projekt.Suche nach Google Calendar API und aktiviere diese für dein Projekt.Gehe auf OAuth-Zustimmungsbildschirm, wähle "Extern" und trage deine E-Mail-Adresse ein.Klicke auf Anmeldedaten -> Anmeldedaten erstellen -> OAuth-Client-ID.Wähle als Anwendungstyp Webanwendung.Füge unter Autorisierte Umleitungs-URIs exakt diesen Link hinzu: home-assistant.io.Kopiere die generierte Client-ID und das Client-Secret.Wechsel zu Home Assistant -> Einstellungen -> Geräte & Dienste -> Integration hinzufügen -> Google Calendar und füge deine Daten dort ein. Bestätige den Zugriff mit deinem Google-Konto. Dein Familienkalender wird nun als Entität calendar.family bereitgestellt.🎨 Schritt 4: Dashboard-Code (YAML)Erstelle unter Einstellungen -> Dashboards ein neues Dashboard mit dem Namen dashboard-familie. Klicke oben rechts auf die drei Punkte -> Dashboard bearbeiten -> Raw-Konfigurationseditor und verwende diesen Beispielcode:yamlviews:
  - title: Übersicht
    path: home
    type: sections
    cards:
      - type: custom:mushroom-light-card
        entity: light.wohnzimmer_licht
        name: Wohnzimmer Licht
        show_brightness_control: true
        use_light_color: true
        layout: vertical

      - type: custom:atomic-calendar-revive
        entities:
          - entity: calendar.family
        name: Familien-Termine
        showCurrentEventLine: true
        maxDaysToShow: 7
        tap_action:
          action: navigate
          navigation_path: /dashboard-familie/kalender-vollbild

  - title: Kalender Vollbild
    path: kalender-vollbild
    type: panel
    cards:
      - type: custom:atomic-calendar-revive
        entities:
          - entity: calendar.family
        showMonth: true
        enableDashboardMode: true
Verwende Code mit Vorsicht.Info zum "Ein-Klick-Kalender": Wenn du auf dem Touchscreen auf die kleinere Kalenderliste tippst, springt die Anzeige sofort in den Vollbild-Kalendermodus (kalender-vollbild).
