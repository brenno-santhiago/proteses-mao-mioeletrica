# Guia técnico de código aberto: protótipo de mão robótica controlada por sinais mioelétricos (sEMG)

Guia passo a passo para construir um protótipo de mão robótica de baixo custo, com estrutura impressa em 3D, microcontrolador ESP32, sensor de eletromiografia de superfície (MyoWare 2.0) e três servomotores.

Este repositório acompanha o Trabalho Final de Graduação II (TFG II) do curso de Engenharia de Computação da Afya Montes Claros (2026).

> **Aviso importante.** Este é um protótipo acadêmico e demonstrativo, testado apenas em bancada. Ele **não é um dispositivo médico**, não foi validado clinicamente e não substitui uma prótese prescrita, adaptada e acompanhada por profissionais habilitados.

---

## Sumário

1. [O que o protótipo faz](#1-o-que-o-protótipo-faz)
2. [Limitações](#2-limitações)
3. [Lista de materiais e custos](#3-lista-de-materiais-e-custos)
4. [Estrutura do repositório](#4-estrutura-do-repositório)
5. [Passo 1: imprimir a estrutura](#passo-1-imprimir-a-estrutura-em-3d)
6. [Passo 2: montar a parte mecânica](#passo-2-montar-a-parte-mecânica)
7. [Passo 3: ligar a eletrônica](#passo-3-ligar-a-eletrônica)
8. [Passo 4: gravar o código no ESP32](#passo-4-gravar-o-código-no-esp32)
9. [Passo 5: calibrar e testar](#passo-5-calibrar-e-testar)
10. [Parâmetros do código](#parâmetros-do-código)
11. [Problemas comuns](#problemas-comuns)
12. [Licença](#licença)
13. [Como citar](#como-citar)

---

## 1. O que o protótipo faz

O sensor MyoWare 2.0 capta a atividade elétrica do músculo. O ESP32 lê esse sinal, calcula uma média móvel e converte a intensidade da contração em um ângulo de **0° (aberto) a 90° (fechado)**. Esse mesmo ângulo é enviado aos três servomotores, que puxam os tendões artificiais (fios de nylon) e fecham a mão. Ao relaxar o músculo, os elásticos devolvem os dedos à posição aberta.

Os servomotores são divididos assim:

| Servo | Dedos acionados | Pino do ESP32 |
|-------|-----------------|---------------|
| 1 | Polegar | GPIO 18 |
| 2 | Indicador e médio | GPIO 19 |
| 3 | Anelar e mínimo | GPIO 21 |

Cada dedo tem o seu próprio tendão, mas os dedos de um mesmo grupo se movem juntos.

## 2. Limitações

- Usa **um único canal sEMG**. O sistema mede a *intensidade* da contração (controle proporcional). Ele **não** diferencia gestos como pinça e preensão cilíndrica.
- Não foram feitas medições de tempo de resposta, força de preensão, repetibilidade ou durabilidade.
- Os ensaios foram feitos em bancada, sem usuários externos e sem encaixe para membro residual.
- A leitura do sinal é feita a cerca de 100 Hz (laço com `delay(10)`), taxa baixa para sEMG bruto. Funciona como estimativa grosseira da intensidade da contração.
- O PLA amolece em temperaturas próximas de 60 °C. Não deixe o protótipo em carro fechado ou perto de fontes de calor.
- A reprodução do guia por outras pessoas ainda não foi avaliada.

## 3. Lista de materiais e custos

Valores de referência de 2026, informados pelo autor. Podem variar conforme loja, região e data.

| Componente | Qtd. | Valor unitário (R$) | Custo (R$) |
|------------|------|---------------------|------------|
| Placa ESP32 D1 Mini | 1 | 53,51 | 53,51 |
| Sensor MyoWare 2.0 com eletrodos | 1 | 357,35 | 357,35 |
| Servomotor MG | 3 | 25,00 | 75,00 |
| Filamento PLA (325 g de um rolo de 1 kg a R$ 85,00) | 325 g | 0,085/g | 27,63 |
| Materiais adicionais (fio de nylon, elásticos ortodônticos, parafusos M3, cabos jumper) | 1 conjunto | 40,00 | 40,00 |
| Fonte dedicada para os servos | 1 | **PREENCHER** | **PREENCHER** |
| **Subtotal dos itens com valor informado** | | | **553,49** |

Não estão incluídos: impressora 3D, ferramentas, energia elétrica, frete, impostos e a fonte dos servos. Para comprar o filamento é preciso levar o rolo inteiro (R$ 85,00).

**PREENCHER:** modelo exato dos servomotores, modelo da fonte (tensão e corrente) e valor dos capacitores de desacoplamento usados.

## 4. Estrutura do repositório

```
.
├── README.md        este guia
├── LICENSE          licença do código
├── codigo/          arquivo .ino para o ESP32
├── stl/             arquivos 3D para impressão
├── docs/            diagrama de ligação e outros documentos
└── imagens/         fotos do protótipo e capturas do Serial Plotter
```

---

## Passo 1: imprimir a estrutura em 3D

**Resultado esperado:** todas as peças da mão impressas em PLA, com cerca de 325 g de filamento e 17 horas de impressão no total.

1. Baixe os arquivos da pasta `stl/`.
2. Abra cada arquivo no fatiador (por exemplo, Cura ou PrusaSlicer).
3. Configure a impressão. Os valores usados pelo autor devem ser preenchidos abaixo.
4. Imprima todas as peças e remova os suportes.

| Parâmetro | Valor usado |
|-----------|-------------|
| Impressora | **PREENCHER** |
| Material | PLA |
| Tecnologia | FDM |
| Diâmetro do bico | **PREENCHER** |
| Altura de camada | **PREENCHER** |
| Preenchimento (infill) | **PREENCHER** |
| Número de paredes | **PREENCHER** |
| Velocidade | **PREENCHER** |
| Suportes | **PREENCHER** |

**Origem dos modelos 3D:** **PREENCHER.** Informe quem projetou a mão (modelo próprio ou derivado de outro projeto), com link e licença do original. Se for derivado, mantenha o crédito e a licença do autor original.

Dica: a orientação da peça na mesa influencia a resistência entre camadas. Imprima de forma que as camadas não fiquem na direção em que a peça é puxada pelos tendões.

## Passo 2: montar a parte mecânica

**Resultado esperado:** mão montada, com um tendão por dedo e elásticos que devolvem os dedos à posição aberta.

1. Una as peças da palma e dos dedos usando parafusos M3.
2. Passe um fio de nylon por dentro de cada dedo (um tendão por dedo) e prenda a ponta na ponta do dedo.
3. Fixe um elástico ortodôntico em cada dedo para fazer o retorno à posição aberta.
4. Fixe os três servomotores na estrutura.
5. Prenda os tendões nos eixos dos servos conforme os grupos:
   - Servo 1: polegar
   - Servo 2: indicador e médio
   - Servo 3: anelar e mínimo
6. Com os servos na posição de 0° (aberto), ajuste a tensão dos tendões para que a mão fique aberta, sem folga excessiva.

**PREENCHER:** inclua fotos de cada etapa na pasta `imagens/` e referencie aqui.

## Passo 3: ligar a eletrônica

**Resultado esperado:** ESP32, sensor e servos ligados, com alimentação separada para os servos.

### Ligações

| De | Para |
|----|------|
| MyoWare 2.0, saída **RAW** | ESP32, **GPIO 34** |
| MyoWare 2.0, alimentação (+) | ESP32, 3V3 |
| MyoWare 2.0, GND (-) | ESP32, GND |
| Servo 1 (sinal) | ESP32, GPIO 18 |
| Servo 2 (sinal) | ESP32, GPIO 19 |
| Servo 3 (sinal) | ESP32, GPIO 21 |
| Servos, alimentação (+) | **Fonte dedicada** (positivo) |
| Servos, GND (-) | **Fonte dedicada** (negativo) **e** GND do ESP32 |
| LED de calibração | LED da própria placa, GPIO 2 |

Confirme as ligações com o seu hardware antes de ligar. O código lê o valor do GPIO 34 e subtrai 2048 (meio da escala de 0 a 4095 do ADC de 12 bits). Isso corresponde à saída RAW do MyoWare, cujo sinal fica centrado no meio da escala. Se você usar a saída ENV, esse cálculo precisa ser ajustado.

### Regras de alimentação

1. **Não alimente os servos pelo ESP32.** Servos puxam muita corrente quando estão sob esforço e podem reiniciar o ESP32.
2. Use uma **fonte dedicada aos servos**, com tensão compatível com o modelo escolhido e corrente suficiente para os três ao mesmo tempo.
3. Ligue o **GND da fonte dos servos ao GND do ESP32** (GND comum). Sem isso, o sinal de controle não funciona.
4. Coloque **capacitores de desacoplamento** próximos à alimentação dos servos para reduzir quedas de tensão e ruído.
5. Não faça a corrente dos servos passar pelo mesmo caminho de alimentação do sensor.

**PREENCHER:** diagrama de ligação (Fritzing, KiCad ou desenho à mão fotografado) em `docs/diagrama-ligacao.png` e referencie aqui:

```
![Diagrama de ligação](docs/diagrama-ligacao.png)
```

### Posicionamento dos eletrodos

1. Limpe a pele do antebraço onde ficará o sensor.
2. Fixe os eletrodos sobre o ventre do músculo (região que "estufa" quando você fecha a mão).
3. Posicione o terceiro eletrodo (referência) em uma região óssea próxima, como o cotovelo.
4. Se o sensor se deslocar, a leitura muda. Refaça a calibração (Passo 5) sempre que reposicionar.

## Passo 4: gravar o código no ESP32

**Resultado esperado:** código compilado e gravado na placa.

1. Instale a [Arduino IDE](https://www.arduino.cc/en/software).
2. Instale o suporte ao ESP32: em *Arquivo > Preferências*, adicione a URL do gerenciador de placas da Espressif; depois, em *Ferramentas > Placa > Gerenciador de placas*, procure por **esp32** e instale.
3. Instale a biblioteca: *Ferramentas > Gerenciar bibliotecas*, procure por **ESP32Servo** e instale.
4. Abra o arquivo da pasta `codigo/`.
5. Selecione a placa (*Ferramentas > Placa*) e a porta (*Ferramentas > Porta*).
6. Clique em **Carregar**.
7. Abra o *Monitor Serial* com velocidade **115200**.

## Passo 5: calibrar e testar

**Resultado esperado:** a mão fecha proporcionalmente à força da contração e abre ao relaxar.

O programa executa a calibração automática (Auto-Tune) assim que é ligado:

1. O LED da placa (GPIO 2) acende. Você tem **5 segundos**.
2. Nesse período, relaxe totalmente o braço e, em seguida, contraia com força máxima.
3. O LED apaga. O programa define o valor de repouso e o de força máxima, que aparecem no Monitor Serial.
4. A partir daí, a mão responde ao sinal.

Para ver o sinal em tempo real, descomente as linhas do `Serial.printf` no final do `loop()` e abra o *Serial Plotter* (*Ferramentas > Plotter Serial*).

**PREENCHER:** inclua uma captura real do Serial Plotter em `imagens/` e referencie aqui.

## Parâmetros do código

| Parâmetro | Valor |
|-----------|-------|
| Pino do sensor | GPIO 34 |
| Pino do LED | GPIO 2 |
| Pinos dos servos | 18, 19 e 21 |
| Janela da média móvel | 15 amostras |
| Pausa do laço principal | 10 ms |
| Frequência do PWM dos servos | 50 Hz |
| Largura de pulso dos servos | 500 a 2400 µs |
| Ângulo aberto / fechado | 0° / 90° |
| Duração da calibração | 5 s |

## Problemas comuns

| Sintoma | Causa provável | O que fazer |
|---------|----------------|-------------|
| ESP32 reinicia quando os servos se movem | Servos alimentados pelo ESP32 ou fonte fraca | Use fonte dedicada e capacitores de desacoplamento |
| Servos não respondem | Falta de GND comum | Ligue o GND da fonte ao GND do ESP32 |
| Sinal muito ruidoso | Eletrodos mal fixados ou fiação em comum com os servos | Limpe a pele, refixe os eletrodos e separe a alimentação |
| Mão fecha sem contração | Calibração feita com o sensor mal posicionado | Reposicione o sensor e refaça a calibração |
| Mão não fecha por completo | Tendão frouxo ou calibração com contração fraca | Ajuste a tensão e repita a calibração com força máxima |

## Licença

**PREENCHER** (escolha e mantenha coerente com o arquivo `LICENSE`):

- Código: MIT ou GPL.
- Documentação: CC BY 4.0.
- Arquivos 3D e projeto de hardware: a licença do projeto original, se forem derivados; caso sejam próprios, por exemplo CERN-OHL ou CC BY.

## Como citar

ALVES, Brenno Santhiago Pimentel. **Guia técnico de código aberto para construção de um protótipo de mão robótica controlada por sinais mioelétricos (sEMG)**. 2026. Trabalho Final de Graduação II (Engenharia de Computação) – Afya Montes Claros, Montes Claros, 2026.

## Autor

Brenno Santhiago Pimentel Alves, com orientação do Prof. Msc. Herbert Souza e Silva e coorientação do Prof. Msc. Délcio Pereira Fortes.
