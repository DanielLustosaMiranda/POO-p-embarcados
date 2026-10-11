# Tutorial: Escolher a Placa, Criar o Projeto, Gravar (Flashar) e Usar o PlatformIO na ESP32

Este tutorial é o guia definitivo para sair do zero até ver a sua ESP32 funcionando, cobrindo tanto a **Interface Gráfica (GUI do VS Code)** quanto os **comandos no Terminal do PlatformIO**.

> 📚 Cada etapa possui referências para aprofundamento. O roteiro completo de estudos está no [README.md](README.md).

---

## Sumário

1. [O que você precisa](#1-o-que-você-precisa)
2. [Instalar o driver USB-Serial e Permissões](#2-instalar-o-driver-usb-serial-e-permissões)
3. [Identificar a sua placa (escolher o *board*)](#3-identificar-a-sua-placa-escolher-o-board)
4. [Como abrir e selecionar projetos no VS Code](#4-como-abrir-e-selecionar-projetos-no-vs-code)
5. [Criando um novo projeto no PlatformIO](#5-criando-um-novo-projeto-no-platformio)
6. [Configurar o `platformio.ini`](#6-configurar-o-platformioini)
7. [O Terminal do PlatformIO (Por que o terminal comum não roda `pio`?)](#7-o-terminal-do-platformio-por-que-o-terminal-comum-não-roda-pio)
8. [Descobrir e selecionar a porta serial (GUI e Configuração)](#8-descobrir-e-selecionar-a-porta-serial-gui-e-configuração)
9. [Primeiro código: Blink + Serial](#9-primeiro-código-blink--serial)
10. [Compilar e Gravar (Flashar): Botões da GUI vs. Comandos](#10-compilar-e-gravar-flashar-botões-da-gui-vs-comandos)
11. [Como usar o Serial Monitor (GUI vs. Terminal)](#11-como-usar-o-serial-monitor-gui-vs-terminal)
12. [Modo de gravação manual (Botões BOOT / EN)](#12-modo-de-gravação-manual-botões-boot--en)
13. [Caso especial: ESP32-CAM](#13-caso-especial-esp32-cam)
14. [Tabela de Solução de Problemas Comuns](#14-tabela-de-solução-de-problemas-comuns)

---

## 1. O que você precisa

| Item | Observação |
|------|------------|
| Placa ESP32 | Ex.: *ESP32 DevKit V1* (ESP32-WROOM-32) ou *ESP32-CAM* |
| Cabo USB **de dados** | Cuidado: muitos cabos de celular antigos ou de caixas de som apenas conduzem energia. Se o PC não emitir som ou não listar a porta serial, troque de cabo! |
| VS Code + Extensão PlatformIO IDE | Veja os passos de instalação em [12-mod0.md](12-mod0.md#1-instalando-a-extensão-platformio-no-vs-code) |
| Git | Necessário para download de repositórios e pacotes do compilador |

📖 **Para estudar:**
- [VS Code — Download oficial](https://code.visualstudio.com/)
- [PlatformIO IDE para VS Code — Guia de Instalação](https://docs.platformio.org/en/latest/integration/ide/vscode.html#installation)
- [Git — Download](https://git-scm.com/downloads)

---

## 2. Instalar o driver USB-Serial e Permissões

A ESP32 clássica comunica-se com o computador através de um chip conversor **USB-para-Serial**. Localize o chip retangular soldado próximo à entrada USB da sua placa:

| Chip gravado no circuito | Onde obter o Driver |
|--------------------------|---------------------|
| **CP2102 / CP2104** (Silicon Labs) | [CP210x VCP Drivers](https://www.silabs.com/developers/usb-to-uart-bridge-vcp-drivers) |
| **CH340 / CH341 / CH9102** (WCH) | [CH341SER (Windows)](https://www.wch-ic.com/downloads/CH341SER_EXE.html) / [CH9102/CH343](https://www.wch-ic.com/downloads/CH343SER_EXE.html) |
| **FT232** (FTDI) | [FTDI VCP Drivers](https://ftdichip.com/drivers/vcp-drivers/) |

### Configuração no Linux (Regras udev e Permissões)

No Linux os drivers geralmente estão integrados ao Kernel, porém o sistema bloqueia o acesso de usuários não-root às portas seriais por padrão. Execute no terminal:

```bash
# 1. Baixa as regras de permissão udev do PlatformIO
curl -fsSL https://raw.githubusercontent.com/platformio/platformio-core/develop/platformio/assets/system/99-platformio-udev.rules | sudo tee /etc/udev/rules.d/99-platformio-udev.rules

# 2. Recarrega o gerenciador de dispositivos udev
sudo udevadm control --reload-rules
sudo udevadm trigger

# 3. Dá permissão ao seu usuário para acessar portas seriais
sudo usermod -a -G dialout $USER
sudo usermod -a -G plugdev $USER
```

> [!IMPORTANT]
> Após executar o `usermod`, é mandatório **fazer Logout e Login** no Linux (ou reiniciar a máquina) para que o grupo `dialout` passe a valer.

> [!TIP]
> **A placa conecta e desconecta imediatamente no Linux (Ubuntu/Debian)?**  
> O serviço de acessibilidade `brltty` (para displays em Braille) costuma sequestrar conversores CH340 achando que são leitores táteis. Remova-o com:  
> `sudo apt remove brltty`

📖 **Para estudar:**
- [PlatformIO — 99-platformio-udev.rules](https://docs.platformio.org/en/latest/core/installation/udev-rules.html)
- [Espressif — Estabelecer Conexão Serial](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/get-started/establish-serial-connection.html)

---

## 3. Identificar a sua placa (escolher o *board*)

O parâmetro **`board`** informa ao compilador e ao gravador as características de hardware (núcleos, velocidade, pinagem, tamanho da memória flash e se há PSRAM).

### Tabela de placas mais comuns

| Placa Física | Identificador (`board`) | Notas |
|--------------|-------------------------|-------|
| **ESP32 DevKit V1 / Dev Module (WROOM-32)** | `esp32dev` | ✅ **Placa padrão do curso** (30 ou 36 pinos) |
| DOIT ESP32 DEVKIT V1 | `esp32doit-devkit-v1` | Idêntico em configuração ao `esp32dev` |
| ESP-WROVER-KIT | `esp-wrover-kit` | Placa oficial com chip WROVER e PSRAM |
| ESP32-CAM (AI-Thinker) | `esp32cam` | Módulo de câmera sem conversor USB embutido |
| ESP32-S3 DevKit | `esp32-s3-devkitc-1` | Arquitetura S3 com USB OTG nativo |
| ESP32-C3 DevKit | `esp32-c3-devkitm-1` | Núcleo único RISC-V |

### Como ter certeza do modelo da sua placa?

1. Observe o encapsulamento metálico: geralmente está gravado **ESP32-WROOM-32** ou **ESP32-WROVER**.
2. Ou conecte a placa e abra no navegador (Chrome/Edge) o utilitário web da Espressif: [esptool-js](https://espressif.github.io/esptool-js/). Clique em **Connect** e selecione a porta serial: ele exibirá com precisão o modelo do chip, frequência e tamanho da Flash.

📖 **Para estudar:**
- [PlatformIO — Registro de Placas Espressif 32](https://docs.platformio.org/en/latest/boards/index.html#espressif-32)
- [Espressif — Guia de Hardware ESP32-DevKitC](https://docs.espressif.com/projects/esp-dev-kits/en/latest/esp32/esp32-devkitc/user_guide.html)

---

## 4. Como abrir e selecionar projetos no VS Code

Muitos erros acontecem porque o usuário abre a pasta errada no VS Code. O PlatformIO **só ativa sua barra de ferramentas e seus botões se a pasta aberta contiver o arquivo `platformio.ini`** na raiz!

### Como abrir um projeto existente

1. No menu superior do VS Code: **File** > **Open Folder...** (ou `Ctrl+K Ctrl+O`).
2. Navegue até a pasta do projeto (por exemplo, a pasta `Exemplo/`) e clique em **Open**.
3. **Aguarde alguns segundos:** o PlatformIO detectará o `platformio.ini` e exibirá a **Barra de Ferramentas Azul** no canto inferior.

> [!CAUTION]
> Se você abrir a pasta mãe de todo o repositório (que contém módulos `.md` e uma subpasta com o projeto), certifique-se de usar um workspace multi-pasta (`OOP.code-workspace`) ou abrir diretamente a pasta onde está o `platformio.ini`. Caso contrário, os ícones de compilar e gravar não aparecerão!

### O Seletor de Ambientes e Projetos (Environment Switcher)

Na barra inferior azul de status do VS Code, você verá um botão escrito algo como:
`env:esp32dev` ou `env:esp-wrover-kit` (ao lado de uma pastinha ou do ícone de formiga).

- **Ao clicar nesse texto na barra inferior:** uma janela no topo do VS Code se abrirá permitindo alternar qual ambiente você quer compilar ou gravar.
- Se o projeto possuir múltiplos ambientes (ex: um para `esp32dev` e outro para `esp32cam`), você alterna entre eles com um simples clique nesse seletor!

---

## 5. Criando um novo projeto no PlatformIO

### Pela Interface Gráfica (GUI)

1. Na barra lateral esquerda do VS Code, clique no ícone do **PlatformIO** (cabeça de formiga 🐜).
2. No menu **Quick Access**, clique em **PIO Home** > **Open**.
3. Na tela de boas-vindas do PlatformIO que se abre, clique no grande botão **"+ New Project"**.
4. Preencha os campos do assistente:
   - **Name:** Nome do projeto (ex.: `MeuRoboESP32` — sem espaços ou acentos).
   - **Board:** Digite `esp32dev` e selecione **"Espressif ESP32 Dev Module"**.
   - **Framework:** Selecione **Arduino**.
   - **Location:** Desmarque *"Use default location"* se quiser salvar em uma pasta específica do seu disco.
5. Clique no botão **Finish**.
6. **Atenção:** Na primeira vez, o PlatformIO baixa os compiladores e bibliotecas. Pode demorar alguns minutos. Não feche o VS Code até terminar!

### Estrutura de Pastas Gerada

```text
MeuProjeto/
├── include/        → Arquivos de cabeçalho .h das suas classes
├── lib/            → Bibliotecas locais do projeto
├── src/
│   └── main.cpp    → Código principal (setup e loop)
├── test/           → Arquivos de testes unitários
└── platformio.ini  → Arquivo de configuração central do projeto
```

> Para aprofundar na organização dos arquivos, leia [organizacao-platformio.md](organizacao-platformio.md) e a [Apresentação](12-Apresentacao.md).

📖 **Para estudar:**
- [PlatformIO — Quick Start no VS Code](https://docs.platformio.org/en/latest/integration/ide/vscode.html#quick-start)
- [Random Nerd Tutorials — Guia VS Code + PlatformIO](https://randomnerdtutorials.com/vs-code-platformio-ide-esp32-esp8266-arduino/)

---

## 6. Configurar o `platformio.ini`

O arquivo `platformio.ini` é o coração do projeto. Abra-o na raiz do seu projeto.

No nosso treinamento utilizamos a plataforma comunitária **pioarduino**, que fornece o núcleo **Arduino-ESP32 3.x** atualizado:

```ini
[env:esp32dev]
; Utiliza a plataforma atualizada compatível com ESP32 Core 3.x
platform = https://github.com/pioarduino/platform-espressif32/releases/download/stable/platform-espressif32.zip
board = esp32dev          ; troque para esp32cam se usar a ESP32-CAM
framework = arduino

; Velocidade de comunicação do Monitor Serial (deve ser idêntica ao Serial.begin() no código)
monitor_speed = 115200

; Configuração de Porta (OPCIONAL):
; Por padrão, o PlatformIO detecta a porta serial automaticamente!
; Descomente e ajuste apenas se tiver múltiplos dispositivos conectados:
; upload_port = /dev/ttyUSB0     ; No Linux
; upload_port = COM3             ; No Windows
```

> [!WARNING]
> **Cuidado com portas fixadas de outras máquinas!**  
> Se no `platformio.ini` houver uma linha como `upload_port = COM9` e você estiver rodando no Linux ou com a ESP32 em outra porta, o upload falhará com erro:  
> `could not open port COM9: [Errno 2] No such file or directory`.  
> Nesse caso, comente a linha colocando um ponto-e-vírgula (`;`) no início para voltar à detecção automática.

📖 **Para estudar:**
- [Documentação oficial do `platformio.ini`](https://docs.platformio.org/en/latest/projectconf/index.html)
- [Opções de upload (`upload_port`, `upload_speed`)](https://docs.platformio.org/en/latest/projectconf/sections/env/options/upload/index.html)

---

## 7. O Terminal do PlatformIO (Por que o terminal comum não roda `pio`?)

Um dos problemas mais comuns de quem está começando:  
Você abre o terminal normal do seu computador (Bash, Zsh ou PowerShell), digita `pio run` e recebe:
```text
bash: pio: command not found
```

### Por que isso acontece?
O PlatformIO instala seu interpretador Python, suas ferramentas de compilação e o comando executável `pio` dentro de uma pasta isolada (*sandbox* / ambiente virtual):
- No Linux/macOS: `~/.platformio/penv/bin/`
- No Windows: `%USERPROFILE%\.platformio\penv\Scripts\`

Por padrão, essa pasta **não está adicionada ao `PATH` global do seu sistema operacional**.

### Como abrir o Terminal correto da extensão PlatformIO

Você deve usar o terminal integrado que o próprio PlatformIO prepara com o ambiente ativado. Há 3 maneiras de abri-lo:

1. **Pela Barra Inferior Azul do VS Code:**  
   Procure na barra inferior pelo ícone de terminal (o símbolo de prompt `>_` ou um quadradinho com terminal). Basta clicar nele!
2. **Pela Barra Lateral do PlatformIO:**  
   Clique no ícone da formiga 🐜 na barra lateral esquerda > vá em **Quick Access** > abra a seção **Miscellaneous** > clique em **New Terminal**.
3. **Pela Paleta de Comandos (`Ctrl+Shift+P`):**  
   Pressione `Ctrl+Shift+P` (ou `Cmd+Shift+P` no Mac), digite `PlatformIO: New Terminal` e aperte **Enter**.

Ao abrir por esses métodos, o terminal exibirá uma mensagem iniciando com `PlatformIO Core CLI...` e qualquer comando `pio` funcionará perfeitamente!

---

## 8. Descobrir e selecionar a porta serial (GUI e Configuração)

### Método 1: Pela Interface Gráfica (GUI)

1. Clique no ícone do PlatformIO (formiga 🐜) na barra lateral esquerda.
2. Em **Quick Access**, clique em **PIO Home** > **Devices**.
3. O PlatformIO listará todas as portas conectadas:
   - Exemplo no Linux: `/dev/ttyUSB0` (chip Silicon Labs CP2102).
   - Exemplo no Windows: `COM3` (chip CH340).
4. Se a porta não aparecer, desconecte e reconecte o cabo USB e clique no botão **Refresh**.

### Método 2: Pelo Terminal do PlatformIO

Abra o Terminal do PlatformIO e execute:
```bash
pio device list
```

A saída exibirá a lista detalhada de portas e o identificador de hardware de cada dispositivo conectado.

---

## 9. Primeiro código: Blink + Serial

No arquivo `src/main.cpp`, insira o código abaixo para testar a placa e a comunicação serial simultaneamente:

```cpp
#include <Arduino.h>

// Pino do LED onboard:
// GPIO 2 na grande maioria das placas ESP32 DevKit
// GPIO 4 caso esteja utilizando o LED Flash da ESP32-CAM
constexpr uint8_t PINO_LED = 2;

void setup() {
    Serial.begin(115200);          // mesma velocidade do monitor_speed
    pinMode(PINO_LED, OUTPUT);
    Serial.println("ESP32 pronta!");
}

void loop() {
    digitalWrite(PINO_LED, HIGH);
    Serial.println("LED ligado");
    delay(500);

    digitalWrite(PINO_LED, LOW);
    Serial.println("LED desligado");
    delay(500);
}
```

---

## 10. Compilar e Gravar (Flashar): Botões da GUI vs. Comandos

### A Barra de Ações Inferior do VS Code

Com um projeto PlatformIO aberto, observe a barra azul no rodapé da janela do VS Code. Da esquerda para a direita você tem os botões principais:

```
[🏠 Home]  [✓ Build]  [→ Upload]  [🗑️ Clean]  [🔌 Monitor]  [>_ Terminal]
```

| Ícone | Ação na GUI | O que faz | Comando no Terminal PIO |
|:-----:|:------------|:----------|:------------------------|
| **🏠** | **PIO Home** | Abre a tela inicial com projetos, bibliotecas e dispositivos | `pio home` |
| **✓** | **Build** | Compila todo o código e verifica erros de sintaxe (sem enviar à placa) | `pio run` |
| **→** | **Upload** | Compila e faz o flash (gravação) do binário na ESP32 conectada via USB | `pio run -t upload` |
| **🗑️** | **Clean** | Apaga os arquivos temporários de compilação da pasta `.pio/build` | `pio run -t clean` |
| **🔌** | **Serial Monitor** | Abre o terminal serial para visualizar saídas de `Serial.println()` | `pio device monitor` |
| **`>_`**| **Terminal** | Abre o terminal com o ambiente do PlatformIO já ativado | *(inicia o terminal PIO)* |

### Executando pela Aba Lateral do PlatformIO (Project Tasks)

Outra forma prática de executar ações:
1. Clique no ícone da formiga 🐜 na barra lateral esquerda.
2. Na árvore de tarefas (**Project Tasks**), expanda o nome do seu ambiente (ex: `esp32dev` ou `esp-wrover-kit`):
   - Clique em **General** > **Build** para compilar.
   - Clique em **General** > **Upload** para gravar.
   - Clique em **General** > **Upload and Monitor** para gravar e abrir o monitor serial automaticamente ao término!

### Executando pelo Terminal do PlatformIO (Linha de Comando)

Se preferir utilizar a linha de comando no Terminal do PlatformIO:

```bash
# Apenas compilar
pio run

# Compilar e gravar na ESP32 (detecção automática da porta)
pio run -t upload

# Gravar forçando uma porta serial específica
pio run -t upload --upload-port /dev/ttyUSB0

# Gravar e em seguida já abrir o Serial Monitor automaticamente
pio run -t upload -t monitor

# Apagar completamente a memória Flash (útil para limpar dados corrompidos)
pio run -t erase
```

---

## 11. Como usar o Serial Monitor (GUI vs. Terminal)

O Serial Monitor permite visualizar o que sua placa está transmitindo via comandos `Serial.print()` e `Serial.println()`.

### Como abrir o Serial Monitor

#### Opção A — Pela Interface Gráfica (Mais rápido)
- Clique no ícone de plugue/tomada (🔌) na barra de status inferior azul do VS Code.
- Ou vá na aba lateral da formiga 🐜 > **Project Tasks** > seu ambiente > clique em **Monitor**.

#### Opção B — Pelo Terminal do PlatformIO
- No Terminal do PlatformIO, execute:
  ```bash
  pio device monitor
  ```
- Para especificar porta e velocidade diretamente:
  ```bash
  pio device monitor -p /dev/ttyUSB0 -b 115200
  ```

### Como selecionar a porta e velocidade do Monitor
- **Velocidade (Baud Rate):** Sempre defina `monitor_speed = 115200` no `platformio.ini`. Se a velocidade for diferente da configurada no `Serial.begin()`, aparecerão caracteres ilegíveis ou símbolos de interrogação (`⸮⸮⸮`).
- **Porta:** Se tiver múltiplas placas conectadas, você pode fixar a porta no `platformio.ini` com:
  ```ini
  monitor_port = /dev/ttyUSB0
  ```

### ⚠️ Como encerrar o Serial Monitor (Crucial!)

Uma porta serial **só pode ser acessada por um único programa por vez**.  
Se o Serial Monitor estiver aberto e você tentar clicar em **Upload (→)**, a gravação pode falhar com o erro:  
`A fatal error occurred: Could not open port, the port is busy or access is denied.`

**Como fechar:**
1. Clique dentro da janela do terminal onde o monitor está rodando e pressione o atalho **`Ctrl + C`**.
2. Ou clique no ícone da **Lixeirinha (Kill Terminal 🗑️)** no canto superior direito do painel de terminais do VS Code para matar a sessão.

---

## 12. Modo de gravação manual (Botões BOOT / EN)

A maioria dos módulos ESP32 modernos possui um circuito com dois transistores que coloca o chip em modo de bootloader automaticamente. No entanto, em diversas placas genéricas esse circuito falha e o terminal fica travado na seguinte mensagem:

```text
Connecting........_____....._____....._____.....
A fatal error occurred: Failed to connect to ESP32: Timed out waiting for packet header
```

### O procedimento manual de gravação:

1. Clique no botão de **Upload (→)** no VS Code.
2. Observe o terminal. No instante exato em que a palavra `Connecting...` aparecer:
   - **Pressione e segure o botão físico marcado como `BOOT`** (ou `IO0`) na sua placa ESP32.
3. Assim que a porcentagem de escrita começar a subir (`Writing at 0x00010000... [10%]`), **solte o botão BOOT**.
4. Aguarde a mensagem verde `SUCCESS`.
5. Se o programa não iniciar sozinho após o envio, pressione rapidamente uma vez o botão **`EN`** (ou `RST`) para resetar e iniciar a execução.

> **Explicação Técnica:** O chip ESP32 entra no modo de recepção de firmware (gravação serial) somente se o pino `GPIO0` estiver aterrado (nível lógico LOW) no momento da reinicialização. O botão físico BOOT conecta manualmente o `GPIO0` ao pino `GND`.

---

## 13. Caso especial: ESP32-CAM

A placa **ESP32-CAM (AI-Thinker)** não possui circuito USB integrado. Para gravá-la, utilize um módulo adaptador USB-Serial FTDI (configurado para nível lógico de 3.3V ou 5V):

### Pinagem de Ligação com Adaptador FTDI:

| Pino do FTDI | Pino na ESP32-CAM | Função |
|:------------:|:-----------------:|:-------|
| **VCC (5V)** | **5V** | Alimentação do módulo |
| **GND** | **GND** | Referência comum de terra |
| **TX** | **U0R (GPIO 3)** | Recepção serial de dados |
| **RX** | **U0T (GPIO 1)** | Transmissão serial de dados |
| *(Jumper)* | **GPIO 0 ligado ao GND** | **Obrigatório para entrar em modo Flash!** |

### Passo a passo para gravação da ESP32-CAM:
1. No `platformio.ini`, configure `board = esp32cam`.
2. Conecte um fio jumper entre os pinos **GPIO0** e **GND** da ESP32-CAM.
3. Conecte o FTDI no computador e pressione o botão **RST** da placa.
4. Clique em **Upload (→)** no VS Code.
5. Após o término com sucesso, **remova o jumper entre GPIO0 e GND**.
6. Pressione o botão **RST** da placa novamente para iniciar a execução do código da câmera.

---

## 14. Tabela de Solução de Problemas Comuns

| Erro / Mensagem no Terminal | Causa Raiz | O que fazer para resolver |
|-----------------------------|------------|---------------------------|
| `pio: command not found` | Tentou rodar em terminal normal do SO | Abra o terminal específico do PlatformIO ([seção 7](#7-o-terminal-do-platformio-por-que-o-terminal-comum-não-roda-pio)) |
| `Could not open COM9: [Errno 2] No such file or directory` | Arquivo `platformio.ini` veio com porta fixa do Windows | Comente a linha `upload_port = COM9` colocando `;` na frente ([seção 6](#6-configurar-o-platformioini)) |
| `Could not open /dev/ttyS2: Input/output error` | PlatformIO tentou conectar em porta serial interna da placa-mãe | Descomente e defina a porta correta: `upload_port = /dev/ttyUSB0` |
| `Permission denied: '/dev/ttyUSB0'` | Falta de permissão de usuário no Linux | Execute `sudo usermod -a -G dialout $USER` e faça logout e login |
| `Timed out waiting for packet header` / travado em `Connecting...` | Placa não entrou em modo bootloader | Pressione e segure o botão físico **BOOT** durante o upload ([seção 12](#12-modo-de-gravação-manual-botões-boot--en)) |
| `Port is busy or Access Denied` | Serial Monitor ou outro programa está usando a porta | Feche o monitor com `Ctrl+C` ou na lixeirinha 🗑️ do terminal antes de gravar ([seção 11](#11-como-usar-o-serial-monitor-gui-vs-terminal)) |
| Caracteres estranhos ou interrogações (`⸮⸮⸮`) no Monitor | Baud rate configurado incorretamente | Ajuste `monitor_speed = 115200` no `platformio.ini` para bater com o código |
| `Brownout detector was triggered` | Queda de tensão na alimentação da ESP32 | Troque de porta USB, evite hubs sem fonte ou alimente a placa externamente |

---

➡️ **Próximo passo na sua trilha:** Com a ESP32 gravada e piscando o LED, avance para o [Módulo 1 — Classes, Objetos e Encapsulamento](13-mod1.md).
