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

# License

CC0 see [LICENSE](LICENSE)
