# 🧮 Documentação Técnica e Manual de Cálculos — Modelagem FP&A

Documentação técnica detalhada das metodologias de cálculo financeiro, arquitetura em camadas e premissas aplicadas aos modelos da pasta `dashboards_modelos/`.

---

## 🏛️ Arquitetura da Solução: Fluxo de Dados e Modelagem Financeira

A solução foi estruturada em etapas, utilizando Python para geração e preparação dos dados fictícios e Google Sheets para modelagem financeira, cálculos analíticos e visualização gerencial. Essa arquitetura permite separar a geração dos dados da camada de análise e apresentação dos resultados.

```text
┌────────────────────────────────────────────────────────────┐
│                  PYTHON                                  │
│          Geração de Dados Fictícios                      │
│  • Base de clientes                                     │
│  • Base de faturas                                      │
│  • Dados operacionais e financeiros                     │
└────────────────────────────┬───────────────────────────────┘
                             │
                             │ Exportação das Bases
                             ▼
┌────────────────────────────────────────────────────────────┐
│                  GOOGLE SHEETS                           │
│             Modelo FP&A - Base Tratada                  │
│              (Camada de Modelagem)                      │
│  • Premissas e cenários                                 │
│  • DRE projetada                                         │
│  • Fluxo de caixa                                        │
│  • Aging e PDD                                           │
│  • Viabilidade financeira                                 │
│  • Sensibilidade                                         │
│  • Resumo de liquidez                                    │
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

### 1. `Modelo FP&A - Base Tratada.xlsx` — Motor Backend

**Objetivo:** Concentrar toda a inteligência de negócios, projeções operacionais, réguas de cobrança, demonstrativos financeiros e cálculos de viabilidade.

**Estrutura — 13 Abas:**

| Aba | Descrição |
|---|---|
| `Premissas` | Parâmetros de WACC, alíquotas de impostos, CAPEX e premissas de crescimento por cenário. |
| `Base_Clientes` | Cadastro e atribuição dos perfis de pagamento dos clientes. |
| `Base_Faturas` | Emissões, vencimentos e liquidação das faturas. |
| `Curva_Recebimento` | Matriz de distribuição percentual dos recebimentos por lag temporal ($k$ meses após emissão). |
| `Aging` | Classificação temporal de recebíveis em aberto e cálculo da Provisão para Devedores Duvidosos (PDD). |
| `Fluxo_Historico` | Conciliação de faturamento e recebimentos passados. |
| `DRE_Pessimista` | Demonstrativo de Resultados projetado para 12 meses no cenário pessimista. |
| `DRE_Base` | Demonstrativo de Resultados projetado para 12 meses no cenário base. |
| `DRE_Otimista` | Demonstrativo de Resultados projetado para 12 meses no cenário otimista. |
| `Fluxo_Caixa` | Projeção de fluxo de caixa mensal operacional, CAPEX e saldos finais acumulados. |
| `Viabilidade` | Matriz de investimento, cálculo de VPL, TIR e Paybacks por cenário. |
| `Sensibilidade` | Matriz bidimensional de variação do VPL conforme alteração do WACC e variação nos fluxos de caixa. |
| `Resumo_Liquidez` | Tabela consolidada com caixa mínimo, mês crítico e necessidade de financiamento. |

### 2. `Dashboard FP&A - Modelo e Indicadores.xlsx` — Camada Visual / Frontend

**Objetivo:** Fornecer um painel limpo, sem poluição de linhas de grade ou cabeçalhos complexos, focado na alta gestão.

**Estrutura — 2 Abas:**

| Aba | Descrição |
|---|---|
| `Base de Dados` | Aba oculta/de suporte que consolida as séries necessárias extraídas diretamente do modelo backend. |
| `Dashboard Executivo` | Painel principal contendo KPIs estratégicos (VPL, TIR e Payback), gráficos de tendência do fluxo de caixa de 12 meses (Nov/26 a Out/27) e distribuição do Aging. |

---

## 📐 Metodologia dos Cálculos Financeiros

### 📌 1. Taxa Média Ponderada de Capital (WACC)

Adotou-se uma taxa de desconto de **14,0% a.a.** para a análise de viabilidade dos projetos, refletindo a Taxa Mínima de Atratividade (TMA) ajustada ao risco do projeto.

$$
\text{WACC} = 14,0\% \text{ a.a.}
$$

### 📌 2. Valor Presente Líquido (VPL)

O VPL mede o valor presente dos fluxos de caixa futuros descontados à taxa WACC, subtraído do investimento inicial ($\text{CAPEX} = \text{R\$ } 2.000.000,00$).

$$
\text{VPL} =
\sum_{t=1}^{n}
\frac{\text{FC}_t}{(1+\text{WACC})^t}
-
\text{CAPEX}
$$

**Resultados por cenário — WACC de 14% a.a.:**

| Cenário | VPL |
|---|---:|
| Pessimista | -R$ 282.649,94 |
| Base | R$ 703.414,71 |
| Otimista | R$ 1.778.008,23 |

### 📌 3. Taxa Interna de Retorno (TIR)

A TIR representa a taxa de desconto que iguala o VPL do projeto a zero.

$$
\sum_{t=1}^{n}
\frac{\text{FC}_t}{(1+\text{TIR})^t}
-
\text{CAPEX}
=
0
$$

**Resultados por cenário:**

| Cenário | TIR |
|---|---:|
| Pessimista | 8,5% |
| Base | 26,7% |
| Otimista | 43,1% |

### 📌 4. Payback Simples e Descontado

**Payback Simples:** Tempo necessário para recuperar o investimento inicial sem considerar o valor do dinheiro no tempo.

**Payback Descontado:** Tempo necessário para que a soma dos fluxos descontados a 14% a.a. iguale o investimento inicial.

$$
\text{Payback Descontado}
=
t-1+
\frac{
\left|\text{Saldo Acumulado Descontado}_{t-1}\right|
}{
\text{Fluxo Descontado}_t
}
$$

**Nota:** Para o cenário pessimista, o Payback Descontado exibe a classificação *"Não se paga em 5 anos"*, pois os fluxos gerados no período de 5 anos, descontados a 14% a.a., não são suficientes para amortizar o investimento inicial de R$ 2,0 milhões.

### 📌 5. Projeção de Fluxo de Caixa (12 Meses — Nov/26 a Out/27)

O saldo de caixa acumulado ao final do mês $m$ é calculado adicionando o fluxo operacional e de investimento ao saldo inicial do mês anterior.

$$
\begin{aligned}
\text{Saldo Final}_m =\;&
\text{Saldo Inicial}_m
+\text{Recebimentos}_m\\
&-\text{Custos Variáveis}_m
-\text{Impostos}_m\\
&-\text{Despesas Fixas}_m
-\text{CAPEX}_m
\end{aligned}
$$

**Interpretação da linha "Saldo Final 12M":**

O valor informado no resumo do final do período (Out/27) não representa a soma dos 12 meses, mas sim o **saldo acumulado de caixa no encerramento da projeção**.

| Cenário | Saldo Final em Out/27 |
|---|---:|
| Pessimista | R$ 485.391,60 |
| Base | R$ 1.570.697,81 |
| Otimista | R$ 2.399.064,46 |

### 📌 6. Aging de Recebíveis e PDD Estimada

A carteira de títulos em aberto é agrupada por faixas de atraso em relação ao vencimento, aplicando-se alíquotas progressivas de provisão para devedores duvidosos (PDD).

| Faixa de Atraso | Alíquota PDD | Saldo (R$) | PDD Estimada (R$) |
|---|---:|---:|---:|
| A vencer | 0,0% | 1.288.900,65 | 0,00 |
| 1 a 30 dias | 1,0% | 237.635,67 | 2.376,36 |
| 31 a 60 dias | 5,0% | 106.169,02 | 5.308,45 |
| 61 a 90 dias | 15,0% | 37.526,05 | 5.628,91 |
| 91 a 180 dias | 40,0% | 0,00 | 0,00 |
| > 180 dias | 80,0% | 264.066,57 | 211.253,26 |
| **Total Geral** | **11,6% (média)** | **1.934.297,96** | **224.566,98** |

---

## 🛠️ Recomendações de Manutenção

### Sincronização entre Arquivos

Ao atualizar premissas no modelo backend (`Modelo FP&A - Base Tratada.xlsx`), lembre-se de:

- Recopiar os dados consolidados para o arquivo frontend; ou
- Manter o vínculo com a aba `Base de Dados` do arquivo `Dashboard FP&A - Modelo e Indicadores.xlsx`.

### Formatação de Séries

Garantir que o eixo temporal dos gráficos mantenha o formato `mmm/aa`, com início em `Nov/26` e encerramento em `Out/27`.

---

## 🎯 Objetivo da Documentação

Esta documentação tem como finalidade:

- Garantir transparência metodológica dos cálculos financeiros.
- Facilitar a rastreabilidade das premissas e dos resultados.
- Documentar a arquitetura dos modelos e a integração entre arquivos.
- Apoiar a manutenção e atualização das projeções.
- Demonstrar a aplicação prática de conceitos de FP&A, análise financeira e modelagem de cenários.
