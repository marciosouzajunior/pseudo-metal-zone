# Pseudo Metal Zone

Pedal de distorção/fuzz pesado baseado em **RC4558/JRC4558**, desenvolvido em protoboard e depois convertido para **PCB V1 em KiCad**.

A ideia é chegar em uma distorção agressiva para baixo/guitarra, inspirada de forma conceitual no Boss Metal Zone e em timbres de hard rock/nu metal, mas com um circuito simples, compreendido estágio por estágio, usando componentes THT e um único CI dual op-amp.

## Objetivo sonoro

- Distorção pesada para baixo/guitarra.
- Mais definida que fuzz puro.
- Grave presente, mas sem embolar demais.
- Agudos controlados, reduzindo fizz/chiado.
- Funcionamento com alimentação simples de 9 V.
- Projeto didático, fácil de modificar e medir.

## Arquitetura do circuito

Fluxo geral do sinal:

```text
INPUT
→ C_INPUT_COUPLING
→ GAIN STAGE A
→ C_INTERSTAGE
→ GAIN STAGE B
→ C_CLIP_IN
→ ASYMMETRICAL CLIPPING
→ ANTI-FIZZ
→ MASTER VOLUME
→ C_OUTPUT_COUPLING
→ OUTPUT
```

## Alimentação e referência

### Alimentação

```text
Jack DC 9 V → chave SPST → +9V
```

- Fonte: 9 V DC.
- CI: RC4558/JRC4558.
- Pino 8 do 4558: +9 V.
- Pino 4 do 4558: GND.
- O circuito usa alimentação simples, então os estágios de áudio trabalham referenciados em VREF.

### Filtro principal

```text
+9V → C_POWER_FILTER 100uF → GND
```

Função: reduzir ruído/ripple da alimentação e estabilizar a linha de 9 V.

### VREF

```text
+9V
→ R_VREF_TOP 100k
→ VREF
→ R_VREF_BOTTOM 100k
→ GND
```

```text
VREF → C_VREF_FILTER 100uF → GND
```

- VREF esperado: aproximadamente 4,5 V.
- Inicialmente foi usado 1000 uF no VREF; funcionou, mas demorava mais para estabilizar e o áudio demorava a aparecer ao ligar.
- Valor atual escolhido: **100 uF**.

### Desacoplamento do op-amp

```text
pino 8 → C_OPAMP_DECOUPLING 100nF (104) → pino 4
```

Deve ficar fisicamente o mais próximo possível do RC4558.

## Entrada

```text
INPUT → C_INPUT_COUPLING 100nF (104) → pino 3
pino 3 → R_INPUT_BIAS 1M → VREF
```

Funções:

- `C_INPUT_COUPLING`: bloqueia DC e deixa o áudio passar.
- `R_INPUT_BIAS`: polariza a entrada em VREF e mantém alta impedância.

## Estágio A

Primeiro estágio de ganho, usando a primeira metade do 4558.

```text
pino 1 → R_STAGE1_GAIN 100k → pino 2
pino 2 → R_STAGE1_REF 4.7k → VREF
```

Onde:

- `R_STAGE1_GAIN` (100k): resistor de realimentação (feedback).
- `R_STAGE1_REF` (4.7k): resistor de referência que define o ganho junto com R_STAGE1_GAIN.

Ganho aproximado:

```text
R_STAGE1_GAIN / R_STAGE1_REF = 100k / 4.7k ≈ 22x
```

Função:

- Pré-amplificar o sinal.
- Preparar o sinal para o segundo estágio.
- Gerar drive inicial sem deixar o circuito totalmente fora de controle.

## Filtro entre estágios

```text
pino 1 → C_INTERSTAGE → pino 5
pino 5 → R_STAGE2_BIAS 100k → VREF
```

Valor escolhido:

```text
C_INTERSTAGE = 10nF (103)
```

Essa foi uma das mudanças mais importantes do projeto. O 103 cortou parte dos graves antes do segundo estágio, deixando o som mais definido e agressivo. O 104 ficou mais cheio/aberto, mas menos controlado. O 153 ficou no meio do caminho.

## Estágio B

Estágio principal de ganho, usando a segunda metade do 4558.

```text
pino 7 ↔ P_GAIN 100k ↔ pino 6
pino 6 → R_STAGE2_REF 22k → VREF
R_LIMIT_GAIN 47k em paralelo com P_GAIN
```

Ligação robusta sugerida do pot como reostato:

