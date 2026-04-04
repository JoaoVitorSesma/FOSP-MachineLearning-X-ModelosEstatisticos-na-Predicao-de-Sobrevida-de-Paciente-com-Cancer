# Guia do Notebook `Pre_Processamento_dos_Dados.ipynb`

## Objetivo

O notebook [`Pre_Processamento_dos_Dados.ipynb`](../processing/Pre_Processamento_dos_Dados.ipynb) realiza o pré-processamento da base bruta [`pacigeral_2025.csv`](../complete/pacigeral_2025.csv) para gerar uma base pronta para modelagem de machine learning e análises de sobrevivência.

## Entradas e saídas

### Entrada principal
- [`pacigeral_2025.csv`](../complete/pacigeral_2025.csv)

### Artefatos gerados
- [`Colorretal_tratado_2025.csv`](../processed/Colorretal_tratado_2025.csv)
- [`train_data_2025.csv`](../processed/train_data_2025.csv)
- [`test_data_2025.csv`](../processed/test_data_2025.csv)
- [`dataset_metadata_2025.json`](../processed/dataset_metadata_2025.json)

## Visão geral do pipeline

<table>
  <tr>
    <th>Etapa</th>
    <th>O que acontece</th>
    <th>Saída principal</th>
  </tr>
  <tr>
    <td><strong>1. Entrada</strong></td>
    <td>Leitura da base bruta <code>pacigeral_2025.csv</code>.</td>
    <td>DataFrame original</td>
  </tr>
  <tr>
    <td><strong>2. Preparação da coorte</strong></td>
    <td>Aplicação dos filtros clínicos e demográficos da população de interesse.</td>
    <td>Base filtrada</td>
  </tr>
  <tr>
    <td><strong>3. Transformações</strong></td>
    <td>Extração de <code>nDRS</code>, conversão de datas, recálculo de intervalos, criação de categorias, ajuste de <code>HABILIT</code> e <code>MORFO_CAT</code>.</td>
    <td>Base tratada</td>
  </tr>
  <tr>
    <td><strong>4. Sobrevivência</strong></td>
    <td>Criação de <code>time</code> e <code>event</code>, com censura administrativa acima de 60 meses.</td>
    <td>Base de sobrevivência</td>
  </tr>
  <tr>
    <td><strong>5. Verificações</strong></td>
    <td>Análise de <code>time</code>, <code>event</code>, valores ausentes e tipos de dados.</td>
    <td>Base validada</td>
  </tr>
  <tr>
    <td><strong>6. Divisão treino/teste</strong></td>
    <td>Separação estratificada em 80/20.</td>
    <td><code>train_data_2025.csv</code> e <code>test_data_2025.csv</code></td>
  </tr>
  <tr>
    <td><strong>7. Salvamento</strong></td>
    <td>Exportação da base tratada, dos conjuntos de treino/teste e dos metadados.</td>
    <td>Arquivos finais do pipeline</td>
  </tr>
</table>

## Fluxo resumido

**Entrada** ➤ **Preparação da coorte** ➤ **Transformações** ➤ **Sobrevivência** ➤ **Verificações** ➤ **Treino/teste** ➤ **Salvamento**

## Etapas detalhadas

### 1. Entrada
- Leitura do arquivo `pacigeral_2025.csv`.

### 2. Preparação da coorte
- Filtro de topografia colorretal (`C18`, `C19`, `C20`).
- Restrição para residentes do estado de SP.
- Manutenção apenas de casos com confirmação microscópica.
- Restrição de `ECGRUP` para `I`, `II`, `III` e `IV`.
- Restrição de `ANODIAG <= 2019`.
- Restrição de `IDADE > 19`.
- Restrição de `CATEATEND` em `2` ou `9`.

### 3. Transformações
- Extração do número da DRS em `nDRS`.
- Conversão de `DTCONSULT`, `DTDIAG`, `DTTRAT` e `DTULTINFO` para `datetime`.
- Recalculo de `CONSDIAG`, `TRATCONS`, `DIAGTRAT` e `ULTIDIAG`.
- Preenchimento de faltantes específicos com `-1`.
- Criação de `CONSDIAG_CAT`, `TRATCONS_CAT` e `DIAGTRAT_CAT`.
- Reagrupamento de `HABILIT`.
- Criação de `MORFO_CAT`.
- Remoção das colunas fora da base final.

### 4. Sobrevivência
- Remoção de linhas com `ULTIDIAG < 0`.
- Conversão de `ULTIDIAG` para meses em `time`.
- Ajuste de `time == 0` para `1`.
- Criação de `event` a partir de `ULTINFO`.
- Aplicação de censura administrativa acima de 60 meses.

### 5. Verificações
- Inspeção de `event`.
- Inspeção de `time`.
- Verificação de valores ausentes.
- Verificação dos tipos de dados.
- Identificação das colunas categóricas.

### 6. Divisão treino/teste
- Separação estratificada em 80/20 com base em `event`.

### 7. Salvamento
- Salvamento de `Colorretal_tratado_2025.csv`.
- Salvamento de `train_data_2025.csv`.
- Salvamento de `test_data_2025.csv`.
- Salvamento de `dataset_metadata_2025.json`.

## Estrutura do notebook

1. Configuração inicial  
2. Tratamento dos dados  
3. Variáveis de sobrevivência  
4. Salvamento da base tratada  
5. Preparação final para modelagem  
6. Divisão treino/teste  
7. Salvamento de datasets e metadados

## Como executar

1. Abra [`Pre_Processamento_dos_Dados.ipynb`](../processing/Pre_Processamento_dos_Dados.ipynb).
2. Execute as células na ordem em que aparecem.
3. Verifique as saídas intermediárias de inspeção de colunas, evento e tempo.
4. Confirme, ao final, o salvamento da base tratada, dos conjuntos de treino/teste e dos metadados.

## Uso prático

Este guia serve para:
- consultar rapidamente o papel do notebook no projeto;
- entender o pipeline sem abrir todas as células;
- visualizar as etapas de preparação da base de forma compacta;
- verificar quais arquivos são gerados ao final do processo.