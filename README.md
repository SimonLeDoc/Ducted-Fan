# 🚁 Double Propeller Ducted-Fan (DPDF) - Flight Control System

Questo repository contiene il codice sorgente, i dati sperimentali e i modelli matematici sviluppati per il controllo dell'assetto di un drone intubato a propulsione coassiale (VTOL). 

A causa della complessa aerodinamica interna del condotto, la modellistica analitica classica è stata scartata in favore di un approccio **Data-Driven (System Identification)**. Il progetto si articola nello sviluppo di un firmware Real-Time per l'acquisizione telemetrica, l'estrazione di modelli *Black-Box* tramite MATLAB e la successiva taratura e implementazione di controllori PIDF.

## 📂 Struttura del Repository

Il progetto è diviso in 4 macro-cartelle principali:

* **`/Firmware`** 💻
  Contiene il progetto sviluppato in C su STM32CubeIDE per il microcontrollore **STM32H7**. Implementa un'architettura asincrona ad eventi:
  * Lettura IMU (BNO055) via I2C a 400kHz.
  * Loop di controllo deterministico subordinato a un Timer hardware (50Hz / $T_s = 20ms$).
  * Algoritmo PID con filtro passa-basso sulla derivata (PIDF) e Anti-Windup.
  * Mixer per i servomotori (Flap) con saturazione meccanica di sicurezza ($\pm30^\circ$).

* **`/Telemetria`** 📊
  Contiene i file di log in formato `.csv` acquisiti durante i test ad anello aperto (Open-Loop). I dataset includono:
  * Test fisici per l'analisi dei limiti strutturali (saturazione termica e Ground Effect).
  * Test PRBS (*Pseudo-Random Binary Sequence*) per l'eccitazione degli assi di Rollio e Beccheggio (SISO).
  * Test combinati (MIMO) per l'analisi del *Cross-Coupling* aerodinamico.

* **`/Modello_Matematico`** 🧮
  Script MATLAB dedicati all'analisi dei dati telemetrici. Tramite il *System Identification Toolbox*, i dati PRBS vengono elaborati per estrarre funzioni di trasferimento (LTI) che mappano il comportamento dinamico del velivolo in configurazione di Meso-Gravità.

* **`/Simulazione`** 📈
  Script MATLAB (es. `Plotter_Tesi.m`, `IDENTIFY_AND_TUNE.m`) per il tuning automatico dei controllori. Include la simulazione della risposta a gradino ad anello chiuso (*Closed-Loop*) e l'analisi del *Control Effort* per garantire che le richieste del controllore rispettino i limiti fisici degli attuatori (evitando la saturazione).

## 🛠️ Hardware Utilizzato
* **Microcontrollore:** STM32 Nucleo-H745ZI-Q
* **Sensori:** IMU Bosch BNO055, ToF VL53L0X
* **Attuatori:** 2x Motori Brushless controrotanti (Throttle), 4x Servomotori (Roll & Pitch Flaps)
* **Alimentazione:** Doppia batteria LiPo 4S con step-down DC-DC separati per logica e potenza.

## 🚀 Come utilizzare questo repository

1. **Analisi Dati e Tuning:**
   * Aprire MATLAB.
   * Eseguire lo script di Identificazione nella cartella `/Simulazione`.
   * Selezionare un log PRBS dalla cartella `/Telemetria`.
   * Validare il modello e i guadagni $K_p, K_i, K_d$ e $\alpha$ tramite i grafici generati.

2. **Implementazione Firmware:**
   * Aprire il progetto in STM32CubeIDE.
   * Inserire i nuovi guadagni calcolati nel file `config.c`.
   * Compilare e flashare la scheda STM32.

## 👥 Autori e Riconoscimenti
Progetto realizzato per il corso di **Laboratorio di Automazione** (Università Politecnica delle Marche).
* **Studenti:** Di Tizio Nicolas, Marcucci Simone, Morresi Tommaso, Nalli Alberto
* **Professore:** Prof. Andrea Bonci
* **Dottorando:** Dott. Alessandro Di Biase