```text
terminal 2 e 3 unidos
terminal 1 no pino 6
```

O resistor `R_LIMIT_GAIN` em paralelo com o potenciômetro limita o ganho máximo e evita que a onda colapse ou fique irregular demais. Foram testados 22k e 47k; ambos funcionaram, com preferência atual por **47k**.

## Pré-clipping

```text
pino 7 → C_CLIP_IN → CLIP_NODE
```

Valor atual:

```text
C_CLIP_IN = 220nF (224 ou u22)
```

Função:

- Moldar o corpo do sinal antes dos diodos.
- Controlar quanto grave chega ao clipping.

Testes mostraram que 224 trouxe mais corpo e graves do que 104, mantendo uma resposta ainda utilizável.

## Clipping assimétrico

Configuração atual:

```text
CLIP_NODE
├─ 1 diodo 1N4148 para GND em um sentido
└─ 2 diodos 1N4148 em série para GND no sentido oposto
```

Função:

- Gerar a distorção principal.
- Produzir clipping assimétrico.
- Criar harmônicos mais ricos que a clipagem perfeitamente simétrica.

Comparações feitas:

- Sem diodos: o op-amp clipa por saturação em ganho alto, mas o resultado depende muito do nível de entrada.
- 2 diodos antiparalelos: clipping simétrico, visível e funcional.
- 3 diodos assimétricos: resultado favorito, mais interessante e menos “reto”.

## Anti-fizz

```text
CLIP_NODE → R_FIZZ 10k → OUTPUT_FILTER
OUTPUT_FILTER → C_FIZZ 3n3 → GND
```

Função:

- Reduzir agudos ásperos depois do clipping.
- Controlar fizz/chiado.
- Suavizar frequências altas sem matar totalmente a definição.

Configuração atual:

```text
R_FIZZ = 10k
C_FIZZ = 3n3
```

## Volume master e saída

```text
OUTPUT_FILTER → P_VOLUME 100k → C_OUTPUT_COUPLING 1uF → OUTPUT
```

Ligação típica do volume:

```text
terminal 1 = sinal
terminal 3 = GND
terminal 2 = saída
```

`C_OUTPUT_COUPLING = 1uF` foi mantido na V1 para bloquear DC residual antes do jack de saída.

## Indicação e proteção

### LED de indicação de energia

```text
+9V → R_LED 2.2k → LED_POWER → GND
```

Função:

- Indicar que o pedal está ligado.
- Proteger o LED com resistor limitador de corrente.

Valor do resistor:

- `R_LED = 2.2k` para LED típico de 5 mm com corrente limitada.
- LED escolhido: 5 mm THT, cor livre (vermelho recomendado).

## Configuração atual da PCB V1

| Bloco | Referência | Valor atual |
|---|---|---|
| **Fonte e referência** | | |
| CI principal | U1 | RC4558/JRC4558 DIP-8 + soquete |
| Alimentação | — | 9 V DC |
| Filtro de alimentação | C_POWER_FILTER | 100uF eletrolítico |
| VREF divisor | R_VREF_TOP / R_VREF_BOTTOM | 100k / 100k |
| Filtro VREF | C_VREF_FILTER | 100uF eletrolítico |
| Desacoplamento CI | C_OPAMP_DECOUPLING | 100nF (104) cerâmico |
| **Entrada** | | |
| Acoplamento entrada | C_INPUT_COUPLING | 100nF (104) filme P5mm |
| Bias entrada | R_INPUT_BIAS | 1M |
| **Estágio A** | | |
| Realimentação | R_STAGE1_GAIN | 100k |
| Referência | R_STAGE1_REF | 4.7k |
| **Interstage** | | |
| Filtro interstage | C_INTERSTAGE | 10nF (103) filme P5mm |
| Bias estágio B | R_STAGE2_BIAS | 100k |
| **Estágio B** | | |
| Ganho | P_GAIN | 100k potenciômetro linear |
| Limitador ganho | R_LIMIT_GAIN | 47k em paralelo com P_GAIN |
| Referência | R_STAGE2_REF | 22k |
| **Clipping** | | |
| Pré-clipping | C_CLIP_IN | 220nF (224) filme P5mm |
| Diodos clipping | D_CLIP_A, D_CLIP_B, D_CLIP_C | 3x 1N4148 assimétricos |
| **Anti-fizz** | | |
| Resistor anti-fizz | R_FIZZ | 10k |
| Capacitor anti-fizz | C_FIZZ | 3.3nF (3n3) filme P5mm |
| **Volume e saída** | | |
| Volume master | P_VOLUME | 100k potenciômetro log |
| Acoplamento saída | C_OUTPUT_COUPLING | 1uF eletrolítico |
| **Indicação** | | |
| LED indicador | LED_POWER | 5mm THT |
| Resistor LED | R_LED | 2.2k |

