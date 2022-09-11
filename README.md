# ESPHome-Smart-Scale
An ESPHome based Smart Scale.

## What you need

* Any standard scale of choice
* An HX711 ADC converter
* An ESP8266, ESP32 or similar board
  * A power supply (preferrably USB)

## Flashing the firmware

* Create a new ESPHome configuration YAML file
* Copy the contents of [esphome_hx711_smart_scale.yaml](esphome_hx711_smart_scale.yaml) into it
* Customize the basic device cnfiguration (Board, WiFi, etc.) to your liking
* Flash it onto your ESP based board

## Wiring

Connect the load cells to the HX711, and the ESP board to the HX711 as shown:

![Wiring diagram for a 4x50kg load cell configuration](https://circuitjournal.com/img/articles/10_50kg_load_cell/HX711_4x50kg_load_cell_diagram.jpg?v=10)
Credit: https://circuitjournal.com/img/articles/10_50kg_load_cell/HX711_4x50kg_load_cell_diagram.jpg

## Connect to Home Assistant

Add the ESPHome device to Home Assistant via the "Integrations" page.
The sensors and buttons will automatically appear.

## Initial Calibration

### Preparations

To do the initial calibration we need to be able to read the raw sensor values of the
HX711 sensor in real time. We can do this a couple of ways, f.ex. by

* plugging the ESP in via USB and using a serial monitor
* reading the ESPHome logs
* temporarily exposing the raw sensor to Home Assistant (set `internal: False` in the YAML config)

### 0 Kg baseline

We can then take the baseline measurement by simply letting the scale sit for a couple of seconds and writing
down the raw sensor value at this time. Don't worry about the value fluctuating a bit, the measurement doesn't
need to be super accurate since it will be automatically adjusted later-on.

In my case the value is `-481989` (a negative number). This means a raw sensor value of `-481989` correlates
with a 0 Kg load on the scale: `-481989 -> 0`

### Known Mass measurement

To be able to extrapolate the whole curve we need to measure a second point on it. To do this the first 
thing we need is a weight with a known mass. Again there are multiple options to choose from:

* your own body weight as measured with a "normal" scale
* a water tank filled to 10 or 20 liters

It should be noted though, that the measurement error does increase the closer the two measurements are to each other.
So my personal recommendation would be to just measure yourself on a standard scale and use yourself as a "known mass".

In my case the raw sensor value for a known mass of 88.3 Kg (my body weight) is `1339163` (not negative anymore).
This means a raw sensor value of `1339163` correlates with a 88.3 Kg load on the scale:

`1339163 -> 88.3`

### Applying this to the YAML config

Lastly we need to set those measurements in the YAML configuration:

```yaml
...

globals:
  - id: initial_zero
    type: float
    restore_value: yes
    # NOTE: make sure to align this value to the one used in "calibrate_linear" below!
    initial_value: '-481989'

...
```

```yaml
...
  - platform: template
    name: "Smart Scale HX711 Value"
    ...
    filters:
      ...
      - calibrate_linear:
          - -481989 -> 0
          - 1339163 -> 88.3
      ...
```

After that, upload the firmware again and you should be good to go!

## Auto Tare

Since the raw values will drift slightly due to room temperature etc., over time the scale might 
shift up or down a bit. To combat this you can use the "Auto Tare" feature, which will automatically
shift the whole curve to keep it at 0 Kg. For this to work the approximate 0 Kg value needs to be set
in the YAML config, and the adjustment is only done while no weight is applied to the scale (0 ± 10 Kg).

This process is intentionally very slow, so don't expect it to adjust within a minute. In practice
no big differences should occur though, if the scale is powered at all times.

## Manual Tare

If the Auto Tare is too slow or you want to force a manual tare at any point you can use the "Manual Tare"
toggle. This will immediately adjust the weight curve to make the current weight be the 0 Kg value.


# License

CC0 see [LICENSE](LICENSE)
