# 🧮 Documentação Técnica e Manual de Cálculos — Modelagem FP&A

Documentação técnica detalhada das metodologias de cálculo financeiro, arquitetura em camadas e premissas aplicadas aos modelos da pasta `dashboards_modelos/`.

---

## 🏛️ Arquitetura da Solução: Fluxo de Dados e Modelagem Financeira

A solução foi estruturada em etapas, utilizando Python para geração e preparação dos dados fictícios e Google Sheets para modelagem financeira, cálculos analíticos e visualização gerencial. Essa arquitetura permite separar a geração dos dados da camada de análise e apresentação dos resultados.

```text
┌────────────────────────────────────────────────────────────┐
│                  PYTHON                                    │
│          Geração de Dados Fictícios                        │
│  • Base de clientes                                        │
│  • Base de faturas                                         │
│  • Dados operacionais e financeiros                        │
└────────────────────────────┬───────────────────────────────┘
                             │
                             │ Exportação das Bases
                             ▼
┌────────────────────────────────────────────────────────────┐
│                  GOOGLE SHEETS                             │
│             Modelo FP&A - Base Tratada                     │
│              (Camada de Modelagem)                         │
│  • Premissas e cenários                                    │
│  • Projeção operacional mensal (3 cenários)                │
│  • Fluxo de caixa                                          │
│  • Aging e PDD                                             │
│  • Viabilidade financeira                                  │
│  • Sensibilidade                                           │
│  • Resumo de liquidez                                      │
└────────────────────────────┬───────────────────────────────┘
                             │
                             │ Consolidação dos Indicadores
                             ▼
┌────────────────────────────────────────────────────────────┐
│                  GOOGLE SHEETS                             │
│         Dashboard FP&A - Modelo e Indicadores              │
│             (Camada de Visualização)                       │
│  • KPIs financeiros                                        │
│  • Gráficos de fluxo de caixa                              │
│  • Indicadores de viabilidade                              │
│  • Aging de recebíveis                                     │
│  • Análise de cenários                                     │
└────────────────────────────────────────────────────────────┘
```

---

## 📄 Descrição dos Arquivos e Estrutura

### 1. `Modelo FP&A - Base Tratada.xlsx`

**Objetivo:** Concentrar toda a inteligência de negócios, projeções operacionais, réguas de cobrança e cálculos de viabilidade.

**Estrutura — 13 Abas:**

| Aba | Descrição |
|---|---|
| `Premissas` | Data-base, saldo inicial de caixa, despesa fixa, alíquota de impostos, WACC, CAPEX, parâmetros por cenário (crescimento, inadimplência, custo variável, atraso extra), curva de recebimento, régua de PDD e fluxos anuais do projeto. |
| `clientes` | Cadastro de clientes e perfil de pagamento. |
| `faturas` | Emissão, vencimento, pagamento, faixa de atraso e PDD de cada fatura. |
| `fluxo_historico` | Faturamento e recebimentos históricos mensais. |
| `curva_recebimento` | Distribuição percentual dos recebimentos por mês após a emissão. |
| `aging_detalhe` | Faturas em aberto com dias de atraso, faixa e PDD estimada. |
| `aging_resumo` / `Aging` | Consolidação do aging por faixa, com saldo, PDD e % da carteira. |
| `Fluxo_Caixa` | Projeção mensal de 12 meses por cenário: faturamento, recebimentos, custos, impostos, despesas, CAPEX, entrada do projeto e saldo de caixa. |
| `fluxo_projetado` | Versão tabular (uma linha por cenário e mês) do `Fluxo_Caixa`. |
| `Viabilidade` | Fluxos do projeto, VPL, TIR, Payback Simples e Descontado por cenário, e matriz de sensibilidade (fluxos × WACC). |
| `projeto_indicadores` | Indicadores de viabilidade consolidados. |
| `resumo_liquidez` | Caixa mínimo, mês do mínimo, mês de recuperação, saldo final em 12 meses e necessidade de financiamento. |

### 2. `Dashboard FP&A - Modelo e Indicadores.xlsx` — Camada Visual

<img width="787" height="481" alt="Dashboard FP A - Modelo e Indicadores Executivo" src="https://github.com/user-attachments/assets/14a5d673-446c-4fc5-a057-3867fa5b417b" />

**Objetivo:** Fornecer um painel limpo, sem poluição de linhas de grade ou cabeçalhos complexos, focado na alta gestão.

**Estrutura — 2 Abas:**