## Conectividade externa

A PCB V1 usa **headers e fios** para conexão de componentes externos:

- **DC_IN**: entrada de alimentação (9V DC) com chave.
- **INPUT**: jack de entrada de áudio.
- **OUTPUT**: jack de saída de áudio.
- **P_GAIN**: potenciômetro de ganho do estágio B (100k linear).
- **P_VOLUME**: potenciômetro de volume master (100k logarítmico).
- **Pads opcionais**: diversos pads de teste e pontos de modificação (13 pads 1x01).

Vide `docs/BOM.md` para detalhes de footprints e conexões.

## Principais descobertas

- Dois estágios funcionaram melhor que um único estágio.
- Ganho excessivo gera fuzz, compressão e perda de definição.
- O filtro entre estágios foi a alteração mais importante para definição.
- O anti-fizz reduziu significativamente o chiado/fizz dos agudos.
- Três diodos assimétricos soaram melhor que dois diodos simétricos.
- O sweet spot do ganho ficou baixo, aproximadamente entre 20% e 35% do pot em alguns testes.
- Abrir demais o ganho não significa necessariamente som melhor; em alguns pontos a onda fica irregular e a medição cai por compressão/limitação.
- O celular em 50% gera sinal mais forte e mais previsível que o baixo, então é bom para comparar medições, mas o instrumento real continua sendo a referência sonora final.
- O RC4558 sozinho consegue clipar por saturação quando há ganho suficiente.
- O TL072 foi testado, mas não trouxe vantagem imediata nesta fase.

## Resumo das medições principais

As medições completas estão em [docs/MEASUREMENTS.md](docs/MEASUREMENTS.md).

### Filtro entre estágios

Configuração:

```text
pino 1 → capacitor → pino 5
pino 5 → 100k → VREF
```

| Capacitor | 80 Hz | 120 Hz | 220 Hz | 440 Hz | 880 Hz | Resultado |
|---|---:|---:|---:|---:|---:|---|
| 104 | 0.0982 | 0.1110 | 0.1185 | 0.0876 | 0.0842 | mais grave/aberto |
| 153 | 0.0613 | 0.0852 | 0.1073 | 0.0851 | 0.0834 | intermediário |
| 103 | 0.0470 | 0.0687 | 0.0942 | 0.0804 | 0.0817 | escolhido, mais definido |

### Pré-clipping 104 vs 224

| C_CLIP_IN | 80 Hz | 120 Hz | 220 Hz | 440 Hz | 880 Hz | 2 kHz | 4 kHz | 8 kHz |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 104 / 100nF | 0.079 | 0.091 | 0.103 | 0.102 | 0.105 | 0.098 | 0.075 | 0.053 |
| 224 / 220nF | 0.092 | 0.104 | 0.115 | 0.114 | 0.116 | 0.108 | 0.090 | 0.069 |

Resultado: **224** trouxe mais corpo e graves.

### Anti-fizz 10k + 3n3

| Frequência | Medição |
|---:|---:|
| 80 Hz | 0.086 |
| 120 Hz | 0.097 |
| 220 Hz | 0.114 |
| 440 Hz | 0.110 |
| 880 Hz | 0.114 |
| 2 kHz | 0.108 |
| 4 kHz | 0.091 |
| 8 kHz | 0.069 |

### Curva de ganho

| Potenciômetro | Medição | Observação |
|---:|---:|---|
| 0% | 0.069 | limpo |
| 25% | 0.097 | melhor ponto |
| 50% | 0.090 | deformação |
| 75% | 0.080 | irregular |
| 100% | 0.078 | irregular |

Sweet spot observado: **20% a 35%**.

## Notas resumidas de PCB

A PCB V1 foi pensada como placa experimental de face simples/THT, com alguns pontos de modificação.

- Camadas: 1.
- Dimensão aproximada: 62,65 mm × 61,8 mm.
- O silk fica do lado dos componentes.
- Jumpers podem cruzar trilhas, mas devem ser feitos com fio isolado quando passarem por cima de cobre/trilhas.
- Jumpers no lado dos componentes não devem encostar em trilhas expostas.
- Conferir especialmente jumpers que cruzam VREF, +9V ou saídas de estágio.

