# Guia do Notebook `Cox.ipynb`

## Objetivo

O notebook [`Cox.ipynb`](./Cox.ipynb) realiza a modelagem estatística da sobrevida de pacientes com câncer colorretal por meio do modelo de riscos proporcionais de Cox, dando continuidade aos artefatos gerados no notebook de pré-processamento.

## Entradas e saídas

### Entradas principais
- [`train_data_2025.csv`](../data/processed/train_data_2025.csv)
- [`test_data_2025.csv`](../data/processed/test_data_2025.csv)
- [`dataset_metadata_2025.json`](../data/processed/dataset_metadata_2025.json)

### Saídas principais
- Modelo de Cox ajustado em memória
- Tabelas de métricas de desempenho
- Tabelas de predição
- Gráficos diagnósticos e de avaliação

## Visão geral do pipeline

<table>
  <tr>
    <th>Etapa</th>
    <th>O que acontece</th>
    <th>Saída principal</th>
  </tr>
  <tr>
    <td><strong>1. Entrada</strong></td>
    <td>Leitura dos conjuntos de treino, teste e dos metadados gerados no pré-processamento.</td>
    <td>DataFrames de treino/teste e dicionário de metadados</td>
  </tr>
  <tr>
    <td><strong>2. Preparação</strong></td>
    <td>Definição das variáveis de sobrevivência, preditoras, estratos e verificações de consistência.</td>
    <td>Base pronta para codificação</td>
  </tr>
  <tr>
    <td><strong>3. Codificação</strong></td>
    <td>Aplicação de <code>OrdinalEncoder</code> e <code>OneHotEncoder</code> com ajuste apenas no treino.</td>
    <td>Bases codificadas para treino e teste</td>
  </tr>
  <tr>
    <td><strong>4. Ajuste do modelo</strong></td>
    <td>Treinamento do <code>CoxPHFitter</code> com estratificação em variáveis clínicas selecionadas.</td>
    <td>Modelo de Cox ajustado</td>
  </tr>
  <tr>
    <td><strong>5. Validação</strong></td>
    <td>Avaliação da calibração, teste de proporcionalidade de Schoenfeld, Kaplan-Meier por grupo, log(-log) e resíduos de Martingale.</td>
    <td>Diagnósticos do modelo</td>
  </tr>
  <tr>
    <td><strong>6. Predição e métricas</strong></td>
    <td>Cálculo de tempos previstos, resíduos, C-Index, C-Index IPCW, IBS e métricas de erro.</td>
    <td>Tabelas de desempenho</td>
  </tr>
  <tr>
    <td><strong>7. Visualizações</strong></td>
    <td>Geração de gráficos de tempo real vs previsto, distribuição de resíduos, Kaplan-Meier geral e distribuição do tempo de sobrevida.</td>
    <td>Gráficos finais do modelo</td>
  </tr>
</table>

## Fluxo resumido

**Entrada** ➤ **Preparação** ➤ **Codificação** ➤ **Ajuste do modelo** ➤ **Validação** ➤ **Predição e métricas** ➤ **Visualizações**

## Etapas detalhadas

### 1. Entrada
- Leitura de `train_data_2025.csv`.
- Leitura de `test_data_2025.csv`.
- Leitura de `dataset_metadata_2025.json`.

### 2. Preparação
- Definição de `time` e `event` como colunas de sobrevivência.
- Definição das colunas preditoras.
- Definição das variáveis de estratificação.
- Verificação de valores ausentes.
- Verificação de colunas categóricas.
- Inspeção dos limites e da taxa de evento.

### 3. Codificação
- Codificação ordinal de `ECGRUP`.
- Codificação one-hot de `TOPO` e `MORFO_CAT`.
- Ajuste dos encoders apenas no conjunto de treino.
- Aplicação dos mesmos encoders no conjunto de teste.
- Comparação entre a estrutura original e a codificada.

### 4. Ajuste do modelo
- Instanciação do `CoxPHFitter` com penalização baixa.
- Ajuste do modelo com `time` como duração e `event` como evento.
- Uso de estratificação em `DIAGPREV`, `TRATCONS_CAT` e `CONSDIAG_CAT`.
- Exibição do sumário do modelo ajustado.

### 5. Validação
- Avaliação visual da calibração em 60 meses.
- Aplicação do teste de proporcionalidade de Schoenfeld.
- Análise exploratória por curvas de Kaplan-Meier e teste global de log-rank.
- Inspeção visual com gráficos log(-log(S(t))).
- Verificação de linearidade com resíduos de Martingale.

### 6. Predição e métricas
- Predição do tempo esperado de sobrevivência para treino e teste.
- Cálculo de resíduos entre tempo observado e previsto.
- Resumo estatístico das predições.
- Cálculo de C-Index.
- Cálculo de C-Index IPCW.
- Cálculo de IBS.
- Cálculo de MSE, RMSE e MAE.

### 7. Visualizações
- Gráfico de tempo real vs previsto.
- Histograma da distribuição dos resíduos.
- Curva de Kaplan-Meier geral para treino e teste.
- Histograma da distribuição do tempo de sobrevida.

## Estrutura do notebook

1. Configuração inicial  
2. Carregamento dos dados tratados  
3. Preparação para modelagem  
4. Codificação das variáveis  
5. Ajuste do modelo de Cox  
6. Validação do modelo  
7. Predição e métricas  
8. Visualizações do modelo

## Como executar

1. Abra [`Cox.ipynb`](./Cox.ipynb).
2. Certifique-se de que os artefatos do pré-processamento já foram gerados.
3. Execute as células na ordem em que aparecem.
4. Verifique as saídas intermediárias de codificação, ajuste, validação e métricas.
5. Confirme, ao final, os gráficos diagnósticos e as tabelas de desempenho.

## Uso prático

Este guia serve para:
- consultar rapidamente o papel do notebook no projeto;
- entender o fluxo completo da modelagem de Cox sem abrir todas as células;
- visualizar as etapas de preparação, ajuste e validação do modelo de forma compacta;
- verificar quais entradas são necessárias e quais resultados analíticos são produzidos.
