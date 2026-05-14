# 📊 Modelo Preditivo de Inadimplência — Datarisk Case DS Júnior

Solução para o case técnico de Cientista de Dados Júnior da Datarisk.
O objetivo é prever a probabilidade de inadimplência de cobranças mensais
feitas a clientes, definida como pagamento realizado com **5 ou mais dias de atraso**.

---

## 🗂️ Estrutura do Projeto

```
├── data/
│   ├── base_cadastral.csv
│   ├── base_info.csv
│   ├── base_pagamentos_desenvolvimento.csv
│   └── base_pagamentos_teste.csv
├── datarisk_case_ds_junior.ipynb   # Notebook principal
├── submissao_case.csv              # Previsões geradas
├── requirements.txt                # Dependências
└── README.md
```

---

## 🔄 Pipeline

```
EDA → Construção do Target → Feature Engineering →
Split Out-of-Time → Preparação do Teste → Modelagem →
Avaliação → Inteligência de Negócio → Submissão
```

---

## ▶️ Como Reproduzir

**1. Instalar dependências**
```bash
pip install -r requirements.txt
```

**2. Abrir o notebook**
```bash
jupyter notebook datarisk_case_ds_junior.ipynb
```

**3. Executar todas as células de cima para baixo**

O arquivo `submissao_case.csv` será gerado automaticamente ao final.

> Os dados são carregados diretamente do GitHub — nenhum CSV precisa
> estar na pasta local.

---

## 📁 Dados

| Base | Descrição | Linhas |
|---|---|---|
| `base_cadastral.csv` | Perfil cadastral dos clientes | 1.315 |
| `base_info.csv` | Renda e funcionários por mês | 24.401 |
| `base_pagamentos_desenvolvimento.csv` | Histórico de cobranças com pagamentos | 77.414 |
| `base_pagamentos_teste.csv` | Cobranças recentes sem pagamento | 12.275 |

---

## 🛠️ Features Criadas (29 no total)

| Grupo | Features |
|---|---|
| Cobrança atual | TAXA, log_valor, prazo_dias_adj, mes_vencimento, dia_vencimento, safra_int |
| Anomalias | flag_prazo_negativo, flag_prazo_longo, flag_valor_nulo |
| Histórico do cliente | hist_tx_inad, hist_inad_ult3m, hist_inad_ult6m, hist_atraso_medio, hist_atraso_max, hist_meses_desde_inad, hist_n_inad, hist_n_safras |
| Cadastral | PORTE, SEGMENTO_INDUSTRIAL, DDD, CEP_2_DIG, DOMINIO_EMAIL, is_pf, tempo_relacionamento |
| Info mensal | log_renda, log_func, flag_renda_nula, flag_func_nula, ratio_valor_renda |

---

## 🤖 Modelo

**Algoritmo:** LightGBM — padrão da indústria para scoring de crédito.

**Validação:** Out-of-Time (OOT)
- Treino: 2018-08 → 2021-03 (70.012 registros)
- Validação: 2021-04 → 2021-06 (7.402 registros)
- Teste: 2021-07 → 2021-11 (12.275 registros)

---

## 📈 Resultados

| Métrica | Valor |
|---|---|
| AUC-ROC (validação OOT) | **0.9542** |
| KS Statistic | **0.7877** |
| Average Precision | **0.6670** |

**Curva de Ganho:**
- Abordando **10%** da base → captura **77.3%** dos inadimplentes
- Abordando **20%** da base → captura **92.9%** dos inadimplentes

---

## ⚠️ Decisões Técnicas Importantes

- **Anti-leakage:** features históricas calculadas com `shift(1)` —
  apenas safras anteriores à safra de referência
- **Split temporal:** nunca aleatório em séries financeiras
- **Nulos como sinal:** `hist_meses_desde_inad` NaN significa "nunca inadimpliu",
  não dado ausente
- **Flags de anomalia:** prazos negativos (96% inad.) e longos (44% inad.)
  preservados como features em vez de removidos

---

## 📦 Dependências

```
pandas==3.0.2
numpy==2.2.5
lightgbm==4.6.0
scikit-learn==1.8.0
scipy==1.15.3
matplotlib==3.10.1
seaborn==0.13.2
```

---

## 📄 Entregáveis

- `submissao_case.csv` — probabilidades de inadimplência para 12.275 cobranças
- `datarisk_case_ds_junior.ipynb` — notebook reprodutível com toda a solução
- `requirements.txt` — dependências com versões fixadas