### Estratégia de layout

Fluxo recomendado:

```text
INPUT → STAGE A → STAGE B → CLIP → ANTI-FIZZ → VOLUME → OUTPUT
```

Prioridades:

- RC4558 central.
- Capacitor de desacoplamento o mais perto possível do CI.
- Alimentação e VREF estáveis.
- Entrada longe da saída e de nós de alto ganho.
- Trilhas curtas nos pontos de maior ganho.
- Plano de GND quando possível.
- Evitar laços grandes.
- Conferir orientação dos diodos.
- Conferir pinos do 4558 antes de soldar.

### Footprints usados/planejados

| Componente | Footprint |
|---|---|
| RC4558 | `Package_DIP:DIP-8_W7.62mm` |
| Resistores | `R_Axial_DIN0207_P7.62mm` |
| Diodos | `D_DO35_SOD27_P7.62mm` |
| Capacitores de voicing | `C_Rect_L7.2mm_W2.5mm_P5.00mm` |
| Eletrolíticos | `CP_Radial_D5.0mm_P2.00mm` |
| Potenciômetros | Header 1x3, ligados por fios |
| Jacks | Header 1x2, ligados por fios |

### Pontos úteis de modificação/teste

- `C_INTERSTAGE`: 103 (10nF), 153 (15nF), 104 (100nF).
- `C_CLIP_IN`: 104 (100nF), 224 (220nF).
- `R_LIMIT_GAIN`: 22k, 33k, 47k, 68k.
- `C_FIZZ`: sem capacitor, 2n2 (2.2nF), 3n3 (3.3nF), 4n7 (4.7nF), 6n8 (6.8nF).
- Pads de teste para +9V, GND, VREF, saída do estágio A, entrada do estágio B, CLIP_NODE e OUTPUT.

## Arquivos do repo

```text
README.md                         documentação principal

docs/
  BOM.md                          lista de componentes da PCB V1
  MEASUREMENTS.md                 medições e testes de desenvolvimento
  PseudoMetalZone_V1_Schematic.pdf
                                  esquemático exportado em PDF
  maloyk.png                      imagem de referência/inspiração

hardware/
  PseudoMetalZone_V1.kicad_sch    esquemático do KiCad
  PseudoMetalZone_V1.kicad_pcb    layout da PCB
  PseudoMetalZone_V1.kicad_pro    arquivo do projeto
  PseudoMetalZone_V1.kicad_prl    preferências do projeto

gerbers/
  PseudoMetalZone_V1-F_Cu.gtl     cobre frente
  PseudoMetalZone_V1-B_Cu.gbl     cobre verso
  PseudoMetalZone_V1-F_Mask.gts   máscara de solda frente
  PseudoMetalZone_V1-B_Mask.gbs   máscara de solda verso
  PseudoMetalZone_V1-F_Silkscreen.gto
                                  serigrafia frente
  PseudoMetalZone_V1-B_Silkscreen.gbo
                                  serigrafia verso
  PseudoMetalZone_V1-Edge_Cuts.gm1
                                  contorno da placa
  PseudoMetalZone_V1.drl          furos (drill)
  PseudoMetalZone_V1-job.gbrjob   configuração do job
```

## Procedimentos para nova placa

1. Conferir continuidade de +9V, GND e VREF antes de inserir o CI.
2. Alimentar a placa sem o 4558 e medir:
   - +9V no pino 8 do soquete.
   - GND no pino 4 do soquete.
   - VREF próximo de 4,5 V.
3. Conferir se não há curto entre +9V e GND.
4. Inserir o 4558 no soquete.
5. Testar áudio limpo em ganho baixo.
6. Testar ganho progressivamente.
7. Medir saída do estágio A, entrada do estágio B, CLIP_NODE e saída final.
8. Testar com guitarra real e baixo real antes de decidir novas alterações.

## Decisões a serem avaliadas depois

- Se o 224 antes dos diodos será mantido definitivamente.
- Se o anti-fizz 3n3 está no ponto ideal.
- Se o resistor de limite de ganho fica em 47k ou outro valor.
- Se o pedal precisa de tone control mais elaborado.
- Se o volume master e o capacitor de saída estão com valores ideais.
- Se vale comparar novamente 4558 vs TL072 na PCB.
