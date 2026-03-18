# LuxNode: Distributed ESP32 Smart Lighting System

![Status](https://img.shields.io/badge/Status-No%20Longer%20Maintained-red.svg?style=flat&logo=github&logoColor=white)
![Language](https://img.shields.io/badge/language-C%2B%2B-00599C.svg?style=flat&logo=cplusplus&logoColor=white)
![Platform](https://img.shields.io/badge/platform-ESP32%20%7C%20PlatformIO-orange.svg?style=flat&logo=espressif&logoColor=white)
![Protocol](https://img.shields.io/badge/protocol-CAN--BUS%20%7C%20nRF24%20%7C%20WiFi-4caf50.svg)
![Hardware](https://img.shields.io/badge/hardware-OSHWLab-blueviolet?style=flat&logo=oshwlab&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-blue.svg)

# LuxNode Legacy Hardware: ESP32 Distributed Ecosystem

Ten folder zawiera dokumentację techniczną modułów PCB zaprojektowanych dla systemu **LuxNode**. Jest to autorski ekosystem rozproszonej automatyki, oparty na mikrokontrolerach ESP32, wykorzystujący magistralę CAN oraz łączność radiową nRF24L01+.

Caly projekt PCB jest dostepny pod adresami: [oshwlab.com](https://oshwlab.com/JLUK/esp32_can_nrf_copy) | [easyeda.com](https://easyeda.com/editor#project_id=7421841e90244079ad9d0232d063693e) 

> [!IMPORTANT]
> **Status Projektu: Legacy / Archiwalny.** > Warstwa programowa nie jest już rozwijana. Rozwiązania sprzętowe zawarte w tym repozytorium służą jako baza dla nowej generacji systemu dostępnej pod adresami:  
> [ot_app](https://github.com/HareoPL/ot_app) | [ot_app_stm](https://github.com/HareoPL/ot_app_stm) | [ot_app_esp](https://github.com/HareoPL/ot_app_esp)



## 🏗 Przegląd Modułów Systemowych

System został zaprojektowany w architekturze modularnej, dzieląc zadania na jednostki sterujące (interfejs użytkownika) oraz wykonawcze.

### 1. Control Device (Button Unit)
Kompaktowy moduł sterujący zaprojektowany do montażu w standardowej puszce podtynkowej. Stanowi główne ogniwo interakcji użytkownika z systemem.

* **Interfejs:** Wyświetlacz OLED informujący o statusie (PWM, temperatura, CAN/nRF ID, status WiFi, przekaźnik).
* **Komunikacja:** Dual-path (CAN-BUS + nRF24L01+).
* **Sensoryka:** Obsługa czujników temperatury (One-Wire) oraz fizycznych przycisków sterujących.
* **Wyjście:** Zintegrowany przekaźnik do bezpośredniego sterowania obwodem oświetleniowym.

<div align="center">
  <img src="IMG/control_module_1.jpg" alt="Control Unit - Front View" width="250">
  <img src="IMG/control_module_2.jpg" alt="Control Unit - Back View" width="250">
  <p><em>Fotografia zmontowanego urządzenia: widok frontu oraz tyłu płytki.</em></p>
</div>


### 2. Light Device (Feature Controller)
Zaawansowana płyta główna integrująca wiele standardów sterowania w jednym module. Zaprojektowana jako uniwersalny "hub" wykonawczy.

* **Napędy:** Możliwość sterowania dwoma silnikami krokowymi (sloty na sterowniki **DRV8825**).
* **Sekcja LED RGB:** 3 wysokoprądowe kanały MOSFET z pełną izolacją galwaniczną za pomocą układów **TLP250**.
* **Pomiar Mocy:** Zintegrowany czujnik **ACS712** do monitorowania prądu obciążenia na kanałach MOSFET.
* **Zasilanie i Izolacja:**
    * Możliwość wspólnego zasilania z linii LED/Silników.
    * System zworek na PCB pozwalający na całkowitą separację napięć logiki od sekcji mocy.
* **Dodatki:** Wyjście przekaźnikowe, obsługa wyświetlacza, magistrala One-Wire.

<div align="center">
  <img src="IMG/multi_module_1.jpg" alt="Light Device - without nrf" width="250">
  <img src="IMG/multi_module_2.jpg" alt="Light Device - Top View" width="250">
  <img src="IMG/multi_module_3.jpg" alt="Light Device - Bottom View" width="250">
  <p><em>Zmontowana płyta jako moduł oświetlenia</em></p>
</div>
<div align="center">
  <img src="IMG/motor_control.jpg" alt="Stepper Motor Device - view 1" width="250">
  <img src="IMG/motor_control_2.jpg" alt="Stepper Motor Device - View 2" width="250">
  <p><em>Zmontowana płyta jako moduł sterowania silnikami krokowymi.</em></p>
</div>

<div align="center">
  <img src="IMG/schematic_control.png" alt="Control Unit - schemat" width="250">
  <p><em>Fotografia schematu.</em></p>
</div>



## 🔌 Moduły Pomocnicze i Rozszerzenia

W ramach projektu powstał szereg wyspecjalizowanych modułów rozszerzających możliwości jednostek głównych.

### ⚡ Mini Moduł Przekaźnika Wysokonapięciowego
Bezpieczny moduł przełączający dla dużych obciążeń AC/DC.
* **Kluczowanie:** Dwa MOSFETy mocy **IPZ60R040C7** ($600V$, $50A$, $R_{DS(on)} = 40m\Omega$).
* **Izolacja:** Photovoltaic MOSFET Driver **VOM1271T** zapewniający barierę izolacyjną między MCU a wysokim napięciem.
* **Bezpieczeństwo:** Zintegrowany bezpiecznik termiczny.

<div align="center">
  <img src="IMG/ssr_photovoltaic.jpg" alt="SSR Relay Module" width="300">
  <img src="IMG/schematic_ssr_module.png" alt="Control Unit - schemat" width="200">
</div>


### 📡 Moduł Zasilania i Komunikacji GSM (SIM800L)
Dedykowany moduł zapewniający stabilne warunki pracy dla układu SIM800L.
* **Zasilanie:** Precyzyjna sekcja buck ustawiona na $4.128V$ (wymagane dla stabilnej pracy SIM800L przy skokach poboru prądu w trakcie logowania do sieci).
* **Komunikacja:** Interfejs UART (komendy AT).

<div align="center">
  <img src="IMG/sim800_module.jpg" alt="GSM Module" width="300">
  <img src="IMG/schematic_gsm.png" alt="Control Unit - schemat" width="320">
</div>


### 🔅 Zewnętrzny Kanał PWM z Izolacją
Jednokanałowy moduł rozszerzający dla sterowania jasnością LED lub prędkością silników DC.
* **Driver:** Układ **TLP250** zapewniający szybkie przełączanie i ochronę mikrokontrolera przed zakłóceniami z sekcji mocy.
<div align="center">
  <img src="IMG/external_dim_module.jpg" alt="External PWM module - view 1" width="250">
  <img src="IMG/schematic_1ch_dim.png" alt="Control Unit - schemat" width="370">
  <p><em>External PWM module.Fotografia schematu. </em></p>
</div>

#### 🧪 Testy i Walidacja (Bench Testing)
Moduł PWM został poddany testom oscyloskopowym w celu weryfikacji poprawności kluczowania przy wysokich częstotliwościach ($25\text{ kHz}$).

<div align="center">
  <img src="IMG/external_dim_module_test_pwm5.jpg" alt="External PWM module - PWM 5%" width="250">
  <img src="IMG/external_dim_module_test_pwm50.jpg" alt="External PWM module - PWM 50%" width="250">
  <p><em>Badanie czasu narastania oraz opadania zboczy przy czestotliwosci 25kHz .</em></p>
</div>
Testy potwierdziły wysoką stabilność drivera TLP250 oraz czyste zbocza sygnału sterującego obciążeniem LED.



## 🎬 Demo i Prezentacja Systemu

Poniżej przedstawiono finalną formę modułu sterującego (Control Device) po montażu w obudowie oraz materiał wideo prezentujący działanie ekosystemu.

### Control Device w obudowie
Moduł został zaprojektowany z myślą o estetycznym montażu podtynkowym, zapewniając łatwy dostęp do przycisków i czytelność parametrów na wyświetlaczu OLED.

<div align="center">
  <img src="IMG/control_modules_demo_front.jpg" alt="Control Device in housing - Front" width="350">
  <img src="IMG/control_modules_demo_back.jpg" alt="Control Device in housing - Interior" width="350">
  <p><em>Finalna wersja modułu sterującego w obudowie.</em></p>
</div>

### Prezentacja Wideo
Kliknij w poniższy obrazek, aby przejść do prezentacji wideo na platformie YouTube:

[![LuxNode Demo Video](https://img.youtube.com/vi/RxKmKqsKblM/0.jpg)](https://www.youtube.com/watch?v=RxKmKqsKblM)


## 📦 Kompletna Rodzina Modułów LuxNode

Projekt obejmuje pełen zestaw wyspecjalizowanych jednostek, które wspólnie tworzą elastyczny system sterowania oświetleniem i automatyką.

<div align="center">
  <img src="IMG/all_modules.jpg" alt="LuxNode Hardware Family" width="800">
  <p><em>Zestawienie wszystkich modułów zaprojektowanych w ramach projektu.</em></p>
</div>

**Podsumowanie Projektu:** Choć warstwa software'owa (v1.0) nie jest już rozwijana, powyższy hardware stanowi solidną bazę do dalszych eksperymentów z protokołami CAN i nRF24. Wiedza zdobyta przy projektowaniu tych modułów (szczególnie w zakresie izolacji galwanicznej mocy i stabilizacji zasilania GSM) jest obecnie implementowana w nowej generacji ekosystemu [ot_app](https://github.com/HareoPL/ot_app).


## 🌐 Interfejs Web i Konfiguracja Zdalna

Każdy moduł oparty na układzie **ESP32** posiada zintegrowany serwer WWW, który umożliwia pełną personalizację urządzenia z poziomu przeglądarki, bez konieczności używania zewnętrznych programatorów.

<div align="center">
<img src="IMG/control_from_www.jpg" alt="LuxNode Hardware Family" width="600">
<p><em>Panel konfiguracyjny LuxNode Dimmer (v1.0).</em></p>
</div>

### Kluczowe możliwości panelu:

  * **Zarządzanie Magistralami:** Ustawianie ID urządzenia dla CAN-BUS oraz parametrów nRF (kanał, adresy, wzmocnienie anteny).
  * **Zaawansowane Parametry Światła:** Precyzyjna kontrola częstotliwości PWM (np. 25 kHz dla eliminacji migotania) oraz ustawianie czasu płynnego przejścia jasności.
  * **Harmonogramy (Timers):** Konfiguracja dwóch niezależnych timerów dla funkcji *Night Light* z precyzją co do minuty i dnia miesiąca.
  * **Utrzymanie i Sieć:** Obsługa aktualizacji firmware przez WiFi (OTA), konfiguracja trybu AP (Access Point) w przypadku braku lokalnej sieci oraz zapis wszystkich parametrów w pamięci nieulotnej EEPROM.


## 🛠 Specyfikacja Techniczna PCB

* **Oprogramowanie EDA:** EasyEDA.
* **Standardy Komunikacji:**
    * **CAN-BUS:** 500kbps.
    * **nRF24L01+:** 2.4GHz.
    * **UART:** Komendy AT (GSM).
* **Zabezpieczenia:** Izolacja galwaniczna (optoizolatory, drivery bramkowe), zabezpieczenia termiczne, monitorowanie prądu.


## 🚀 Przyszłość Projektu
Hardware LuxNode został sprawdzony w boju i posłużył jako fundament pod nową architekturę **ot_app**. Nowe wersje płytek będą skupiać się na:
1. Przejściu na mikrokontrolery STM32 w sekcjach wykonawczych.
2. Zastosowaniu nowocześniejszych układów komunikacyjnych.
3. Pełnej integracji z nowym interfejsem Dashboardu.


**Projektant:** Jan Łukaszewicz 
