# POO para Sistemas Embarcados com ESP32 🤖

Treinamento de **Programação Orientada a Objetos (C++)** aplicada a sistemas embarcados, usando **ESP32 + VS Code + PlatformIO**.
Ao final você terá construído um robô cujo `main.cpp` tem poucas linhas, porque toda a complexidade fica encapsulada em classes.

> Siga as etapas **na ordem**. Cada etapa tem o material do curso (📄) e referências externas para aprofundar (📖).

---

## 🗺️ Roteiro

| # | Etapa | Material do curso |
|---|-------|-------------------|
| 0 | Objetivos do curso | [1-objetivos.md](1-objetivos.md) |
| 1 | Instalar VS Code + Extensão PlatformIO | [12-mod0.md](12-mod0.md) |
| 2 | Driver USB-Serial e Permissões (Linux/Windows) | [10-Tutorial-ESP32.md §2](10-Tutorial-ESP32.md#2-instalar-o-driver-usb-serial-e-permissões) |
| 3 | Identificar a placa (*board*) | [10-Tutorial-ESP32.md §3](10-Tutorial-ESP32.md#3-identificar-a-sua-placa-escolher-o-board) |
| 4 | Abrir projetos e criar novo no PlatformIO | [10-Tutorial-ESP32.md §4–5](10-Tutorial-ESP32.md#4-como-abrir-e-selecionar-projetos-no-vs-code) |
| 5 | Configurar o `platformio.ini` (pioarduino) | [10-Tutorial-ESP32.md §6](10-Tutorial-ESP32.md#6-configurar-o-platformioini) |
| 6 | Terminal do PlatformIO *(Por que o terminal normal não roda `pio`?)* | [10-Tutorial-ESP32.md §7](10-Tutorial-ESP32.md#7-o-terminal-do-platformio-por-que-o-terminal-comum-não-roda-pio) |
| 7 | Gravar (flashar) a ESP32 (Botões da GUI e Comandos) | [10-Tutorial-ESP32.md §8–10](10-Tutorial-ESP32.md#10-compilar-e-gravar-flashar-botões-da-gui-vs-comandos) |
| 8 | Usar o Serial Monitor (GUI, comandos e liberação de porta) | [10-Tutorial-ESP32.md §11](10-Tutorial-ESP32.md#11-como-usar-o-serial-monitor-gui-vs-terminal) |
| 9 | Estrutura de projeto e modularização (`.h` / `.cpp`) | [organizacao-platformio.md](organizacao-platformio.md) · [12-Apresentacao.md](12-Apresentacao.md) |
| 10 | *(Opcional)* Autocompletar avançado com clangd | [clangd_esp32_setup_walkthrough.md](clangd_esp32_setup_walkthrough.md) |
| 11 | Módulo 1: Classes, Objetos e Encapsulamento | [13-mod1.md](13-mod1.md) |
| 12 | Módulo 2: Agregação (A Classe Robô) | [14-mod2.md](14-mod2.md) |
| 13 | Módulo 3: Herança, Polimorfismo e Sistemas Não-Bloqueantes | [15-mod3.md](15-mod3.md) |
| 14 | Módulo 4: Grand Finale (Integração) | [16-mod4.md](16-mod4.md) |
| — | Projeto completo de referência | [Exemplo/](Exemplo/) |
| — | Plano de aula (para instrutores) | [11-Plano-de-Aula.md](11-Plano-de-Aula.md) |

---

## Passo a passo

### Etapa 0 — Entenda o objetivo
📄 [1-objetivos.md](1-objetivos.md)

---

### Etapa 1 — Instalar o ambiente (VS Code + PlatformIO)
📄 [12-mod0.md — Instalando a extensão PlatformIO](12-mod0.md)

- [ ] Instalar o VS Code
- [ ] Instalar o Git
- [ ] Instalar a extensão **PlatformIO IDE** no VS Code e reiniciar a janela

📖 Para estudar:
- [VS Code — Download](https://code.visualstudio.com/)
- [PlatformIO IDE para VS Code — Instalação](https://docs.platformio.org/en/latest/integration/ide/vscode.html#installation)
- [O que é o PlatformIO?](https://docs.platformio.org/en/latest/what-is-platformio.html)

---

### Etapa 2 — Driver USB-Serial e Permissões
📄 [10-Tutorial-ESP32.md §2](10-Tutorial-ESP32.md#2-instalar-o-driver-usb-serial-e-permissões)

- [ ] Identificar o chip conversor (CP2102, CH340, etc.) e instalar o driver no Windows/macOS
- [ ] **Linux:** Instalar as regras `udev` do PlatformIO, entrar no grupo `dialout` e remover o pacote conflitante `brltty`

📖 Para estudar:
- [Driver CP210x (Silicon Labs)](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers)
- [Driver CH340/CH341 (WCH)](https://www.wch-ic.com/downloads/CH341SER_EXE.html)
- [PlatformIO — Regras udev no Linux](https://docs.platformio.org/en/latest/core/installation/udev-rules.html)

---

### Etapa 3 — Escolher a placa (*board*)
📄 [10-Tutorial-ESP32.md §3](10-Tutorial-ESP32.md#3-identificar-a-sua-placa-escolher-o-board)

- [ ] Identificar o módulo físico (ex.: ESP32-WROOM-32 → `esp32dev`; ESP32-CAM → `esp32cam`)

📖 Para estudar:
- [PlatformIO — Placas Espressif 32](https://docs.platformio.org/en/latest/boards/index.html#espressif-32)
- [PlatformIO — Placa `esp32dev`](https://docs.platformio.org/en/latest/boards/espressif32/esp32dev.html)
- [Random Nerd Tutorials — Pinagem da ESP32](https://randomnerdtutorials.com/esp32-pinout-reference-gpios/)

---

### Etapa 4 — Abrir projetos no VS Code e Criar Novo Projeto
📄 [10-Tutorial-ESP32.md §4–5](10-Tutorial-ESP32.md#4-como-abrir-e-selecionar-projetos-no-vs-code)

- [ ] **Como abrir:** Abrir a pasta que contém o `platformio.ini` via **File > Open Folder...** para ativar a barra do PlatformIO
- [ ] **Como selecionar:** Alternar ambiente/projeto clicando no botão `env:...` na barra inferior de status
- [ ] **Criar novo:** Criar projeto pelo assistente gráfico **PIO Home → New Project**

📖 Para estudar:
- [PlatformIO — Quick Start no VS Code](https://docs.platformio.org/en/latest/integration/ide/vscode.html#quick-start)
- [Random Nerd Tutorials — VS Code + PlatformIO com ESP32](https://randomnerdtutorials.com/vs-code-platformio-ide-esp32-esp8266-arduino/)

---

### Etapa 5 — Configurar o `platformio.ini`
📄 [10-Tutorial-ESP32.md §6](10-Tutorial-ESP32.md#6-configurar-o-platformioini) · Exemplo: [Exemplo/platformio.ini](Exemplo/platformio.ini)

- [ ] Usar a plataforma atualizada **pioarduino** (Arduino core 3.x)
- [ ] Configurar `monitor_speed = 115200`
- [ ] Deixar a porta em detecção automática (comentar linhas fixas como `upload_port = COM9` se estiver no Linux)

📖 Para estudar:
- [PlatformIO — Configuração do `platformio.ini`](https://docs.platformio.org/en/latest/projectconf/index.html)
- [pioarduino — Repositório platform-espressif32](https://github.com/pioarduino/platform-espressif32)

---

### Etapa 6 — Usar o Terminal do PlatformIO
📄 [10-Tutorial-ESP32.md §7](10-Tutorial-ESP32.md#7-o-terminal-do-platformio-por-que-o-terminal-comum-não-roda-pio)

- [ ] Entender por que o terminal padrão do sistema dá `pio: command not found`
- [ ] Abrir o terminal específico do PlatformIO clicando no ícone `>_` da barra inferior ou pelo menu da formiga 🐜

---

### Etapa 7 — Gravar (flashar) a ESP32
📄 [10-Tutorial-ESP32.md §8–10](10-Tutorial-ESP32.md#10-compilar-e-gravar-flashar-botões-da-gui-vs-comandos)

- [ ] Gravar pela GUI: clicar no ícone de seta **Upload (→)** na barra azul inferior
- [ ] Gravar pelo Terminal PIO: executar `pio run -t upload`
- [ ] Entender o modo manual: segurar o botão físico **BOOT** caso trave em `Connecting...`

📖 Para estudar:
- [esptool — Seleção de Modo de Boot](https://docs.espressif.com/projects/esptool/en/latest/esp32/advanced-topics/boot-mode-selection.html)
- [esptool — Guia de Troubleshooting](https://docs.espressif.com/projects/esptool/en/latest/esp32/troubleshooting.html)

---

### Etapa 8 — Serial Monitor: Interface e Linha de Comando
📄 [10-Tutorial-ESP32.md §11](10-Tutorial-ESP32.md#11-como-usar-o-serial-monitor-gui-vs-terminal)

- [ ] Abrir pela GUI: clicar no ícone de tomada/plug (🔌) na barra inferior
- [ ] Abrir pelo Terminal PIO: executar `pio device monitor`
- [ ] **Liberar a porta:** fechar com `Ctrl+C` ou na lixeirinha 🗑️ do terminal antes de fazer um novo upload

---

### Etapa 9 — Estrutura de Projeto e Modularização
📄 [organizacao-platformio.md](organizacao-platformio.md) · [12-Apresentacao.md](12-Apresentacao.md)

- [ ] Compreender a função de `src/`, `include/` e `lib/`
- [ ] Uso correto de `.h` (declarações e include guards) e `.cpp` (implementação)

📖 Para estudar:
- [PlatformIO — Diretórios do Projeto](https://docs.platformio.org/en/latest/projectconf/sections/platformio/options/directory/index.html)
- [LearnCpp — Header files](https://www.learncpp.com/cpp-tutorial/header-files/)
- [LearnCpp — Header guards](https://www.learncpp.com/cpp-tutorial/header-guards/)

---

### Etapa 10 — *(Opcional)* Autocompletar com clangd
📄 [clangd_esp32_setup_walkthrough.md](clangd_esp32_setup_walkthrough.md)

📖 Para estudar:
- [clangd — Instalação e Configuração](https://clangd.llvm.org/installation)

---

### Etapa 11 — Módulo 1: Classes, Objetos e Encapsulamento
📄 [13-mod1.md](13-mod1.md) · Código: [Led.h](Exemplo/include/Led.h) e [Led.cpp](Exemplo/src/Led.cpp)

📖 Para estudar:
- [LearnCpp — Introdução à POO](https://www.learncpp.com/cpp-tutorial/introduction-to-object-oriented-programming/)
- [LearnCpp — Classes e Objetos](https://www.learncpp.com/cpp-tutorial/introduction-to-classes/)
- [LearnCpp — Construtores](https://www.learncpp.com/cpp-tutorial/introduction-to-constructors/)
- [Arduino-ESP32 — API GPIO](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/gpio.html)

---

### Etapa 12 — Módulo 2: O Robô e a Agregação
📄 [14-mod2.md](14-mod2.md) · Código: [Motor.h](Exemplo/include/Motor.h) e [Robo.h](Exemplo/include/Robo.h)

📖 Para estudar:
- [LearnCpp — Composição de Objetos](https://www.learncpp.com/cpp-tutorial/object-composition/)
- [LearnCpp — Agregação](https://www.learncpp.com/cpp-tutorial/aggregation/)
- [Arduino-ESP32 — API LEDC (PWM para motores)](https://docs.espressif.com/projects/arduino-esp32/en/latest/api/ledc.html)

---

### Etapa 13 — Módulo 3: Herança, Polimorfismo e Sistemas Não-Bloqueantes
📄 [15-mod3.md](15-mod3.md)

📖 Para estudar:
- [LearnCpp — Herança em C++](https://www.learncpp.com/cpp-tutorial/introduction-to-inheritance/)
- [LearnCpp — Polimorfismo e Funções Virtuais](https://www.learncpp.com/cpp-tutorial/virtual-functions/)
- [LearnCpp — Interfaces e Classes Abstratas](https://www.learncpp.com/cpp-tutorial/pure-virtual-functions-abstract-base-classes-and-interface-classes/)
- [Arduino — Piscar sem delay (`millis`)](https://docs.arduino.cc/built-in-examples/digital/BlinkWithoutDelay/)

---

### Etapa 14 — Módulo 4: Grand Finale (Integração)
📄 [16-mod4.md](16-mod4.md) · Código completo: [Exemplo/src/main.cpp](Exemplo/src/main.cpp)

---

## ⚡ Comandos rápidos

```bash
pio device list               # Lista as portas seriais conectadas
pio run                       # Compila o projeto (equivalente ao ✓)
pio run -t upload             # Compila e grava na ESP32 (equivalente ao →)
pio device monitor            # Abre o Monitor Serial (equivalente ao 🔌 - Ctrl+C para sair)
pio run -t upload -t monitor  # Grava e abre o monitor em um único comando
pio run -t clean              # Limpa os arquivos de compilação (equivalente ao 🗑️)
pio run -t erase              # Apaga completamente a memória Flash da ESP32
```

## 📁 Estrutura do Repositório

```text
.
├── README.md                          ← Roteiro principal passo a passo
├── 1-objetivos.md                     ← Objetivos pedagógicos
├── 10-Tutorial-ESP32.md               ← Guia prático de placas, projetos e gravação
├── 11-Plano-de-Aula.md                ← Planejamento pedagógico
├── 12-Apresentacao.md                 ← Introdução ao PlatformIO vs. Arduino IDE
├── 12-mod0.md                         ← Instalação do ambiente VS Code
├── 13-mod1.md … 16-mod4.md            ← Módulos teóricos e práticos de POO
├── organizacao-platformio.md          ← Guia de organização de código e pastas
├── clangd_esp32_setup_walkthrough.md  ← Guia de configuração do clangd
└── Exemplo/                           ← Projeto prático completo
    ├── include/                       (Led.h, Motor.h, Robo.h)
    ├── src/                           (main.cpp, Led.cpp, Motor.cpp, Robo.cpp)
    └── platformio.ini
```