| Aba | Descrição |
|---|---|
| `Base de Dados` | Aba de suporte (visível) que consolida os resultados do arquivo `Modelo FP&A - Base Tratada.xlsx` (aging, sensibilidade do VPL, indicadores de viabilidade e fluxo de caixa projetado) e alimenta os KPIs e gráficos do `Dashboard Executivo`. Os valores são consolidados a partir do modelo e não possuem vínculo automático com ele. |
| `Dashboard Executivo` | Painel principal com 5 KPIs (VPL, TIR e Payback, PDD total, Caixa Mínimo e Saldo Final 12M), tabela de viabilidade por cenário, estrutura do aging e 3 gráficos: evolução do saldo de caixa (12 meses, Nov/26 a Out/27), carteira de recebíveis (saldo vs. PDD por faixa) e sensibilidade do VPL. |

---

## 📐 Metodologia dos Cálculos Financeiros

### 📌 1. Taxa de Desconto (WACC)

Adotou-se uma taxa de desconto de **14,0% a.a.** (parâmetro `WACC` da aba `Premissas`) para a análise de viabilidade dos projetos, refletindo a Taxa Mínima de Atratividade (TMA) ajustada ao risco do projeto.

```math
\text{WACC} = 14{,}0\% \ \text{a.a.}
```

**Por quê:** a taxa de desconto representa o retorno mínimo exigido. Fluxos futuros valem menos que fluxos presentes, e essa taxa converte cada fluxo em valor de hoje. Ela é a referência de comparação para o VPL e para a TIR.

### 📌 2. Valor Presente Líquido (VPL)

O VPL mede o valor presente dos fluxos de caixa futuros descontados à taxa WACC, subtraído do investimento inicial ($`\text{CAPEX} = \text{R\$ } 2.000.000{,}00`$).

```math
\text{VPL} = \sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + \text{WACC})^t} - \text{CAPEX}
```

Onde $`t`$ = ano (1 a $`n`$), $`n`$ = 5 anos (horizonte de análise), $`\text{FC}_t`$ = fluxo de caixa líquido do projeto no ano $`t`$, e o CAPEX é desembolsado em $`t = 0`$.

**Fluxos do projeto (R$ mil):**

| Cenário | Ano 1 | Ano 2 | Ano 3 | Ano 4 | Ano 5 |
|---|---:|---:|---:|---:|---:|
| Pessimista | 350 | 450 | 550 | 600 | 650 |
| Base | 600 | 750 | 850 | 900 | 950 |
| Otimista | 800 | 1.000 | 1.200 | 1.300 | 1.400 |

**Resultados por cenário — WACC de 14% a.a.:**

| Cenário | VPL |
|---|---:|
| Pessimista | -R$ 282.649,94 |
| Base | R$ 703.414,71 |
| Otimista | R$ 1.778.008,22 |

**Regra de decisão:** VPL > 0 indica que o projeto remunera acima da taxa de desconto.

#### Sensibilidade do VPL (Cenário Base): variação dos fluxos × WACC

A matriz aplica uma variação percentual $`\Delta`$ aos fluxos do projeto e recalcula o VPL para diferentes taxas de desconto $`r`$:

```math
\text{VPL}(\Delta, r) = -\text{CAPEX} + (1 + \Delta) \times \sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + r)^t}
```

**Por quê:** o CAPEX é mantido constante porque é um desembolso contratado no início, enquanto a incerteza está nos fluxos futuros e na taxa de desconto.

| Variação dos fluxos | WACC 10% | WACC 12% | WACC 14% | WACC 16% | WACC 18% |
|---|---:|---:|---:|---:|---:|
| -40% | -R$ 194.903 | -R$ 290.213 | -R$ 377.951 | -R$ 458.875 | -R$ 533.652 |
| -30% | R$ 105.946 | -R$ 5.249 | -R$ 107.610 | -R$ 202.021 | -R$ 289.261 |
| -20% | R$ 406.795 | R$ 279.716 | R$ 162.732 | R$ 54.834 | -R$ 44.870 |
| -10% | R$ 707.645 | R$ 564.680 | R$ 433.073 | R$ 311.688 | R$ 199.522 |
| Base (0%) | R$ 1.008.494 | R$ 849.645 | R$ 703.415 | R$ 568.542 | R$ 443.913 |
| +10% | R$ 1.309.344 | R$ 1.134.609 | R$ 973.756 | R$ 825.396 | R$ 688.304 |

> O dashboard exibe a coluna **WACC 14%**, nas linhas de -30% a +10%.

### 📌 3. Taxa Interna de Retorno (TIR)

A TIR representa a taxa de desconto que iguala o VPL do projeto a zero.

```math
\sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + \text{TIR})^t} - \text{CAPEX} = 0
```

**Por quê:** a TIR expressa o retorno do projeto como taxa anual e é comparada diretamente com o WACC. Quando a TIR supera o WACC, o VPL é positivo. Não tem solução algébrica fechada, por isso é obtida de forma iterativa (função `IRR` da planilha).

