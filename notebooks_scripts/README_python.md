# 🐍 Geração e Preparação de Dados Fictícios em Python

Documentação técnica do notebook Python (`Codigo_python.ipynb`), responsável pela primeira camada da solução de FP&A: a simulação de dados sintéticos, o cálculo de indicadores de referência e a consolidação da base que alimenta o modelo financeiro no Google Sheets.

> **Nota de Metodologia:** Os cálculos e premissas financeiras aplicados (WACC, VPL, TIR, Payback e réguas de PDD) estão detalhados na documentação da pasta [`dashboards_modelos/`](https://github.com/LeticiaPaesano/financial-planning-model-fpa/tree/main/dashboards_modelos).

---

## 🎯 Por Que Este Código Existe

* **🔒 Confidencialidade:** Dados financeiros reais de empresas, clientes e faturas são protegidos por sigilo. Por isso, toda a base foi gerada de forma sintética. Cada tabela exportada contém a tag rastreável `dados_ficticios = True`.
* **🔄 Reprodutibilidade (Determinismo):** A utilização de uma semente aleatória fixa (`SEED = 42`) garante que qualquer pessoa ao rodar o notebook obtenha exatamente os mesmos dados e os mesmos resultados finais.
* **📈 Realismo de Carteira:** A geração simula comportamentos típicos de uma carteira de recebíveis, como perfis de clientes (pontuais, atrasos leves e inadimplência grave), defasagem entre emissão e recebimento (curva de recebimento) e envelhecimento de faturas (Aging).
* **⚖️ Validação Cruzada (Cross-Validation):** O código calcula VPL, TIR, Payback, saldo de caixa e aging em Python (`numpy-financial` e `pandas`), permitindo conferir os resultados das fórmulas implementadas no Google Sheets.

---

## ⚙️ Arquitetura do Script e Etapas de Execução

| Etapa | Descrição Técnica |
| :--- | :--- |
| **1. Clientes** | Geração de 40 clientes distribuídos em 3 perfis de pagamento: 24 pontuais, 11 com atraso leve e 5 com atraso grave. |
| **2. Faturas** | Emissões mensais com cálculo automático de vencimento, pagamento efetivo e inadimplência (10% de chance de calote para faturas de clientes com atraso grave). |
| **3. Histórico** | Consolidação mensal de faturamento e recebimentos realizados no período anterior (Nov/24 a Out/26). |
| **4. Curva de Recebimento** | Matriz percentual de recebimento $`k`$ meses após a emissão. Considera apenas faturas emitidas há mais de 180 dias, para reduzir o viés de censura (faturas recentes ainda não tiveram tempo de ser pagas). |
| **5. Aging e PDD** | Classificação das faturas em aberto por faixas de atraso em relação à data-base (`31/10/2026`) e aplicação de alíquotas progressivas de PDD. |
| **6. Fluxo Projetado** | Projeção de 12 meses (Nov/26 a Out/27) sob 3 cenários (Pessimista, Base e Otimista), considerando CAPEX e a entrada do projeto (fluxo do ano 1 diluído em 12 parcelas). |
| **7. Viabilidade** | Cálculo automatizado de VPL, TIR, Payback Simples e Payback Descontado utilizando a biblioteca `numpy-financial`. |

---

## 🛡️ Verificações de Sanidade (`assert`)

O código conta com testes de integridade estrutural que interrompem a execução se alguma premissa for violada:

1. **Curva de Recebimento:** `assert abs(curva.sum() - 1) < 1e-9` (A soma das frações deve ser exatamente 100%).
2. **Cronologia:** `assert (faturas["data_vencimento"] > faturas["data_emissao"]).all()` (Vencimento sempre posterior à emissão).
3. **Data-base:** `assert (pagas["data_pagamento"] <= DATA_REF).all()` (Nenhum pagamento registrado no futuro).
4. **Distribuição:** Validação da quantidade exata de perfis de risco e ausência de *partida a frio* (*cold start*) no histórico.

---

## 📦 Saídas Geradas (Pasta `data/`)

Ao executar o notebook, a pasta `data/` é criada automaticamente no diretório de execução, contendo:

* **9 Arquivos CSV (`.csv`):** `clientes`, `faturas`, `fluxo_historico`, `curva_recebimento`, `aging_detalhe`, `aging_resumo`, `fluxo_projetado`, `resumo_liquidez` e `projeto_indicadores`.
* **1 Planilha Consolidada (`portfolio_financeiro.xlsx`):** Contendo uma aba para cada tabela exportada, utilizada como insumo principal para carga no Google Sheets.

---

## ▶️ Como Executar

Você pode interagir com o projeto de diferentes maneiras, dependendo do seu objetivo:

Opção 1: Baixar os Dados Prontos (Acesso Direto)
Se o seu objetivo é apenas visualizar os arquivos gerados ou alimentar outras ferramentas sem rodar o código, você pode acessar e baixar diretamente os arquivos prontos na pasta [data/ do repositório no GitHub]( https://github.com/LeticiaPaesano/financial-planning-model-fpa/tree/main/data).

### Opção 2: Google Colab (Recomendado)
1. Abra o arquivo `Codigo_python.ipynb` no Google Colab.
2. Execute todas as células em sequência (`Ctrl + F9`).
3. Baixe os arquivos gerados na aba lateral de arquivos (pasta `data/`) antes de encerrar a sessão.

### Opção 3: Ambiente Local
O notebook foi executado em Python 3.13 (Google Colab). No terminal:

```bash
pip install numpy numpy-financial pandas openpyxl jupyter
jupyter notebook Codigo_python.ipynb
```

> ⚠️ **Aviso:** todos os dados são sintéticos e não representam nenhuma empresa ou pessoa real.
