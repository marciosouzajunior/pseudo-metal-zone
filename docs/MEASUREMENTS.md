# Medições e testes

Medições coletadas durante o desenvolvimento do Pseudo Metal Zone em protoboard e na preparação da PCB V1.

## Ambiente geral de teste

- CI principal: RC4558/JRC4558.
- Alimentação: 9 V.
- VREF: aproximadamente 4,5 V.
- Sinal de teste: celular em aproximadamente 50% de volume, onda senoidal, principalmente 440 Hz.
- Instrumento real também testado: baixo.
- Ferramenta de visualização: Visual Analyser via entrada de áudio do computador.
- Saída medida sempre depois de capacitor de acoplamento, não diretamente no pino do op-amp.

## Testes iniciais de resistor de ganho para VREF

Configuração conceitual do estágio inicial:

```text
pino 1 → pot/feedback → pino 2
pino 2 → resistor → VREF
```

### 1k

Cores: marrom, preto, vermelho.

Com tom de 440 Hz:

| Pot | Medição aproximada |
|---:|---:|
| 50% | 0.203 V RMS |
| 100% | 0.357 V RMS |

Observações:

- Ganho muito alto.
- Clipping natural do op-amp a partir de aproximadamente 75% do pot.
- Com diodos, começa a clipar por volta de 25% do pot.
- Excelente para distorção pesada, mas com pouca faixa limpa.

### 2.2k

Cores: vermelho, vermelho, vermelho.

| Pot | Medição aproximada |
|---:|---:|
| 50% | 0.097 V RMS |
| 100% | 0.187 V RMS |

Observações:

- Melhor compromisso entre controle e ganho na fase inicial.
- Sem clipping natural tão significativo quanto 1k.

### 4.7k

Cores: amarelo, violeta, vermelho.

| Pot | Medição aproximada |
|---:|---:|
| 50% | 0.047 V RMS |
| 100% | 0.090 V RMS |

Observações:

- Som limpo.
- Ganho baixo para o objetivo de distorção pesada em estágio único.
- Acabou sendo usado no Estágio A da arquitetura de dois estágios.

### 10k

Cores: marrom, preto, laranja.

| Pot | Medição aproximada |
|---:|---:|
| 100% | 0.053 V RMS |

Observações:

- Muito limpo.
- Ganho insuficiente para a proposta como estágio principal de distorção.

## Teste com baixo real: 2.2k sem diodos

| Pot | Medição aproximada | Observação |
|---:|---:|---|
| 0% | 0.003 V | quase inaudível no computador |
| 50% | 0.05 V | audível e visível na onda |
| 100% | 0.10 V | mais alto, ainda controlado |

## Teste com baixo real: 2.2k + 2 diodos depois do 104

| Pot | Medição aproximada | Observação |
|---:|---:|---|
| 0% | 0.003 V | quase inaudível no computador |
| 50% | 0.05 V | audível, onda balançando e alguma distorção |
| 100% | 0.06 V | mais distorção, mas volume limitado |

Interpretação:

- Os diodos limitam o sinal.
- Ganho adicional vira compressão, harmônicos e distorção, não necessariamente mais volume.

## Clipping por saturação do op-amp

Sem diodos, o RC4558 também mostrou clipping quando o ganho ficou alto o suficiente.

Condição observada:

- Resistor de 1k.
- Pot acima de aproximadamente 75%.
- Senoide começa a achatar.

Conclusão:

- O 4558 sozinho consegue gerar distorção por saturação.
- Os diodos tornam o comportamento mais controlado e previsível.

## Clipping com diodos

### Um diodo

- Clipagem assimétrica.
- Difícil perceber em alguns testes.

### Dois diodos antiparalelos

- Clipagem simétrica.
- Claramente visível no osciloscópio.
- Funcional, mas menos interessante que a assimétrica com três diodos.

### Três diodos assimétricos

Configuração favorita:

```text
CLIP_NODE
├─ 1 diodo para GND em um sentido
└─ 2 diodos em série para GND no sentido oposto
```

