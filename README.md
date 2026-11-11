<h1 align="center">
  <span style="color: #1F6FEB;">📊 Planejamento e Análise Financeira (FP&A) — Viabilidade e Projeção de Caixa</span>
</h1>

[![Google Sheets](https://img.shields.io/badge/Google%20Sheets-Dashboard%20Online-green?style=flat&logo=googlesheets)](https://docs.google.com/spreadsheets/d/e/2PACX-1vSxQjqmEcIs80cMjFdZ_rEunU9m5pkhikqHHfhliB3M0scXMhMAiVl-t5n0R4lipJ7ul9NIpFLdI9uy/pubhtml?gid=0&single=true)
[![Python](https://img.shields.io/badge/Python-3.13-blue?style=flat&logo=python)](notebooks_scripts/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

Este projeto apresenta um modelo integrado de FP&A (Financial Planning & Analysis) para apoiar a decisão de um **investimento de R$ 2.000.000,00 em expansão**. Ele responde, em linguagem de gestão, a três perguntas que todo dono de empresa faz antes de assinar o cheque:

1. **Vale a pena?** O projeto rende mais do que o dinheiro custa?
2. **Vou ficar sem caixa?** Quanto falta no curto prazo e quando o caixa se recupera?
3. **Meus clientes pagam?** Quanto da carteira está em risco de virar prejuízo?

**🔗 Acesso rápido ao painel interativo:** [Visualizar Dashboard Executivo (Google Sheets)](https://docs.google.com/spreadsheets/d/1orvkcc7CfJB8UasnSQDS6_uvQh3GeDsjoS1PBsXirg0/edit?usp=sharing)

---

## 🧭 ${\color{#1F6FEB}\textsf{Resumo para o Dono da Empresa (em 1 minuto)}}$

| Pergunta | Resposta (Cenário Base) | O que isso significa na prática |
|---|---|---|
| **Vale a pena?** | **Sim.** VPL de **R$ 703.414,71** e TIR de **26,7% a.a.** | Depois de descontar o custo do dinheiro (14% a.a.), o projeto ainda deixa R$ 703 mil de lucro em valores de hoje. Ele rende quase o dobro do mínimo que exigimos (14%). |
| **Em quanto tempo o dinheiro volta?** | **2,76 anos** (2 anos e 9 meses). Considerando o custo do dinheiro: **3,61 anos** (3 anos e 7 meses). | É o prazo para recuperar os R$ 2,0 milhões investidos. |
| **Vou ficar sem caixa?** | **Sim, por pouco tempo.** Faltam **R$ 310.082,32** em Nov/26, e o caixa volta ao positivo em **Jan/27**. | A operação gera caixa todo mês. O buraco existe porque o investimento de R$ 2,0 milhões sai de uma vez, e só há R$ 1,5 milhão em caixa. |
| **Meus clientes pagam?** | **66,6%** da carteira está em dia. **R$ 264 mil** estão atrasados há mais de 180 dias. | Esse valor está quase perdido: provisionamos **R$ 211 mil** (80%) e a perda total estimada é de **R$ 224.566,98**. |
| **E se as coisas piorarem?** | No Cenário Pessimista, o VPL é **-R$ 282.649,94** e o investimento **não se paga em 5 anos**. | Por isso o plano precisa de uma reserva de caixa e de acompanhamento mensal. |

> 💡 **Margem de segurança:** no Cenário Base, o projeto deixa de dar lucro se as entradas previstas caírem cerca de **26%** (R$ 2.000.000 ÷ R$ 2.703.414,71 = 74%).

> **Decisão de negócio:** a empresa precisa de uma **linha de financiamento pré-aprovada de aproximadamente R$ 310 mil (Base) a R$ 420 mil (Pessimista)** para cobrir a diferença entre o investimento e o caixa disponível no primeiro mês, sem comprometer a operação.

---

## 🧱 ${\color{#1F6FEB}\textsf{Como o Modelo Foi Construído}}$

A solução foi montada em três etapas, cada uma alimentando a seguinte:

```text
┌────────────────────────────────────────────────────────────┐
│                  1. PYTHON                                 │
│          Geração de dados fictícios                        │
│  • Clientes e perfis de pagamento                          │
│  • Faturas emitidas, pagas e em aberto                     │
│  • Histórico de faturamento e recebimentos                 │
└────────────────────────────┬───────────────────────────────┘
                             │  Exportação das tabelas
                             ▼
┌────────────────────────────────────────────────────────────┐
│                  2. GOOGLE SHEETS                          │
│             Modelo FP&A - Base Tratada (13 abas)           │
│  • Premissas e cenários                                    │
│  • Aging (envelhecimento) e PDD                            │
│  • Projeção operacional e de caixa de 12 meses             │
│  • Viabilidade: VPL, TIR, Payback e sensibilidade          │
│  • Resumo de liquidez                                      │
└────────────────────────────┬───────────────────────────────┘
                             │  Consolidação dos indicadores
                             ▼
┌────────────────────────────────────────────────────────────┐
│                  3. GOOGLE SHEETS                          │
│         Dashboard FP&A - Modelo e Indicadores (2 abas)     │
│  • KPIs para a diretoria                                   │
│  • Gráficos de caixa, carteira e sensibilidade             │
└────────────────────────────────────────────────────────────┘
```

**Por que dessa forma?** Separar a geração dos dados, o cálculo financeiro e a apresentação permite conferir cada etapa de forma independente. O Python também calcula os principais indicadores, e os resultados conferem com os da planilha, o que valida as fórmulas.

---

## 🗂️ ${\color{#1F6FEB}\textsf{Dicionário de Dados — Tabelas Criadas}}$

Todas as tabelas abaixo são **fictícias** (`dados_ficticios = True`), geradas com semente fixa (`SEED = 42`), o que garante que o mesmo código produza sempre os mesmos números.

### 👥 `clientes` — 40 registros

Quem compra da empresa e como costuma pagar.

| Campo | O que significa | Por que usamos |
|---|---|---|
| `cliente_id` | Código do cliente (C001 a C040). | Liga o cliente às suas faturas. |
| `nome` | Nome fictício do cliente. | Identificação, sem expor ninguém real. |
| `segmento` | Setor do cliente: Agronegócio (10), Serviços (9), Varejo (8), Indústria (7) ou Saúde (6). | Mostra se a carteira está diversificada. |
| `prazo_dias` | Prazo concedido para pagar: 30 dias (16 clientes), 45 dias (17) ou 60 dias (7). | Define o vencimento de cada fatura. |
| `perfil_pagamento` | Comportamento de pagamento: **pontual** (24), **atraso leve** (11) ou **atraso grave** (5). | Reproduz uma carteira real, com bons e maus pagadores. |

**Como cada perfil se comporta:**

| Perfil | Quando costuma pagar | Observação |
|---|---|---|
| Pontual | Cerca de 2 dias antes do vencimento | Quase sem risco. |
| Atraso leve | Cerca de 12 dias depois do vencimento | Atrasa, mas paga. |
| Atraso grave | Cerca de 55 dias depois do vencimento | 10% das faturas nunca são pagas. |

### 🧾 `faturas` — 923 registros

Cada nota emitida para um cliente, de Ago/24 a Out/26: **847 pagas** e **76 em aberto**, somando R$ 23,7 milhões (valor médio de R$ 25.666,20 por fatura).

| Campo | O que significa | Por que usamos |
|---|---|---|
| `fatura_id` | Número da fatura (F000001 em diante). | Identificação única. |
| `cliente_id` | Cliente que recebeu a fatura. | Liga a fatura ao perfil de pagamento. |
| `data_emissao` | Dia em que a venda foi faturada. | Marca a **receita** (regime de competência). |
| `data_vencimento` | Emissão + prazo do cliente. | Base para medir o atraso. |
| `data_pagamento` | Dia em que o cliente pagou (vazio se não pagou). | Marca a **entrada de caixa**. |
| `valor` | Valor da fatura, em reais. | Base de todos os totais. |
| `status` | "Pago" ou "Em aberto". | Separa o que já virou dinheiro do que ainda é risco. |
| `dias_atraso` | Data-base (31/10/2026) menos vencimento. Negativo significa que ainda vai vencer. | Alimenta o aging. |
| `faixa` | Faixa de atraso da fatura em aberto. | Define o percentual de provisão. |
| `pdd_estimada` | Valor × percentual de provisão da faixa. | Estima a perda provável. |

> **Por que as faturas começam em Ago/24 e o histórico em Nov/24?** Assim, os primeiros meses do histórico já recebem pagamentos de faturas emitidas antes, como numa empresa que já estava operando.

### 📅 `fluxo_historico` — 24 meses (Nov/24 a Out/26)

| Campo | O que significa | Por que usamos |
|---|---|---|
| `mes` | Mês de referência. | Linha do tempo. |
| `faturamento` | Total faturado no mês (competência). Média de **R$ 875.774,05** por mês. | Mostra o tamanho do negócio e é a base da projeção. |
| `recebimentos` | Total que entrou no caixa no mês. | Mostra a diferença entre vender e receber. |

### ⏳ `curva_recebimento` — 5 registros

| Meses após a emissão | 0 | 1 | 2 | 3 | 4 |
|---|---:|---:|---:|---:|---:|
| **% do valor recebido** | 15,2% | 60,2% | 16,7% | 4,9% | 3,0% |

**Como ler:** de cada R$ 100 vendidos, R$ 15 entram no próprio mês, R$ 60 no mês seguinte e o restante nos três meses seguintes. **Por que usamos:** a venda de hoje não vira caixa hoje, e sem essa curva a projeção de caixa seria otimista demais. Ela foi calculada só com faturas emitidas há mais de 180 dias, para não distorcer o resultado com faturas recentes que ainda não tiveram tempo de ser pagas.

### 🕰️ `aging_detalhe` — 76 registros

Lista das faturas em aberto, com os mesmos campos de `faturas` e os três campos de atraso (`dias_atraso`, `faixa`, `pdd_estimada`). **Por que usamos:** mostra, fatura a fatura, de quem a empresa precisa cobrar.

### 📊 `aging_resumo` — 6 faixas

| Campo | O que significa | Por que usamos |
|---|---|---|
| `faixa` | A vencer, 1 a 30, 31 a 60, 61 a 90, 91 a 180 ou acima de 180 dias. | Agrupa o risco por idade. |
| `qtd_faturas` | Número de faturas na faixa. | Mede a quantidade de cobranças. |
| `saldo` | Valor a receber na faixa. | Mede o dinheiro exposto. |
| `pdd_estimada` | Perda provável da faixa. | Mede quanto reservar. |
| `pct_carteira` | Peso da faixa no total. | Mostra onde o risco se concentra. |

### 🔮 `fluxo_projetado` — 36 registros (12 meses × 3 cenários)

| Campo | O que significa | Por que usamos |
|---|---|---|
| `cenario` | Pessimista, Base ou Otimista. | Compara o plano em situações diferentes. |
| `mes` | Mês projetado (Nov/26 a Out/27). | Linha do tempo. |
| `faturamento` | Vendas previstas no mês. | Base de todas as contas. |
| `recebimentos` | O que realmente entra no caixa. | Mostra o caixa, e não só a venda. |
| `custos_variaveis` | Custos que crescem junto com as vendas. | Primeira saída de caixa. |
| `impostos` | 8% do faturamento. | Segunda saída de caixa. |
| `despesas_fixas` | Custos mensais que existem mesmo sem vender. | Terceira saída de caixa. |
| `fluxo_operacional` | Recebimentos − custos variáveis − impostos − despesas fixas. | Mostra se a operação gera caixa. |
| `capex` | Investimento de R$ 2,0 milhões, só no mês 1. | O grande desembolso do projeto. |
| `entrada_projeto` | Fluxo do ano 1 do projeto ÷ 12. | Mostra o ganho mensal do investimento. |
| `saldo_sem_projeto` | Caixa acumulado se o investimento **não** fosse feito. | Separa o efeito do projeto do efeito da operação. |
| `saldo_caixa` | Caixa acumulado com o projeto. | O número que o dono acompanha todo mês. |

### 💧 `resumo_liquidez` — 3 registros

| Campo | O que significa | Por que usamos |
|---|---|---|
| `caixa_minimo` | Pior saldo de caixa em 12 meses. | Mostra o tamanho do aperto. |
| `mes_do_minimo` | Mês em que o aperto ocorre (Nov/26 nos três cenários). | Diz quando agir. |
| `mes_recuperacao` | Primeiro mês com saldo positivo após o aperto. | Diz quando o aperto termina. |
| `saldo_final_12m` | Caixa acumulado em Out/27. | Mostra onde a empresa chega. |
| `necessidade_financiamento` | Valor de crédito necessário para nunca ficar negativo (igual ao módulo do caixa mínimo). | Dimensiona a linha de crédito. |

### 🎯 `projeto_indicadores` — 3 registros

| Campo | O que significa | Por que usamos |
|---|---|---|
| `investimento` | R$ 2.000.000,00. | Capital aplicado. |
| `wacc` | Taxa de desconto de 14% a.a. | Custo mínimo exigido para o dinheiro. |
| `vpl` | Lucro do projeto em valores de hoje. | Responde se vale a pena. |
| `tir` | Rendimento anual do projeto. | Compara com os 14% exigidos. |
| `payback_simples_anos` | Anos para recuperar o investimento. | Mede o prazo de retorno. |
| `payback_descontado_anos` | Idem, considerando o custo do dinheiro. | Mede o prazo de retorno real. |

### ⚙️ `Premissas` — parâmetros do modelo (Google Sheets)

| Parâmetro | Valor | Por que foi adotado |
|---|---|---|
| Data-base | 31/10/2026 | Último fechamento. O aging e o saldo inicial são medidos nessa data. |
| Saldo inicial de caixa | R$ 1.500.000,00 | Ponto de partida da projeção de caixa. |
| Despesa fixa mensal | R$ 150.000,00 | Custos que existem independentemente das vendas. |
| Impostos sobre faturamento | 8,0% | Tributação sobre a receita. |
| Taxa de desconto (WACC) | 14,0% a.a. (valor ilustrativo) | Retorno mínimo exigido para o dinheiro investido. |
| CAPEX | R$ 2.000.000,00 (Nov/26) | Investimento em expansão. |
| Faturamento base | R$ 862.015,23 | Média de ago. a out./2026, que evita que o pico de out./2026 (R$ 913.186,76) distorça a projeção. |
| Fluxos do projeto (anos 1 a 5) | Ver tabela abaixo | Retorno esperado do investimento, em cada cenário. |

**Fluxos do projeto (R$ mil):**

| Cenário | Ano 1 | Ano 2 | Ano 3 | Ano 4 | Ano 5 |
|---|---:|---:|---:|---:|---:|
| Pessimista | 350 | 450 | 550 | 600 | 650 |
| Base | 600 | 750 | 850 | 900 | 950 |
| Otimista | 800 | 1.000 | 1.200 | 1.300 | 1.400 |

---

## 💼 ${\color{#1F6FEB}\textsf{Contas Financeiras Utilizadas}}$

| Conta | Tipo | Como é calculada | Por que está no modelo |
|---|---|---|---|
| **Faturamento** | Receita (competência) | Faturamento base × (1 + crescimento mensal)<sup>mês</sup> | Mostra quanto a empresa vende. |
| **Recebimentos** | Entrada de caixa | (1 − inadimplência) × soma do faturamento dos meses anteriores × curva de recebimento | Venda não é dinheiro: o caixa só recebe depois. |
| **Custos variáveis** | Saída de caixa | Faturamento × 62% / 58% / 55% | Quanto mais vende, mais gasta para produzir. |
| **Impostos** | Saída de caixa | Faturamento × 8% | Obrigação sobre cada venda. |
| **Despesas fixas** | Saída de caixa | R$ 150.000 × (1 + crescimento)<sup>mês</sup> | Custo que existe mesmo sem vendas. |
| **Fluxo operacional** | Resultado de caixa | Recebimentos − custos variáveis − impostos − despesas fixas | Mostra se o negócio gera caixa por si só. |
| **CAPEX** | Investimento | R$ 2.000.000 no mês 1 | O dinheiro aplicado na expansão. |
| **Entrada do projeto** | Entrada de caixa | Fluxo do ano 1 ÷ 12 | Retorno mensal do investimento. |
| **Saldo de caixa** | Posição financeira | Saldo anterior + fluxo operacional − CAPEX + entrada do projeto | O número mais importante para sobreviver ao primeiro mês. |
| **Contas a receber** | Ativo | Faturas em aberto (R$ 1.934.297,96) | O que os clientes ainda devem. |
| **PDD** | Redutora do ativo | Saldo da faixa × percentual da faixa | Reserva para a perda provável. |

**Premissas por cenário:**

| Premissa | Pessimista | Base | Otimista |
|---|---:|---:|---:|
| Crescimento mensal do faturamento | -0,5% | +0,8% | +1,8% |
| Inadimplência | 6,0% | 3,0% | 1,5% |
| Custo variável (% do faturamento) | 62% | 58% | 55% |
| Crescimento mensal das despesas fixas | 0,8% | 0,4% | 0,3% |
| Atraso extra nos recebimentos | 1 mês | 0 | 0 |

**Por que três cenários?** Ninguém sabe o futuro. Comparar um cenário bom, um esperado e um ruim mostra quanto o resultado pode variar e se o plano aguenta o pior caso.

**Fórmula do saldo de caixa:**

```math
\text{Saldo}_m = \text{Saldo}_{m-1} + \text{Recebimentos}_m - \text{Custos Variáveis}_m - \text{Impostos}_m - \text{Despesas Fixas}_m - \text{CAPEX}_m + \text{Entrada do Projeto}_m
```

Com $`\text{Saldo}_0 = \text{R\$ } 1.500.000{,}00`$.

---

## 📈 ${\color{#1F6FEB}\textsf{Resultados por Cenário}}$

| Indicador | Pessimista | Base | Otimista |
|---|---:|---:|---:|
| **VPL** | -R$ 282.649,94 | R$ 703.414,71 | R$ 1.778.008,22 |
| **TIR** (% a.a.) | 8,5% | 26,7% | 43,1% |
| **Payback simples** | 4,08 anos | 2,76 anos | 2,17 anos |
| **Payback descontado** | Não se paga em 5 anos | 3,61 anos | 2,65 anos |
| **Caixa mínimo** (Nov/26) | -R$ 417.247,18 | -R$ 310.082,32 | -R$ 257.977,01 |
| **Caixa volta a ficar positivo** | Abr/27 | Jan/27 | Jan/27 |
| **Saldo de caixa em Out/27** | R$ 485.391,60 | R$ 1.570.697,81 | R$ 2.399.064,46 |

### 🔍 Por que o caixa fica negativo em Nov/26?

A operação **gera caixa em todos os meses**, nos três cenários. No Cenário Base, são R$ 139.917,68 já em Nov/26. O problema é o investimento:

| Cenário Base, Nov/26 | Valor |
|---|---:|
| Caixa inicial | R$ 1.500.000,00 |
| (+) Caixa gerado pela operação | R$ 139.917,68 |
| (+) Entrada do projeto | R$ 50.000,00 |
| (−) Investimento (CAPEX) | -R$ 2.000.000,00 |
| **(=) Saldo de caixa** | **-R$ 310.082,32** |

Sem o investimento, o caixa nunca ficaria abaixo de R$ 1,55 milhão em nenhum cenário. A coluna `saldo_sem_projeto` mostra isso.

---

## 🔢 ${\color{#1F6FEB}\textsf{Os Indicadores Explicados}}$

### 💰 VPL — Valor Presente Líquido

**Em uma frase:** quanto o projeto deixa de lucro, em valores de hoje, depois de pagar o custo do dinheiro.

```math
\text{VPL} = \sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + \text{WACC})^t} - \text{CAPEX}
```

Onde $`t`$ é o ano (1 a $`n`$), $`n`$ = 5 anos, $`\text{FC}_t`$ é o fluxo de caixa do projeto no ano $`t`$ e o CAPEX é pago em $`t = 0`$. **Por que descontar?** R$ 100 daqui a 3 anos valem menos do que R$ 100 hoje. **Como ler:** VPL positivo significa que o projeto vale mais do que custa.

### 📐 TIR — Taxa Interna de Retorno

**Em uma frase:** quanto o projeto rende por ano, como uma taxa de juros.

```math
\sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + \text{TIR})^t} - \text{CAPEX} = 0
```

**Como ler:** compare com o mínimo exigido (14%). No Base, 26,7% é quase o dobro. Quando a TIR é maior que 14%, o VPL é positivo.

### ⏱️ Payback — prazo de retorno

**Em uma frase:** em quanto tempo o investimento volta para o caixa.

```math
\text{Payback} = t - 1 + \frac{\left| \text{Saldo Acumulado}_{t-1} \right|}{\text{Fluxo}_t}
```

Onde $`t`$ é o primeiro ano em que o saldo acumulado deixa de ser negativo. **Exemplo (Base):** após o ano 2, faltam R$ 650.000 para recuperar. O fluxo do ano 3 é de R$ 850.000, então o payback é 2 + 650.000 ÷ 850.000 = **2,76 anos**. A versão **descontada** aplica o custo do dinheiro e é mais prudente.

### 💧 Caixa mínimo e necessidade de financiamento

**Em uma frase:** o pior saldo de caixa do ano e o valor de crédito que cobre esse buraco. É o número que evita atrasar fornecedores e salários.

### 🛡️ Sensibilidade do VPL

**Em uma frase:** o quanto o lucro muda se as entradas do projeto vierem diferentes do previsto.

```math
\text{VPL}(\Delta, r) = -\text{CAPEX} + (1 + \Delta) \times \sum_{t=1}^{n} \frac{\text{FC}_t}{(1 + r)^t}
```

O CAPEX fica fixo, porque o investimento é pago no início, e a incerteza está nos retornos futuros.

| Variação nas entradas do projeto | VPL (Cenário Base, taxa de 14%) |
|---|---:|
| -30% | -R$ 107.609,71 |
| -20% | R$ 162.731,76 |
| -10% | R$ 433.073,23 |
| Base (0%) | R$ 703.414,71 |
| +10% | R$ 973.756,18 |

A matriz completa (variação das entradas × taxa de desconto de 10% a 18%) está na aba `Viabilidade` do modelo e na [documentação técnica](dashboards_modelos/README.md).

### 🕰️ Aging e PDD

**Aging** é a fotografia das contas a receber por idade do atraso. **PDD** é a reserva para perdas, que cresce com o atraso, porque quanto mais velha a dívida, menor a chance de receber:

| Faixa de atraso | A vencer | 1 a 30 | 31 a 60 | 61 a 90 | 91 a 180 | Acima de 180 |
|---|---:|---:|---:|---:|---:|---:|
| **Percentual de provisão** | 0% | 1% | 5% | 15% | 40% | 80% |

| Faixa de Atraso | Qtd. Faturas | Saldo (R$) | % da Carteira | PDD Estimada (R$) |
|---|---:|---:|---:|---:|
| A vencer | 47 | 1.288.900,65 | 66,6% | 0,00 |
| 1 a 30 dias | 10 | 237.635,67 | 12,3% | 2.376,36 |
| 31 a 60 dias | 4 | 106.169,02 | 5,5% | 5.308,45 |
| 61 a 90 dias | 2 | 37.526,05 | 1,9% | 5.628,91 |
| 91 a 180 dias | 0 | 0,00 | 0,0% | 0,00 |
| Acima de 180 dias | 13 | 264.066,57 | 13,7% | 211.253,26 |
| **Total** | **76** | **1.934.297,96** | **100%** | **224.566,98** |

**Como ler para o dono:**

- Da carteira de R$ 1,93 milhão, estimamos que **R$ 224,6 mil (11,6%)** não serão recebidos, e o valor líquido esperado é de R$ 1.709.730,98.
- **94,1% dessa perda (R$ 211,2 mil)** está em apenas **13 faturas com mais de 180 dias de atraso**.
- Todas as faturas vencidas há mais de 30 dias pertencem aos **5 clientes de atraso grave**. O problema é concentrado, e não espalhado pela carteira.
- **Ação recomendada:** priorizar a cobrança extrajudicial e judicial dessas 13 faturas. Como elas estão provisionadas a 80%, cada real recuperado permite reverter R$ 0,80 de provisão, com efeito positivo no resultado. Para novas vendas a esses clientes, vale rever o prazo e o limite de crédito.

---

## 🖼️ ${\color{#1F6FEB}\textsf{Painel em Imagens}}$

| Evolução do Saldo de Caixa (12 meses) | Sensibilidade do VPL (Cenário Base) |
|:---:|:---:|
| <img width="420" alt="Evolução Projetada do Saldo de Caixa (12 Meses)" src="https://github.com/user-attachments/assets/e03914b1-edc4-4d16-a6c3-9de0ca889d6b" /> | <img width="420" alt="Sensibilidade do VPL por Variação (Cenário Base)" src="https://github.com/user-attachments/assets/dbac539f-1695-4fda-b98e-e113878dc2c5" /> |

<p align="center"><b>Carteira de Recebíveis: Saldo vs. PDD por Faixa</b></p>

<p align="center">
  <img width="600" alt="Carteira de Recebíveis: Saldo vs. PDD por Faixa" src="https://github.com/user-attachments/assets/bbce23bc-37a8-460a-90d7-67c6660bbcc0" />
</p>

---

## 🗂️ ${\color{#1F6FEB}\textsf{Estrutura do Repositório}}$

```text
financial-planning-model-fpa/
│
├── README.md                           <- Apresentação executiva e dicionário apresentação
│
│
├── 📁 data/                            <- Tabelas em CSV e Excel consolidado
│   └── portfolio_financeiro.xlsx
│
├── 📁 notebooks_scripts/               <- Geração dos dados em Python
│   ├── Codigo_python.ipynb             <- Gerador sintético determinístico e validações (SEED 42)
│   └── README.md                       <- Manual de execução do script
│
└── 📁 dashboards_modelos/              <- Modelo financeiro e painel
    ├── Modelo FP&A - Base Tratada.xlsx <- Cálculos e projeções (13 abas)
    ├── Dashboard FP&A - Modelo e Indicadores.xlsx <- Painel executivo (2 abas)
    └── README.md                       <- Manual de fórmulas e metodologia financeira
```

---

## 🛠️ ${\color{#1F6FEB}\textsf{Tecnologias e Ferramentas}}$

- **Google Sheets / Excel:** conciliação entre competência e caixa, régua de PDD, projeção operacional e de caixa por cenário, análise de viabilidade e sensibilidade.
- **Python 3.13:** geração de dados sintéticos com `pandas` e `numpy`, e cálculo financeiro com `numpy-financial`.
- **Painel executivo:** visual limpo, focado nos indicadores de decisão e nos gráficos de apoio à diretoria.

---

> ⚠️ **Nota de transparência:** todos os dados cadastrais e transacionais deste repositório são fictícios (`dados_ficticios = True`), gerados por algoritmo com semente fixa (`SEED = 42`) para preservar a confidencialidade e para fins estritamente demonstrativos de modelagem FP&A.
