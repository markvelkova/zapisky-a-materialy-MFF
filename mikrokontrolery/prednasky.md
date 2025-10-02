> Programování mikrokontrolerů

## vývojová prostředí
- Mikrochip Studio
- VSCOde + PlatformIO

## hardware
- možná bude potřeba opatřit si arduino

# MCU - microcontroller unit
- umí běžet sám, má nějaké vlastní hodiny, jen se zapojí a běží
## z čeho se skládá 
- procesor (MPU - microprocessor unit)
- dráty
- paměti
- ...

## historie
### Intel
- 4004 -> 8048 (MCS48)
### Texas Instruments
- TMS1000
- předek kdovíčeho, pořád se používá
- clock, processor (11+32 instr), instruction ROM (1024\*8), data RAM (64*4), I/O support
  
## architektury
### von Neumann
- jedna paměť, jedna sběrnice
### Harvard
- oddělená programová a datová paměť
- může být rychlejší, protože jsou oddělené linky
- jednodušší instr. sady

## instrukční sety
- RISC - typicky Harvard
- CISC specialitky
  
## programové paměti
- ROM (obsah dán výrobou)
- OTP (one time programmable)
- EPROM
- EEPROM
- FLASH
- externí

## datové paměti
- statické RAM - mohou běžet pomalu
- perzistentní (EEPROM, SRAM s nějakým rezervním zdrojem - vydrží až desítky let)
- externí

## I/O
- všechno

## komunikace
- všemožné sériové linky, SPI - serial peripheral interface
- I2C - sběrnice
- každé nové auto má alespoň tři CAN sběrnice, opravář se napíchne a ví, který modul vyměnit
- USB
- RSxxx

## speciální jednotky
- brown-out detection - zjistí, že umírá a rozumně se ukončí
- power-on delay - hned po sartu by tam byl náhodný obsah
- oscillator control - za běhu si můžou změnit taktovací frekvenci
- timery - nezávisle na vykonávání instrukcí 
  - watchdog timer - **hlídací pes, kterej, když ho nekrmíte, tak vás pokouše**, když ho jednou za čas nezresetuju, zresetuje vše - řeší nekonečné smyčky
- přerušení

# AVR core
- [Instruction set datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/AVR-InstructionSet-Manual-DS40002198.pdf)
- [Celkový datasheet](https://ww1.microchip.com/downloads/en/DeviceDoc/en590320.pdf)
- Harvard - oddělené paměti, oddelené datové prostory
- **něco někde nějak to způsobí, že se něco někde nějak stane**
- standartně 1 instrukce na takt (skoky ne, nemůžeme si dopředu načítat další)
- u každé intrukce, jak dlouho trvá
- ALU vezme v jednom kroku dva argumenty a do jednoho z nich hodí výsledek
## registry
### 32 obecných (general purpose) osmibitových
- jedna půlka použitelná obecně
- další ne tak úplně
- poslední tři dvojice z nich se mohou chápat jako až 16-bitové (kvůli adresování) - **pojmenování X, Y, Z**
### stavový (status) a řídící (control) registr 
- můžu na něj sahat rovnou (nějaký flagy atd)
- stavový pro ALU 
  - musí se uložitt a obnovit při interrruptu
  - mimo jiné je ta flag, který povoluje nebo zaakzuje interrupty
  - pak je tam jen manuálně nastavitelný flag - one bit storage pro programátora
- řídící třeba obsahuje důvod posledního resetu

## paměťový model
- **3 adresové prostory**
### program flash
- 0...velikost
### register file (32 obecných) + data SRAM + I/O registry na periferie
- nejdřív 32 registry
- pak 40 I/O
- pak od 0x60 RAM
- **pak ještě existují nějaký externí I/O registry, který se nevešly**
  - některé instrukce mají přístup jen k zákaldním
- **na 88, 168, 328 je SRAM od 0x100**
#### pojmenování registrů
- pro pohodlí jsou pojmenované
- je potřeba při kompilaci převést na adresu
- ale na jakou? na interní v rámci registrů, nebo I/O registrů, nebo na adresu celopaměťovou?

### EEPROM

## stack
- **na začátku si musím zaktualiovat stack pointer** - pokud budu začínat na nule, podteču
- je dobré nastavit si ho na velikost paměti
- argumenty je lepšínedávat pes zásobník - není garantováno, v jakém pořadí se to tam dává, navíc registry jou rychlejší