**Resultados por cenário:**

| Cenário | TIR |
|---|---:|
| Pessimista | 8,5% |
| Base | 26,7% |
| Otimista | 43,1% |

### 📌 4. Payback Simples e Descontado

**Payback Simples:** tempo necessário para recuperar o investimento inicial sem considerar o valor do dinheiro no tempo.

```math
\text{Payback Simples} = t - 1 + \frac{\left| \text{Saldo Acumulado}_{t-1} \right|}{\text{FC}_t}
```

**Payback Descontado:** tempo necessário para que a soma dos fluxos descontados a 14% a.a. iguale o investimento inicial.

```math
\text{Payback Descontado} = t - 1 + \frac{\left| \text{Saldo Acumulado Descontado}_{t-1} \right|}{\text{Fluxo Descontado}_t}
```

Onde $`t`$ é o primeiro ano em que o saldo acumulado deixa de ser negativo, e $`\text{Fluxo Descontado}_t = \text{FC}_t / (1 + \text{WACC})^t`$.

**Por quê:** a fração do ano é obtida por interpolação linear, supondo que o fluxo do ano $`t`$ ocorre de forma uniforme. O descontado é mais conservador, pois reconhece o custo do capital.

**Exemplo (cenário Base):** após o ano 2, restam R$ 650.000 a recuperar. O fluxo do ano 3 é de R$ 850.000, logo o Payback Simples é 2 + 650.000 ÷ 850.000 = **2,76 anos**.

**Resultados por cenário:**

| Cenário | Payback Simples | Payback Descontado |
|---|---:|---:|
| Pessimista | 4,08 anos | Não se paga em 5 anos |
| Base | 2,76 anos | 3,61 anos |
| Otimista | 2,17 anos | 2,65 anos |

**Nota:** para o cenário pessimista, o Payback Descontado exibe a classificação *"Não se paga em 5 anos"*, pois os fluxos gerados no período de 5 anos, descontados a 14% a.a., somam R$ 1.717.350,06, valor insuficiente para amortizar o investimento inicial de R$ 2,0 milhões.

### 📌 5. Projeção de Fluxo de Caixa (12 Meses — Nov/26 a Out/27)

O saldo de caixa parte do **saldo inicial de R$ 1.500.000,00** (data-base 31/10/2026). A cada mês, soma-se o fluxo operacional, deduz-se o CAPEX (R$ 2,0 milhões, desembolsado integralmente em Nov/26) e somam-se as entradas do projeto (fluxo do ano 1 dividido por 12).

```math
\begin{aligned}
\text{Saldo Final}_m ={}& \text{Saldo Final}_{m-1} + \text{Recebimentos}_m - \text{Custos Variáveis}_m \\
& - \text{Impostos}_m - \text{Despesas Fixas}_m - \text{CAPEX}_m + \text{Entrada do Projeto}_m
\end{aligned}
```

Onde $`\text{Saldo Final}_0 = \text{Saldo Inicial} = \text{R\$ } 1.500.000{,}00`$ e $`\text{Entrada do Projeto}_m = \text{FC}_1 / 12`$.

#### Construção de cada linha do fluxo

| Linha | Fórmula |
|---|---|
| Faturamento | $`\text{FatBase} \times (1 + g)^m`$ |
| Recebimentos | $`(1 - \text{Inad}) \times \sum_{k=0}^{4} p_k \times \text{Faturamento}_{m-k-a}`$ |
| Custos Variáveis | $`\text{Faturamento}_m \times \%\text{CV}`$ |
| Impostos | $`\text{Faturamento}_m \times 8\%`$ |
| Despesas Fixas | $`\text{DespFixaBase} \times (1 + g_{DF})^m`$ |
| CAPEX | $`\text{CAPEX}`$ se $`m = 1`$; caso contrário, 0 |

Onde $`m`$ é o mês da projeção (1 = Nov/26), $`p_k`$ é o percentual recebido $`k`$ meses após a emissão e $`a`$ é o atraso extra do cenário, em meses. Para meses anteriores a Nov/26, o faturamento é o histórico realizado.

**Premissas:**

| Parâmetro | Pessimista | Base | Otimista |
|---|---:|---:|---:|
| Crescimento mensal do faturamento ($`g`$) | -0,5% | 0,8% | 1,8% |
| Inadimplência (Inad) | 6,0% | 3,0% | 1,5% |
| Custo variável (%CV) | 62% | 58% | 55% |
| Crescimento mensal das despesas fixas ($`g_{DF}`$) | 0,8% | 0,4% | 0,3% |
| Atraso extra nos recebimentos ($`a`$) | 1 mês | 0 | 0 |

