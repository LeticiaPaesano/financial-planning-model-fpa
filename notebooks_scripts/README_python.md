🐍 Geração de Dados Fictícios — Código Python

O código desta pasta (notebooks_scripts/) é a primeira camada da solução FP&A: ele gera a base de dados que alimenta o modelo financeiro em Google Sheets (Modelo FP&A - Base Tratada). Os cálculos e a metodologia estão documentados no README da pasta dashboards_modelos/.

🎯 Por que este código existe
Confidencialidade: dados financeiros reais de clientes e faturas não podem ser publicados. Todos os dados são sintéticos, e cada tabela traz a coluna dados_ficticios = True.
Reprodutibilidade: a semente fixa (SEED = 42) garante que qualquer pessoa gere exatamente a mesma base e chegue aos mesmos resultados do modelo.
Realismo: a base reproduz comportamentos típicos de uma carteira de recebíveis (clientes pontuais, atrasados e inadimplentes), o que permite testar aging, PDD e projeção de caixa de forma significativa.
Validação cruzada: o código também calcula VPL, TIR, Payback, liquidez e aging em Python. Esses resultados conferem com os do modelo em planilha, o que valida as fórmulas.
⚙️ O que o código faz
Etapa	Descrição
Clientes	40 clientes com perfil de pagamento: 24 pontuais, 11 com atraso leve e 5 com atraso grave.
Faturas	Emissões mensais com vencimento, pagamento e inadimplência (10% das faturas dos clientes de atraso grave não são pagas).
Histórico	Faturamento e recebimentos mensais, agregados a partir das faturas.
Curva de recebimento	Percentual recebido $k$ meses após a emissão, calibrado nas faturas pagas. Usa apenas faturas com mais de 180 dias, para reduzir o viés de faturas recentes ainda não pagas.
Aging e PDD	Faixas de atraso em relação à data-base (31/10/2026) com alíquotas progressivas de provisão.
Fluxo projetado	12 meses em 3 cenários (Pessimista, Base e Otimista), com CAPEX, entrada do projeto e saldo de caixa.
Viabilidade	VPL, TIR, Payback Simples e Descontado com numpy-financial.
🛡️ Verificações de sanidade (assert)

O código interrompe a execução se alguma regra for violada: a curva soma 100%, o vencimento é posterior à emissão, nenhum pagamento ocorre antes da emissão ou depois da data-base, e o histórico não tem "partida a frio".

📦 Saídas

Os arquivos são gravados na pasta data/, criada automaticamente ao lado do notebook:

9 arquivos .csv: clientes, faturas, fluxo_historico, curva_recebimento, aging_detalhe, aging_resumo, fluxo_projetado, resumo_liquidez e projeto_indicadores;
1 arquivo portfolio_financeiro.xlsx, com uma aba por tabela, usado como base para o modelo em Google Sheets.
▶️ Como executar

O notebook foi desenvolvido no Google Colab. Basta executar as células em ordem: a primeira instala o numpy-financial e importa as bibliotecas, e a segunda gera as tabelas e imprime um resumo dos resultados.

Para rodar localmente:

bash
pip install numpy numpy-financial pandas openpyxl

No Colab, a pasta data/ fica no ambiente temporário da sessão. Baixe os arquivos antes de encerrá-la.

As premissas (WACC, CAPEX, cenários, curva de atraso e régua de PDD) ficam na seção de parâmetros, no topo do código, e podem ser alteradas para simular outros contextos.

⚠️ Aviso: todos os dados são sintéticos e não representam nenhuma empresa ou pessoa real.
