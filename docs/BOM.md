# BOM — Pseudo Metal Zone V1

Arquivo consolidado para compra e conferência de componentes da PCB V1.

> **Objetivo**: evitar compra de componente com footprint/tamanho errado.  
> **Placa**: THT (through-hole) com potenciômetros, jacks e chave ligados por headers/fios.  
> **Referência completa**: ver [README.md](../README.md) para especificações e valores.

---

## Busca rápida por bloco funcional

Para encontrar componentes de uma seção específica do circuito, consulte:

| Bloco | Componentes | Seção do BOM |
|---|---|---|
| Alimentação e VREF | U1, C_POWER_FILTER, C_VREF_FILTER, R_VREF_TOP/BOTTOM, C_OPAMP_DECOUPLING | Resistores, Capacitores eletrolíticos |
| Entrada | C_INPUT_COUPLING, R_INPUT_BIAS | Capacitores filme, Resistores |
| Estágio A | R_STAGE1_GAIN, R_STAGE1_REF | Resistores |
| Interstage | C_INTERSTAGE, R_STAGE2_BIAS | Capacitores filme, Resistores |
| Estágio B (ganho) | P_GAIN, R_LIMIT_GAIN, R_STAGE2_REF | Potenciômetros, Resistores |
| Clipping | C_CLIP_IN, D_CLIP_A/B/C | Capacitores filme, Diodos |
| Anti-fizz | R_FIZZ, C_FIZZ | Resistores, Capacitores filme |
| Volume e saída | P_VOLUME, C_OUTPUT_COUPLING | Potenciômetros, Capacitores eletrolíticos |
| Indicação | LED_POWER, R_LED | LED, Resistores |

---

## Resumo crítico de tamanhos

| Categoria | Footprint na PCB | Comprar como |
|---|---|---|
| Resistores | `R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm_Horizontal` | Resistor axial THT 1/4W, corpo ~6.3 mm, passo 7.62 mm |
| Diodos | `D_DO-35_SOD27_P7.62mm_Horizontal` | 1N4148 axial THT DO-35, vidro, passo 7.62 mm |
| CI | `DIP-8_W7.62mm` | RC4558 / JRC4558 DIP-8 + soquete DIP-8 |
| Capacitores de voicing | `C_Rect_L7.2mm_W2.5mm_P5.00mm` | Filme/poliéster box THT, passo 5 mm, 50V/63V/100V |
| Desacoplamento opamp | `C_Disc_D5.0mm_W2.5mm_P2.50mm` | Cerâmico disco ou MLCC radial 100nF, passo 2.5 mm |
| Eletrolíticos | `CP_Radial_D5.0mm_P2.00mm` | Eletrolítico radial THT, diâmetro 5 mm, passo 2 mm |
| LED | `LED_D5.0mm` | LED 5 mm THT |
| Pots externos | `PinHeader_1x03_P2.54mm` | Potenciômetros de painel ligados por fio |
| Jacks/chave externos | `PinHeader_1x02_P2.54mm` | Jacks/chave ligados por fio |
| Pads/testes/jumpers | `PinHeader_1x01_P2.54mm` | Header opcional ou fio/perninha de resistor |

---

## Lista de compra recomendada

### CI e soquete

| Item | Valor/modelo | Qtde | Comprar como | Observações |
|---|---:|---:|---|---|
| U1 | RC4558 / JRC4558 | 1 | CI DIP-8 THT | Evitar SMD/SOIC |
| Soquete U1 | DIP-8 | 1 | Soquete DIP-8, comum ou torneado | Recomendado para troca/teste |

### Resistores

Comprar todos como **resistores axiais THT 1/4W**, preferencialmente metal film 1%. Carbono 5% também funciona.

| Referência | Valor | Qtde | Footprint | Comprar como | Observações |
|---|---:|---:|---|---|---|
| R_INPUT_BIAS1 | 1M | 1 | DIN0207 P7.62 | 1M 1/4W axial | Bias da entrada para VREF |
| R_STAGE1_GAIN1, R_STAGE2_BIAS1, R_VREF_BOTTOM1, R_VREF_TOP1 | 100k | 4 | DIN0207 P7.62 | 100k 1/4W axial | Feedback estágio A, bias estágio B e divisor VREF |
| R_LIMIT_GAIN1 | 47k | 1 | DIN0207 P7.62 | 47k 1/4W axial | Em paralelo ao gain; evita colapso/saturação excessiva |
| R_STAGE2_REF1 | 22k | 1 | DIN0207 P7.62 | 22k 1/4W axial | Referência do estágio B |
| R_FIZZ1 | 10k | 1 | DIN0207 P7.62 | 10k 1/4W axial | Anti-fizz |
| R_STAGE1_REF1 | 4.7k | 1 | DIN0207 P7.62 | 4k7 1/4W axial | Define ganho do estágio A com 100k |
| R_LED1 | 2k2 | 1 | DIN0207 P7.62 | 2k2 1/4W axial | Limitador do LED |

