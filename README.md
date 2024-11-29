# Visualisatie met Grafana: oefeningen

## Setup

1. Ga in een Command prompt naar de folder waarin deze README staat.

> Tip: nog twee extra manieren om een command prompt in een bepaalde folder te openen vanuit Verkenner:
> 1. in Verkenner/Explorer, druk Shift in terwijl je rechtsklikt op een leeg deel van de folder. Kies dan 'open in Terminal'
> 2. ga in Verkenner/Explorer naar de navigatiebalk en vervang alles door 'cmd'

2. Start alle Docker containers via
`docker compose up --build` in de folder van deze README. Dit start 5 containers die met mekaar communiceren, namelijk Grafana en de 4 containers uit de time series oefeningen (Dummy datasource, mosquitto, telegraf, en influxdb).

> Als je later opnieuw wil beginnen,
> - stop je alles (Ctrl-C)
> - verwijder je de folder `influxdb2_data` en `grafana_data` die aangemaakt werden
> - start je alles opnieuw (`docker compose up`)

3. Ga naar Grafana: [http://localhost:3000](http://localhost:3000) en login met `admin`/`admin`. Grafana vraagt om het wachtwoord te wijzigen; dat kan je overslaan met 'Skip'.


4. Voeg InfluxDB als databron toe: ga naar 'Connections' > 'Add new connection'. Kies 'InfluxDB' als type. Klik op 'Add new data source'. Gebruik volgende configuratie:
    - Query language: Flux
    - URL: http://influxdb:8086
    - Basic auth: uit
    - Organization: infm
    - Token: admintoken
    - Default bucket: timeseries

## Oefeningen

### Oefening 1

Maak een nieuw dashboard en een visualisatie-panel. Gebruik influxdb als databron, en geef volgende query in:
```flux
from(bucket: "timeseries")
|> range(start: v.timeRangeStart, stop: v.timeRangeStop)
|> filter(fn: (r) => r.deviceid == "water_temp_sensor_01")
```
om alle sensordata te visualiseren.

Merk op: in plaats van een start- en stoptijd op te geven, gebruiken we de variabelen `v.timeRangeStart` en `v.timeRangeStop`. Die worden automatisch gezet op wat in de Grafana-interface gekozen wordt door de gebruiker (bv. de laatste 5 minuten).

Zet rechts bovenaan de tijd op 'Last 5 minutes'. Druk rechtsbovenaan op `Refresh` indien je geen grafiek ziet.

### Oefening 2

Verken de opties in het rechterpaneel.
- Speel bijvoorbeeld eens met de 'Fill opacity', 'Gradient mode', .... om de grafiek onder de lijn op te vullen.
- Voeg een unit (Celcius) toe aan de meetwaarden.
- Verander de naam in de legende
- Zet het kleurenschema op 'From thresholds (by value)'. Wat doet dit? Waar kan je de thresholds wijzigen?

Bewaar je dashboard. 

### Oefening 3

Voeg nog een visualisatie-paneel toe. Verken de andere types grafieken.

Probeer bijvoorbeeld eens een 'State timeline' uit, die aangeeft wat de toestand van de actuatoren is.
Je kan volgende query gebruiken:
```flux
from(bucket: "timeseries")
|> range(start: v.timeRangeStart, stop: v.timeRangeStop)
|> filter(fn: (r) => r.type == "actuator")
```

Wat visualiseer je hier?

### Oefening 4

De kleuren van de visualisatie uit oefening 3 zijn niet heel duidelijk.
Dit kan je aanpassen via de 'Value mappings'.
Voeg een mapping toe die een andere kleur toekent aan de waadrden 'on' en 'off'. Doe hetzelfde voor 'open' en 'closed'.

### Oefening 5

Ook de labels van de actuatoren zijn niet heel duidelijk.
Je kan die vervangen bij de 'Display name'.
We kunnen niet gewoon 1 string ingeven, omdat de naam verschilt per actuator.
Je kan daarom de speciale waarde `${__field.labels.deviceid}` gebruiken.
Dat vervangt de naam door de waarde van het deviceid label.

### Oefening 6
Bewaar ook je paneel uit oefening 5. Ga terug naar het dashboard om beide visualisaties samen te zien.
Zet auto refresh aan.