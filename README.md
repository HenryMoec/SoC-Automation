# SoC Automation

Die Zendure Systeme benötigen 100% SoC zur Akkukalibrierung.
Nun ist dieser im Sommer oft schon am Vormittag erreicht und die Systeme stehen über Stunden mit 100% SoC.
Um dies zu vermeiden, habe ich diese Automation geschrieben.
Die Automation prüft alle 30 min auf Basis der Intergration Solcast PV forecast die restliche PV Produktion für den aktuellen Tag. Ist diese 2,5x größer als die Restkapazität des Systems,
wird der SoC auf 90% gesetzt. Fällt die restliche PV PRoduktion des Tages auf <2,5x Restkapazität des Systems (kann angepasst werden), wird der SoC wieder auf 100% gesetzt.
Ziel: Fertigladen erst so spät wie möglich.


Entitäten:
- Schalter zum Aktivieren/Deaktivieren der Automation: input_boolean.soc_automatik

- Gesamt SoC des Systems: sensor.soc_gesamt_sf_pro

- Anzahl an Akkus pro Gerät: sensor.l1_solarflow_2400_pro_batterieanzahl/sensor.l2_solarflow_2400_ac_pack_num/sensor.l3_solarflow_2400_ac_pack_num

- PV Forecast: sensor.solcast_pv_forecast_prognose_verbleibende_leistung_heute

- Max Soc: input_number.gesamt_soc_max (Ich habe hier einen Helfer um die maxSoCs aller Geräte über einen Regler zu steuern. Bei einem Gerät ist dies die maxSoC Entität)

- U100: "{{ ((packsl2 + packsl3) * 2.88 + packsl1 * 2.4) | float(0) | round(2) }}"
  in U100 kann wahlweise auch die Gesamtkapazität des Systems als Zahl hintelegt werden. Ansonsten müssen die Kapa der Packs hier noch angepasst werden

- fac_rest: "{{ (2.5 * rest_to_100) | round(2) }}"  / An dieser Stelle kann der Faktor 2,5 angepasst werden.
  Faktor größer: System läd eher fertig
  Faktor kleiner: System läd später fertig