**Total de resistores:** 10 unidades.

### Diodos

| Referência | Valor/modelo | Qtde | Footprint | Comprar como | Observações |
|---|---:|---:|---|---|---|
| D_CLIP_A1, D_CLIP_B1, D_CLIP_C1 | 1N4148 | 3 | DO-35 P7.62 | 1N4148 axial THT de vidro | Clipping assimétrico; não comprar SMD |

### Capacitores de filme / poliéster / box

Comprar como **capacitor filme/poliéster box THT com passo 5 mm**. Tensão de 50V, 63V ou 100V é suficiente para pedal de 9V.

**Nota sobre códigos comuns**: o código de três dígitos indica o valor em pF (exemplo: 104 = 100nF, 103 = 10nF). Este é o código impresso no capacitor.

| Referência | Valor | Código comum | Qtde | Footprint | Comprar como | Observações |
|---|---:|---|---:|---|---|---|
| C_INPUT_COUPLING1 | 100nF | 104 | 1 | Rect L7.2 W2.5 P5.00 | Filme box 100nF P5mm | Acoplamento de entrada |
| C_INTERSTAGE1 | 10nF | 103 | 1 | Rect L7.2 W2.5 P5.00 | Filme box 10nF P5mm | Filtro entre estágios; valor escolhido para timbre mais definido |
| C_CLIP_IN1 | 220nF | 224 / u22 | 1 | Rect L7.2 W2.5 P5.00 | Filme box 220nF P5mm | Pré-clipping; mais corpo/graves que 100nF |
| C_FIZZ1 | 3.3nF | 3n3 / 332 | 1 | Rect L7.2 W2.5 P5.00 | Filme box 3.3nF P5mm | Anti-fizz |

**Atenção:** evitar capacitores grandes de 250V com passo 10 mm, 15 mm ou 22.5 mm. Eles podem até funcionar eletricamente, mas provavelmente não encaixam bem na PCB.

### Capacitor de desacoplamento do opamp

| Referência | Valor | Código comum | Qtde | Footprint | Comprar como | Observações |
|---|---:|---|---:|---|---|---|
| C_OPAMP_DECOUPLING1 | 100nF | 104 | 1 | Disc D5 W2.5 P2.50 | Cerâmico disco 100nF P2.5mm | Deve ficar próximo do RC4558, entre pino 8 e pino 4 |

### Capacitores eletrolíticos

Comprar como **eletrolítico radial THT, diâmetro 5 mm, passo 2 mm**.

| Referência | Valor | Qtde | Footprint | Comprar como | Observações |
|---|---:|---:|---|---|---|
| C_OUTPUT_COUPLING1 | 1uF | 1 | CP Radial D5 P2.00 | 1uF 16V ou 25V radial D5/P2 | Saída; bloqueia DC residual |
| C_POWER_FILTER1, C_VREF_FILTER1 | 100uF | 2 | CP Radial D5 P2.00 | 100uF 16V radial D5/P2 | Filtro alimentação e VREF |

**Atenção:** 100uF 25V pode servir se o corpo for D5mm. 100uF 50V geralmente é maior e deve ser evitado. Procurar por `100uF 16V 5x11mm radial P2mm`.

### LED

| Referência | Valor/modelo | Qtde | Footprint | Comprar como | Observações |
|---|---:|---:|---|---|---|
| LED_POWER1 | LED | 1 | LED_D5.0mm | LED 5mm THT | Cor livre; respeitar polaridade |

### Potenciômetros externos

Na PCB, os potenciômetros são conectados por **headers 1x03 P2.54mm**. Eles não são soldados diretamente como potenciômetros PCB-mount.

| Referência | Valor | Qtde | Footprint na PCB | Comprar como | Recomendação |
|---|---:|---:|---|---|---|
| P_GAIN1 | 100k | 1 | Header 1x03 P2.54 | Potenciômetro de painel B100k linear | Ligado por fios; usado como reostato |
| P_VOLUME1 | 100k | 1 | Header 1x03 P2.54 | Potenciômetro de painel A100k log/audio | B100k funciona, mas A100k é mais natural para volume |

### Headers, pads, jacks e chave

A PCB usa headers de 2.54 mm para conexões externas, pads de teste e jumpers.

| Referência | Qtde no BOM | Footprint | Comprar como | Observações |
|---|---:|---|---|---|
| Pads 1x01 diversos | 13 | PinHeader 1x01 P2.54 | Barra de pinos macho 2.54mm ou deixar sem montar | Muitos são pontos de teste/modificação opcionais |
| DC_IN1 | 1 | PinHeader 1x02 P2.54 | Header 1x02 ou fio direto | Entrada/chave de alimentação conforme montagem |
| INPUT1, OUTPUT1, POWER_SW1 | 3 | PinHeader 1x02 P2.54 | Header 1x02 ou fio direto | Jacks/chave externos ligados por fio |
| P_GAIN1, P_VOLUME1 | 2 | PinHeader 1x03 P2.54 | Header 1x03 | Para ligação dos potenciômetros |
| Jumpers | conforme PCB | Pads/headers | Fio rígido ou perninha de resistor | Não precisa comprar componente especial |

