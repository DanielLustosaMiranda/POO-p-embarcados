# Como organizar o código bem no PlatformIO?

Ao criar projetos no PlatformIO, organizar os arquivos é essencial para manter o código limpo, legível e fácil de gerenciar, especialmente quando o projeto cresce em complexidade. O PlatformIO utiliza uma estrutura de diretórios padrão que facilita essa organização. Abaixo está a estrutura recomendada e uma explicação de cada componente.

## Estrutura de Diretórios Padrão

A estrutura recomendada pelo PlatformIO é a seguinte:

```text
MeuProjeto/
├── include/
│   └── MeuProjeto.h
├── src/
│   ├── main.cpp
│   ├── MeuModulo.cpp
│   └── MeuModulo.h
├── lib/
│   └── MinhaBiblioteca/
│       ├── library.json
│       └── src/
│           └── MinhaBiblioteca.cpp
├── test/
│   ├── test_case.cpp
│   └── test_main.cpp
├── platformio.ini
└── README.md
```

---

## 1. `platformio.ini`

Este é o arquivo de configuração central do seu projeto PlatformIO. Ele define:
- O ambiente de desenvolvimento (`[env:...]`) - qual placa, framework e ferramentas usar.
- As dependências do projeto (bibliotecas).
- Configurações específicas do build (flags do compilador, upload, etc.).
- Configurações de testes e monitoramento.

**Exemplo:**
```ini
[env:esp32dev]
platform = espressif32
bord = esp32dev
framework = arduino
lib_deps = 
    FastLED@^3.5.0
    PubSubClient@^2.9.0
```

---

## 2. `include/` (Cabeçalhos)

Dedicado para arquivos de cabeçalho (`.h`).
- **Propósito**: Armazenar declarações de funções, classes e protótipos que precisam ser compartilhadas entre diferentes arquivos `.cpp`.
- **Melhores Práticas**:
  - Arquivos aqui devem ter a extensão `.h` ou `.hpp`.
  - É uma boa prática criar um arquivo de cabeçalho por classe ou módulo.
  - Use "include guards" ( `#ifndef`/`#define`/`#endif`) para evitar erros de compilação.

**Exemplo:**
```cpp
// include/MeuModulo.h
#ifndef MEU_MODULO_H
#define MEU_MODULO_H

class MeuModulo {
public:
    MeuModulo();
    void fazerAlgo();
};

#endif
```

---

## 3. `src/` (Código Fonte)

Dedicado para arquivos de código fonte (`.cpp`).
- **Propósito**: Armazenar a implementação das funções e classes declaradas nos arquivos de cabeçalho.
- **Melhores Práticas**:
  - O ponto de entrada principal do seu programa deve ser `src/main.cpp`.
  - Divida o código em módulos lógicos (ex: `sensores.cpp`, `comunicacao.cpp`).
  - Inclua os arquivos de cabeçalho necessários (`#include "MeuModulo.h"`).

**Exemplo:**
```cpp
// src/MeuModulo.cpp
#include "../include/MeuModulo.h"
#include <Arduino.h>

MeuModulo::MeuModulo() {
    Serial.begin(115200);
}

void MeuModulo::fazerAlgo() {
    Serial.println("Fazendo algo...");
}
```

---

## 4. `lib/` (Bibliotecas)

Dedicado para bibliotecas externas ou do próprio projeto.
- **Propósito**: Gerenciar dependências do projeto.
- **Tipos de Bibliotecas**:
  1. **Bibliotecas do PlatformIO Registry**: Adicionadas via `platformio.ini` (ex: `FastLED`). O PlatformIO baixa e gerencia automaticamente.
  2. **Bibliotecas Locais**: Arquivos que você baixa manualmente. Devem ter uma estrutura mínima (`library.json` ou `.h`/`.cpp`).
  3. **Bibliotecas do Projeto**: Módulos do próprio projeto que você quer reutilizar em outros projetos.

**Estrutura de uma Biblioteca Local:**
```text
lib/MinhaBiblioteca/
├── src/        # Código fonte da biblioteca
├── include/    # Cabeçalhos da biblioteca (opcional)
├── library.json  # Arquivo de configuração da biblioteca
└── README.md   # Documentação
```

---

## 5. `test/` (Testes)

Dedicado para testes unitários e de integração.
- **Propósito**: Garantir a qualidade e correto funcionamento do código.
- **Estrutura**:
  - `test_main.cpp`: O ponto de entrada dos testes.
  - `test_case.cpp`: Arquivos contendo os casos de teste.
- **Frameworks Comuns**: O PlatformIO suporta frameworks como **Unity** e **CppUTest**, configuráveis no `platformio.ini`.

**Exemplo:**
```cpp
// test/test_main.cpp
#include <Arduino.h>
#include <unity.h>

void setup() {
    Serial.begin(115200);
    delay(2000);
    
    UNITY_BEGIN();
    
    // Executar testes
    RUN_TEST(test_minha_funcao);
    
    UNITY_END();
}

void loop() {
    // Não fazer nada
}
```

---

## 6. `README.md`

Arquivo de documentação para o projeto.
- **Propósito**: Descrever o que o projeto faz, como instalá-lo, configurá-lo e usá-lo.
- **Conteúdo Recomendado**:
  - Descrição do projeto.
  - Hardware necessário.
  - Instruções de instalação.
  - Exemplo de uso.
  - Como rodar testes.
  - Licença.

---

## 7. Outros Diretórios Úteis

O PlatformIO reconhece automaticamente outros diretórios para propósitos específicos:

- **`examples/`**: Contém exemplos de uso do projeto ou das bibliotecas.
- **`scripts/`**: Scripts úteis para o desenvolvimento (ex: scripts de automação).
- **`data/`**: Arquivos que serão armazenados no sistema de arquivos do ESP32 (SPIFFS ou LittleFS).
- **`docs/`**: Documentação detalhada do projeto (opcional).
- **`build_output/`**: Onde o PlatformIO coloca os arquivos gerados durante o build (geralmente não precisa ser editado manualmente).

---

## Melhores Práticas de Organização

### 1. Modularização
Divida o código em módulos lógicos, cada um com sua própria responsabilidade. Por exemplo:

```text
src/sensores.cpp
src/sensores.h
src/display.cpp
src/display.h
```

### 2. Constantes e Configurações
Centralize constantes e configurações em um único arquivo:

```text
include/config.h
```

### 3. Proteção de Inclusão (Include Guards)
Sempre use include guards nos arquivos de cabeçalho:

```cpp
#ifndef MEU_MODULO_H
#define MEU_MODULO_H
// ... conteúdo do cabeçalho
#endif
```

### 4. Controle de Versão
Use Git para versionar seu projeto. O PlatformIO se integra bem com o Git, e você pode gerenciar suas bibliotecas como submódulos Git se desejar.

### 5. Documentação
Mantenha a documentação atualizada, especialmente no `README.md` e nos comentários dos arquivos de cabeçalho.

### 6. Ambiente Consistente
Use o arquivo `platformio.ini` para definir todas as configurações do ambiente, garantindo que qualquer pessoa que use seu projeto tenha a mesma configuração de compilação.