Resultado:

- Mais interessante harmonicamente.
- Mais próximo do objetivo de distorção pesada, com menos sensação de clipagem “reta”.

## Filtro entre estágios

Configuração testada:

```text
pino 1 → capacitor → pino 5
pino 5 → 100k → VREF
```

| Capacitor | 80 Hz | 120 Hz | 220 Hz | 440 Hz | 880 Hz | Leitura subjetiva |
|---|---:|---:|---:|---:|---:|---|
| 104 / 100nF | 0.0982 | 0.1110 | 0.1185 | 0.0876 | 0.0842 | mais grave, mais aberto |
| 153 / 15nF | 0.0613 | 0.0852 | 0.1073 | 0.0851 | 0.0834 | intermediário |
| 103 / 10nF | 0.0470 | 0.0687 | 0.0942 | 0.0804 | 0.0817 | mais definido, escolhido |

Escolha atual: **103 / 10nF**.

## Pré-clipping: 104 vs 224

Configuração:

```text
pino 7 → C_CLIP_IN → CLIP_NODE
```

| C_CLIP_IN | 80 Hz | 120 Hz | 220 Hz | 440 Hz | 880 Hz | 2 kHz | 4 kHz | 8 kHz |
|---|---:|---:|---:|---:|---:|---:|---:|---:|
| 104 / 100nF | 0.079 | 0.091 | 0.103 | 0.102 | 0.105 | 0.098 | 0.075 | 0.053 |
| 224 / 220nF | 0.092 | 0.104 | 0.115 | 0.114 | 0.116 | 0.108 | 0.090 | 0.069 |

Resultado:

- 224 produz mais corpo e graves.
- 104 fica mais tight.
- Escolha atual da PCB V1: **224 / 220nF**.

## Anti-fizz

Configuração:

```text
CLIP_NODE → 10k → OUTPUT_FILTER
OUTPUT_FILTER → 3n3 → GND
```

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

Resultado:

- Reduziu fizz/chiado dos agudos.
- Manteve definição suficiente.
- Configuração atual: **10k + 3n3**.

## Curva de ganho da configuração atual

| Potenciômetro | Medição | Observação |
|---:|---:|---|
| 0% | 0.069 | limpo |
| 25% | 0.097 | melhor ponto |
| 50% | 0.090 | deformação |
| 75% | 0.080 | irregular |
| 100% | 0.078 | irregular |

Sweet spot observado:

```text
20% a 35%
```

Interpretação:

- O ganho máximo não foi o melhor som.
- Acima de certo ponto há compressão, deformação e irregularidade.
- O resistor em paralelo no pot ajuda a evitar colapso da onda.

## Resistor em paralelo no pot de ganho

Testados:

| Valor | Resultado |
|---|---|
| 22k | funcionou, limitou ganho |
| 47k | funcionou, preferência atual |

Conclusão:

- Ambos evitaram colapso da onda.
- Diferença subjetiva pequena.
- PCB V1 usa **47k** como ponto inicial.

## FFT e harmônicos

Sem clipping:

- Pico dominante na frequência fundamental, por exemplo 440 Hz.

Com clipping:

- Surgem harmônicos.
- Exemplos observados/conceituais: 440 Hz, 1320 Hz, 2200 Hz, 3080 Hz.

Conclusão:

- A FFT confirmou que o clipping cria conteúdo harmônico adicional.
- O osciloscópio mostra o achatamento da onda, enquanto a FFT mostra o enriquecimento harmônico.

## Observações de teste

- O celular em 50% foi uma referência prática para comparação entre mudanças.
- O baixo real gera sinal diferente e deve ser usado para validação sonora final.
- Com áudio limpo de celular, havia pouca distorção abaixo de aproximadamente 75% em algumas configurações iniciais.
- Com diodos, o volume tende a limitar e o aumento de ganho vira mais distorção/compressão.
- O anti-fizz foi uma das mudanças mais úteis para reduzir aspereza.