---

## Componentes opcionais/extras recomendados para voicing

Esses valores ajudam nos testes futuros, caso a V1 precise de ajuste após testar com guitarra real.

| Ponto | Valor atual | Extras úteis | Efeito esperado |
|---|---:|---|---|
| C_INTERSTAGE | 10nF | 15nF, 100nF | 10nF = mais tight/definido; 100nF = mais grave/aberto |
| C_CLIP_IN | 220nF | 100nF | 220nF = mais corpo/graves; 100nF = mais tight |
| R_LIMIT_GAIN | 47k | 22k, 33k, 68k | Ajusta limite do ganho máximo |
| C_FIZZ | 3.3nF | 2.2nF, 4.7nF, 6.8nF | Ajusta corte de agudos/fizz |

---

## O que evitar comprar errado

- RC4558 / 4558 em SMD/SOIC.
- 1N4148 SMD.
- Resistores 1/2W grandes, se quiser montagem limpa.
- Capacitor 220nF 250V grande com passo 15mm.
- Capacitor 100nF/10nF/3n3 com passo 10mm ou 15mm para os pontos de voicing.
- Eletrolítico 100uF 50V grande.
- Potenciômetro PCB-mount esperando encaixar direto na placa.

---

## BOM original agrupado por referência

| Reference | Qty | Value | Footprint | Nota de compra |
|---|---:|---|---|---|
| 9V_PAD1, CLIP_NODE_PAD1, GND_PAD1, GND_PAD2, JMP_9V_A1, JMP_9V_B1, JMP_INTER_A1, JMP_INTER_B1, OUTPUT_PAD1, STAGE1_OUT_PAD1, STAGE2_IN_PAD1, VREF_PAD1, VREF_PAD2 | 13 | Conn_01x01 | PinHeader_1x01_P2.54mm_Vertical | Opcional; pads de teste/jumpers/mods |
| C_CLIP_IN1 | 1 | 220nF | C_Rect_L7.2mm_W2.5mm_P5.00mm | Filme box P5mm, evitar 250V grande |
| C_FIZZ1 | 1 | 3n3 | C_Rect_L7.2mm_W2.5mm_P5.00mm | Filme box P5mm |
| C_INPUT_COUPLING1 | 1 | 100nF | C_Rect_L7.2mm_W2.5mm_P5.00mm | Filme box P5mm |
| C_INTERSTAGE1 | 1 | 10nF | C_Rect_L7.2mm_W2.5mm_P5.00mm | Filme box P5mm |
| C_OPAMP_DECOUPLING1 | 1 | 100nF | C_Disc_D5.0mm_W2.5mm_P2.50mm | Cerâmico P2.5mm |
| C_OUTPUT_COUPLING1 | 1 | 1uF | CP_Radial_D5.0mm_P2.00mm | Eletrolítico radial D5/P2 |
| C_POWER_FILTER1, C_VREF_FILTER1 | 2 | 100uF | CP_Radial_D5.0mm_P2.00mm | Eletrolítico radial D5/P2, preferir 16V 5x11mm |
| DC_IN1 | 1 | POWER_SW | PinHeader_1x02_P2.54mm_Vertical | Header/fio para alimentação |
| D_CLIP_A1, D_CLIP_B1, D_CLIP_C1 | 3 | 1N4148 | D_DO-35_SOD27_P7.62mm_Horizontal | 1N4148 axial THT vidro |
| INPUT1, OUTPUT1, POWER_SW1 | 3 | — | PinHeader_1x02_P2.54mm_Vertical | Jacks/chave por fio |
| LED_POWER1 | 1 | LED | LED_D5.0mm | LED 5mm THT |
| P_GAIN1, P_VOLUME1 | 2 | 100k | PinHeader_1x03_P2.54mm_Vertical | Pots externos por fio; B100k gain, A100k volume |
| R_FIZZ1 | 1 | 10k | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_INPUT_BIAS1 | 1 | 1M | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_LED1 | 1 | 2k2 | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_LIMIT_GAIN1 | 1 | 47k | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_STAGE1_GAIN1, R_STAGE2_BIAS1, R_VREF_BOTTOM1, R_VREF_TOP1 | 4 | 100k | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_STAGE1_REF1 | 1 | 4.7k | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| R_STAGE2_REF1 | 1 | 22k | R_Axial_DIN0207_L6.3mm_D2.5mm_P7.62mm | 1/4W axial |
| U1 | 1 | RC4558 | DIP-8_W7.62mm | CI DIP-8 + soquete recomendado |
