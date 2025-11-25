# Data Challenge RNP - 2025
Desafio de dados para prever se ocorreu uma mudança de rota ao comparar com a medição anterior para o mesmo par de origem e destino.

## Como executar

Para executar os notebook é necessário alterar a variável `DRIVE_PATH` para apontar para onde estão os arquivos `train.csv` e `test.csv`.

## Notebooks

Existe um Jupyter Notebook para cada etapa:

1. [Feature Engineering](notebooks/1-feature_engineering.ipynb)
2. [Data Profiling](notebooks/2-data_profiling.ipynb)
3. [Treinamento do Modelo](notebooks/3-catboost.ipynb)


## Sobre o dataset

Dados originais processados: 10,000,000 linhas.

10 Colunas existentes no dataset.

| Coluna                   | Descrição                                                                 |
|--------------------------|---------------------------------------------------------------------------|
| tr_id                    | Identificador único do traceroute                                         |
| tr_src                   | Identificador único de máquina de origem                                  |
| tr_dst                   | Identificador único de máquina de destino (máquina do m-lab)              |
| all_rtts                 | Lista de valores em ms do RTT de cada pacote no último hop                |
| tr_attempts              | Número de tentativas feitas por probe para alcançar o último hop           |
| total_probes_sent        | Total de pacotes enviados para o último hop                                |
| total_replies_last_hop   | Número de respostas recebidas do último hop                                |
| route_changed            | Indicador se houve alteração de rota (1: sim, 0: não)                      |
| date_index               | Índice da data (1 = primeiro dia, 2 = segundo, etc.)                       |
| seconds_since_start      | Tempo desde a primeira medição do dataset, contado em segundos             |


## Análise Exploratório dos dados

Total de registros: 10,000,000
Pares origem-destino únicos: 109

Mudanças de rota: 205,935 (2.06%)
Sem mudanças: 9,794,065 (97.94%)
scale_pos_weight = 47.56

RTT médio: 124.56 ± 78.10 ms

Processamento concluído!
Dataset final: (10000000, 11)
Features disponíveis: 11

Features criadas:
  - rtt_mean
  - rtt_std
  - rtt_range
  - rtt_count
  - time_since_last
  - reply_ratio
  - probe_efficiency
  - pair_id

## Modelo

### Hiperparâmetros CatBoost com GPU

| Hiperparâmetro         | Valor                                    |
|-----------------------|-------------------------------------------|
| loss_function         | Logloss                                   |
| eval_metric           | TotalF1:average=Macro;use_weights=False   |
| depth                 | 6                                         |
| iterations            | 5000                                      |
| random_seed           | 42                                        |
| verbose               | 100                                       |
| scale_pos_weight      | 47.56                                     |
| task_type             | GPU                                       |
| devices               | 0                                         |

**Resultados do modelo CatBoost:**

- Melhor F1 Macro obtido: **0.8626**
- Melhor iteração: 4887
- O modelo foi ajustado para manter apenas as primeiras 4888 iterações, garantindo melhor desempenho e evitando overfitting.