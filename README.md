# Chamber Temperature Controller (CTC) for Klipper

A lightweight, fully‑automated chamber‑temperature mitigation system.  (or any Klipper‑based firmware with similar config structure).

CTC monitors chamber temperature in the background and dynamically boosts the filter fan during temperature excursions. It restores the fan to the user’s chosen speed once conditions return to normal. The goal: **stable chamber temps without overridden fan settings**.

Code devloped on a QIDi Xmax 3 running freeDi Firmware. 

---

## ✨ Features

### **Automatic chamber temperature protection**
- Runs silently every 30 seconds while printing or paused.
- Boosts filter fan speed when chamber temperature exceeds warning or severe thresholds.

### **Respects user fan settings**
- Saves the current fan speed before boosting.
- Restores it automatically after the excursion ends.

### **Adaptive thresholds**
- As the chamber target approaches chamber max, the severe threshold shrinks.
- Prevents unnecessary warnings when printing near chamber max.

### **Three operating states**
- **NORMAL** – No excursion; fan returns to user setting.
- **WARNING** – Chamber > target + buffer; proportional fan boost.
- **SEVERE** – Chamber > max − buffer; fan forced to 100%.

### **Configurable behavior**
- Enable/disable CTC externally.
- Adjustable warning and severe buffers.
- Optional operation during “standby”.

---

## 📊 Excursion Logic

### **WARNING**
**Trigger:**  
`Chamber Temp > Target + warning_buffer`

- Default buffer: **3°C**
- Fan boost: **+20% per °C** above threshold  
  (clamped between fan_off_below and 100%)

---

### **SEVERE**
**Trigger:**  
`Chamber Temp > (Chamber Max − severe_buffer)`

- Default buffer: **5°C**
- Fan forced to **100%**

---

### **NORMAL**
**Trigger:**  
Chamber returns below thresholds

**Action:**  
Fan restored to saved user speed

---

## 🛠 Installation

### 1. Add the macros to your Klipper config
Insert both macro blocks into your printer’s `.cfg` files:
[delayed_gcode CHAMBER_TEMP_CONTROLLER]
[delayed_gcode CHAMBER_TEMP_CONTROLLER_LOOP]

### 2. Ensure required config sections exist

Attributes That May Need Adjustment:

Code was developed on QIDI Xmax 3 with so the following may need adjustment.

Chamber Fan
Current: fan_generic filterfan  
Alternatives:
fan_generic chamber_fan
fan_generic exhaust_fan
fan_generic nevermore
fan_generic aux_fan
or other.

Chamber Heater / Sensor
Current: heater_generic chamber  
Alternatives:
heater_generic chamber_heater
heater_generic enclosure
temperature_sensor chamber
temperature_sensor enclosure
or other

Fan Minimum Power Attribute:
Current: off_below               Alternatives:  'min_power' or others  
Current 'fan_generic filterfan'  Alternative:  'heater_generic chamber' or others.

#### `fan_generic filterfan`
- Must include `speed` and `off_below`

#### `heater_generic chamber`
- Must include `temperature`, `target`, and `max_temp`

#### `[respond]`
- Required for `M118` console messages

### 3. Restart Klipper
CTC will automatically start with Klipper.

### 4. Behavior
CTC runs only during:
- `"printing"`
- `"paused"`
- (Optional) `"standby"` — add to the list in the loop macro

---

## ⚙ Configuration Variables

| Variable           | Default | Description                                 |
|-------------------|---------|---------------------------------------------|
| `ctc_enabled`     | 1       | Master enable/disable switch                |
| `warning_buffer`  | 3.0     | °C above target before WARNING              |
| `severe_buffer`   | 5.0     | °C below max before SEVERE                  |
| `base_fan_speed`  | -1      | Auto‑managed; do not modify                 |
| `boost_fan_speed` | 0       | Auto‑managed                                |
| `state`           | 0       | 0=NORMAL, 1=WARNING, 2=SEVERE               |

---

## 📣 Console Messages

CTC logs messages only during excursions:

- `CTC WARNING: Chamber XX.X°C > YY.Y°C, FilterFan boosted to ZZ%`
- `CTC SEVERE: Chamber XX.X°C > YY.Y°C, FilterFan boosted to 100%`
- `CTC NORMAL: Chamber XX.X°C, FilterFan returned to NN%`

---

## 🧪 Tested On
- QIDI X‑Max 3
- FreeDI Firmware
- Standard QIDI filter fan and chamber heater definitions

---

## 📄 License
none

---

## 🤝 Contributing
Pull requests are welcome!  
If you improve logic, add printer compatibility, or optimize thresholds, feel free to submit a PR.