Parâmetros comuns: faturamento base (`FatBase`) de R$ 862.015,23, média de ago. a out./2026, adotada como referência normalizada para mitigar oscilações pontuais; despesa fixa base (`DespFixaBase`) de R$ 150.000,00 por mês; impostos de 8% sobre o faturamento.

**Curva de recebimento** (calibrada no histórico de faturas):

| Meses após a emissão ($`k`$) | 0 | 1 | 2 | 3 | 4 |
|---|---:|---:|---:|---:|---:|
| % recebido ($`p_k`$) | 15,2% | 60,2% | 16,7% | 4,9% | 3,0% |

**Por quê:**

- O **CAPEX no mês 1** reflete o desembolso integral do investimento no início do projeto. Por isso o caixa atinge o mínimo em Nov/26, mitigado pelo saldo inicial e pelas entradas do projeto.
- A **curva de recebimento** converte faturamento (regime de competência) em entrada de caixa (regime de caixa), pois a venda de hoje é recebida ao longo dos meses seguintes.
- A **inadimplência** reduz o valor efetivamente recebido, e o **atraso extra** do cenário pessimista desloca a curva em 1 mês.
- A **entrada do projeto** distribui o fluxo do ano 1 em 12 parcelas iguais, integrando a análise de viabilidade (anual) à projeção de liquidez (mensal).

**Interpretação da linha "Saldo Final 12M":** o valor de Out/27 não é a soma dos 12 meses, mas o **saldo acumulado de caixa no encerramento da projeção**.

| Cenário | Caixa Mínimo | Mês Crítico | Saldo volta a ser positivo | Saldo Final em Out/27 |
|---|---:|:---:|:---:|---:|
| Pessimista | -R$ 417.247,18 | Nov/26 | Abr/27 | R$ 485.391,60 |
| Base | -R$ 310.082,32 | Nov/26 | Jan/27 | R$ 1.570.697,81 |
| Otimista | -R$ 257.977,01 | Nov/26 | Jan/27 | R$ 2.399.064,46 |

**Necessidade de financiamento:** equivale ao módulo do caixa mínimo, ou seja, R$ 417.247,18 (Pessimista), R$ 310.082,32 (Base) e R$ 257.977,01 (Otimista).

**Exemplo de conferência (Base, Nov/26):** 1.500.000,00 + 139.917,68 (fluxo operacional) − 2.000.000,00 (CAPEX) + 50.000,00 (entrada do projeto) = **−310.082,32**.

### 📌 6. Aging de Recebíveis e PDD Estimada

A carteira de títulos em aberto é agrupada por faixas de atraso em relação ao vencimento, aplicando-se alíquotas progressivas de provisão para devedores duvidosos (PDD).

```math
\text{Dias de Atraso} = \text{Data-base} - \text{Data de Vencimento}
```

```math
\text{PDD} = \sum_{f} \text{Saldo}_f \times \text{Alíquota}_f
```

Onde $`f`$ representa cada faixa de atraso. Títulos com dias de atraso negativos ou nulos estão "a vencer". A data-base é 31/10/2026.

**Por quê:** a alíquota cresce com o atraso porque a probabilidade de perda aumenta quanto mais o título envelhece. O cálculo é feito fatura a fatura e depois consolidado por faixa.

| Faixa de Atraso | Qtd. Faturas | Alíquota PDD | Saldo (R$) | PDD Estimada (R$) |
|---|---:|---:|---:|---:|
| A vencer | 47 | 0,0% | 1.288.900,65 | 0,00 |
| 1 a 30 dias | 10 | 1,0% | 237.635,67 | 2.376,36 |
| 31 a 60 dias | 4 | 5,0% | 106.169,02 | 5.308,45 |
| 61 a 90 dias | 2 | 15,0% | 37.526,05 | 5.628,91 |
| 91 a 180 dias | 0 | 40,0% | 0,00 | 0,00 |
| > 180 dias | 13 | 80,0% | 264.066,57 | 211.253,26 |
| **Total Geral** | **76** | **11,6% (média)** | **1.934.297,96** | **224.566,98** |

A alíquota média do total é calculada como PDD total ÷ saldo total: 224.566,98 ÷ 1.934.297,96 = 11,6%.

---

## 🎯 Objetivo da Documentação

Esta documentação tem como finalidade:

- Garantir transparência metodológica dos cálculos financeiros.
- Facilitar a rastreabilidade das premissas e dos resultados.
- Documentar a arquitetura dos modelos e a integração entre arquivos.
- Apoiar a manutenção e atualização das projeções.
- Demonstrar a aplicação prática de conceitos de FP&A, análise financeira e modelagem de cenários.
