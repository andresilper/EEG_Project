# Comparação de Modelos para Classificação de EEG Motor

**Tarefa:** Classificação de sinais de Eletroencefalografia provenientes de até 5 classes 
Repouso, imaginação flexão, imaginação extensão, movimento flexão, movimento extensão e repouso. 
Este experimento foi desenhado para separar temporalmente a flexão e a extensão, focando em analisar sinais
provenientes de tarefas diferentes envolvendo o mesmo membro. 

**Validação:** 10-Fold Cross-Validation com data augmentation no treino  

---
## Contexto do Dataset

- **Número de sujeitos**: 19  
- **Canais**: 30 (sistema internacional 10-20)  
- **Taxa de amostragem**: 250 Hz  
- **Janela temporal**: 3 segundos (750 amostras por janela)  
- **Classes**: Repouso, Imaginação Motora (flexão/extensão), Movimento (flexão/extensão)  
- **Balanceamento aplicado**: 1814 amostras por classe após equalização  
- **Sinais pré-processados**: Aplicação de filtros e de algoritmos para remoção de artefatos

---
## Configurações do Treinamento

**Data Augmentation:**

| Parâmetro | Valor |
|---|---|
| `noise_level` | 0.015 |
| `time_shift_range` | 0.02 |
| `amplitude_scale_range` | (0.9, 1.1) |
| `p_noise` | 0.3 |
| `p_shift` | 0.3 |
| `p_scale` | 0.3 |
 
- **Validação**: 10-Fold Cross-Validation  
- **Otimizador**: Adam  
- **Scheduler**: ReduceLROnPlateau  
- **Early Stopping**: ativado para evitar sobreajuste  

**Balanceamento por classe:**

| Classe | Antes | Após balanceamento |
|---|---|---|
| Repouso (0) | 3639 | 1814 |
| Imaginação (1) | 1825 | 1814 |
| Movimento (2) | 1814 | 1814 |
| **Total** | **7278** | **5442** |

---

---
## Experimento 1: Análise por Classes - Movimento, Imaginação e Repouso

| Modelo | Acurácia Teste | Desvio Padrão | Melhora vs. EEGNet |
|---|---|---|---|
| EEGNet | 73.98% | ± 1.68% | — |
| EEGConformer | **84.22%** | ± 2.19% | **+10.24 p.p.** |

O EEGConformer superou a EEGNet em **+10.24 pontos percentuais** de acurácia média no conjunto de teste, com melhora consistente em todos os 10 folds.

---

## Configurações

| Parâmetro | EEGNet | EEGConformer |
|---|---|---|
| Arquitetura | CNN depthwise-separable | CNN + Transformer |
| Pré-treinamento | Não | Não |
| N_TIMES | 750 | 750 |
| Batch size | 32 | 32 |
| Learning rate | 1e-3 (com decay) | 1e-4 (com decay) |
| Normalização entrada | Z-score por canal | Z-score por canal |
| Otimizador | AdamW | Adam |
| Scheduler | ReduceLROnPlateau | ReduceLROnPlateau |
| Early stopping | Sim (patience=10) | Sim (patience=15) |
| Épocas máximas | 50 | 100 |

---

## Resultados por Fold

### EEGNet

| Fold | Test Acc | Val Acc | Épocas |
|---|---|---|---|
| 1 | 74.68% | 77.09% | 50 |
| 2 | 75.78% | 75.34% | 50 |
| 3 | 75.74% | 73.69% | 50 |
| 4 | 74.63% | 74.61% | 50 |
| 5 | 75.37% | 74.15% | 50 |
| 6 | 71.14% | 73.51% | 50 |
| 7 | 74.08% | 74.61% | 50 |
| 8 | 74.63% | 73.14% | 50 |
| 9 | 72.79% | 74.24% | 50 |
| 10 | 70.96% | 73.05% | 50 |
| **Média** | **73.98%** | **74.34%** | — |
| **Desvio** | **± 1.68%** | **± 1.14%** | — |

### EEGConformer

| Fold | Test Acc | Val Acc | Melhor Época |
|---|---|---|---|
| 1 | 85.32% | 86.11% | 90 |
| 2 | 84.77% | 87.84% | 96 |
| 3 | 83.46% | 84.58% | 75 |
| 4 | 84.38% | 83.15% | 59 |
| 5 | 86.40% | 85.39% | 100 |
| 6 | 85.85% | 85.19% | 98 |
| 7 | 81.99% | 81.21% | 46 |
| 8 | 87.87% | 87.64% | 94 |
| 9 | 80.88% | 82.12% | 40 |
| 10 | 81.25% | 83.04% | 68 |
| **Média** | **84.22%** | **84.63%** | — |
| **Desvio** | **± 2.19%** | **± 2.13%** | — |

---
## Experimento 2: Análise por Classes - Flexão, Extensão e Repouso

### Objetivo

Avaliar se tarefas diferentes envolvendo o mesmo membro são mais difíceis de classificar 
usando os mesmos algoritmos. 

## EEGNET

###  Treinamento base por Época
| Época | Train Acc | Val Acc | Learning Rate |
|-------|-----------|---------|---------------|
| 1/50  | 37.58%    | 36.89%  | 1.00e-03 |
| 10/50 | 57.27%    | 57.74%  | 1.00e-03 |
| 20/50 | 59.75%    | 55.14%  | 1.00e-03 |
| 30/50 | 60.41%    | 56.63%  | 1.00e-03 |
| 40/50 | 61.76%    | 58.11%  | 5.00e-04 |
| 50/50 | 62.42%    | 59.22%  | 2.50e-04 |

---

###  Detalhes por Fold

| Fold | Test Acc | Val Acc | Épocas |
|------|----------|---------|--------|
| 1    | 58.60%   | 60.15%  | 50 |
| 2    | 60.26%   | 61.26%  | 50 |
| 3    | 62.85%   | 60.70%  | 50 |
| 4    | 58.52%   | 61.54%  | 50 |
| 5    | 58.33%   | 57.46%  | 50 |
| 6    | 60.00%   | 60.15%  | 50 |
| 7    | 65.56%   | 61.72%  | 50 |
| 8    | 55.19%   | 59.41%  | 50 |
| 9    | 57.78%   | 60.43%  | 50 |
| 10   | 62.22%   | 60.15%  | 50 |
| **Média** | **59,31% ± 2.81%** | **60,29% ± 1.17%** | — |

---

### Relatório de Classificação 
| Classe    | Precision | Recall | F1-Score | Support |
|-----------|-----------|--------|----------|---------|
| Repouso   | 0.7464    | 0.8368 | 0.7890   | 1801 |
| Extensão  | 0.4813    | 0.4370 | 0.4581   | 1801 |
| Flexão    | 0.5397    | 0.5242 | 0.5318   | 1801 |
| **Macro avg** | **0.5892** | **0.5993** | **0.5930** | 5403 |
| **Weighted avg** | **0.5892** | **0.5993** | **0.5930** | 5403 |

---
## EEGConformer

### Resultados por Fold
| Fold | Test Acc | Val Acc | Melhor Época |
|------|----------|---------|--------------|
| 1    | 63.03%   | 63.17%  | 34 |
| 2    | 64.51%   | 63.89%  | 31 |
| 3    | 66.73%   | 65.53%  | 49 |
| 4    | 65.00%   | 61.52%  | 30 |
| 5    | 63.52%   | 62.76%  | 21 |
| 6    | 62.59%   | 66.56%  | 53 |
| 7    | 67.59%   | 64.51%  | 54 |
| 8    | 65.93%   | 64.09%  | 43 |
| 9    | 66.11%   | 62.96%  | 36 |
| 10   | 64.07%   | 64.20%  | 20 |
| **Média** | **64.91% ± 1.57%** | **63.92% ± 1.36%** | — |


**Comparação com EEGNet:**
- Baseline EEGNet: **59.93% ± 2.81%**
- Baseline EEGConformer **64.91% ± 1.57%**
- Diferença: **+4.98% **

---

### Matriz de Confusão Média (10 folds)

### Relatório de Classificação

| Classe    | Precision | Recall | F1-Score | Support |
|-----------|-----------|--------|----------|---------|
| Repouso   | 0.81      | 0.89   | 0.85     | 1801 |
| Flexão    | 0.53      | 0.58   | 0.56     | 1801 |
| Extensão  | 0.59      | 0.48   | 0.53     | 1801 |
| **Macro avg** | **0.64** | **0.65** | **0.64** | 5403 |
| **Weighted avg** | **0.64** | **0.65** | **0.64** | 5403 |

---

### Interpretação
- **Repouso**: melhor desempenho, com recall de 83.7%.  
- **Extensão**: classe mais difícil, recall de apenas 43.7%, frequentemente confundida com flexão.  
- **Flexão**: desempenho intermediário, recall de 52.4%.  
- **Acurácia geral**: ~60%, mostrando aprendizado acima do acaso (33%), mas ainda com espaço para melhorias.  

---
## Experimento 3: Análise de todas as 5 classes simultaneamente  
MV Flexão, IM Flexão, MV Extensão, IM Extensão e Repouso

### Objetivo

Avaliar se com os algoritmos aplicados é possível diferenciar entre 5 classes envolvendo tanto MV como IM e Repouso.

# EEGNET

### Treinamento Base

| Época | Train Acc | Val Acc | Learning Rate |
|-------|-----------|---------|---------------|
| 90/100 | 51.58%   | 53.13%  | 5.00e-04 |
| 100/100| 51.42%   | 54.14%  | 5.00e-04 |
| **Melhor época 97** | **53.69%** | **55.03% ** | — |

---

### Relatório de Classificação
| Classe           | Precision | Recall | F1-Score | Support |
|------------------|-----------|--------|----------|---------|
| Repouso          | 0.73      | 0.85   | 0.79     | 96 |
| Imag. Flexão     | 0.44      | 0.43   | 0.44     | 88 |
| Imag. Extensão   | 0.47      | 0.35   | 0.40     | 84 |
| Mov. Flexão      | 0.45      | 0.57   | 0.50     | 81 |
| Mov. Extensão    | 0.60      | 0.52   | 0.56     | 98 |
| **Macro avg**    | **0.54**  | **0.54** | **0.54** | 447 |
| **Weighted avg** | **0.55**  | **0.55** | **0.54** | 447 |

---
### K-Fold Validation 

| Fold | Test Acc | Val Acc | Melhor Época |
|------|----------|---------|--------------|
| 1    | 54.81%   | 55.47%  | 74 |
| 2    | 56.15%   | 53.11%  | 84 |
| 3    | 57.72%   | 56.09%  | 97 |
| 4    | 57.05%   | 52.99%  | 79 |
| 5    | 54.14%   | 54.48%  | 87 |
| 6    | 55.03%   | 53.48%  | 75 |
| 7    | 51.90%   | 55.72%  | 85 |
| 8    | 48.77%   | 53.11%  | 99 |
| 9    | 57.49%   | 54.10%  | 79 |
| 10   | 51.45%   | 53.61%  | 88 |
| **Average**    | **54.45% ± 2.79%**  | **54%** | - |
---

### Matriz de Confusão Média (10 folds)

| Classe           | Precision | Recall | F1-Score | Support |
|------------------|-----------|--------|----------|---------|
| Repouso          | 0.65      | 0.87   | 0.74     | 894 |
| Imag. Flexão     | 0.53      | 0.46   | 0.49     | 894 |
| Imag. Extensão   | 0.62      | 0.27   | 0.38     | 894 |
| Mov. Flexão      | 0.45      | 0.54   | 0.49     | 894 |
| Mov. Extensão    | 0.51      | 0.58   | 0.54     | 894 |
| **Macro avg**    | **0.55**  | **0.54** | **0.53** | 4470 |

---

### Interpretação
- **Repouso**: melhor classe, recall de 87%.  
- **Imaginação (Flexão/Extensão)**: desempenho mais baixo, especialmente imaginação de extensão (recall 27%).  
- **Movimento (Flexão/Extensão)**: desempenho intermediário, recall de 54% para flexão e 58% para extensão.  
- **Acurácia geral**: ~54%, bem acima do acaso (20%), mas ainda limitada para aplicações práticas.  

---

# EEGConformer

### Treinamento Base

| Época | Train Acc | Val Acc | Learning Rate |
|-------|-----------|---------|---------------|
| 70/100 | 54.43%   | 53.80%  | 1.25e-05 |
| 80/100 | 55.67%   | 54.03%  | 3.13e-06 |
| Early stopping na época 80 |

### Matriz de Confusão Média 

| Classe           | Precision | Recall | F1-Score | Support |
|------------------|-----------|--------|----------|---------|
| Repouso          | 0.69      | 0.85   | 0.76     | 96 |
| Imag. Flexão     | 0.49      | 0.60   | 0.54     | 88 |
| Imag. Extensão   | 0.48      | 0.27   | 0.35     | 84 |
| Mov. Flexão      | 0.51      | 0.59   | 0.55     | 81 |
| Mov. Extensão    | 0.64      | 0.50   | 0.56     | 98 |
| **Macro avg**    | **0.56**  | **0.56** | **0.55** | 447 |
| **Weighted avg** | **0.57**  | **0.57** | **0.56** | 447 |

---

### K-Fold Validation (k = 10)

| Fold | Test Acc | Val Acc | Melhor Época |
|------|----------|---------|--------------|
| 1    | 54.59%   | 56.97%  | 66 |
| 2    | 57.94%   | 57.59%  | 88 |
| 3    | 50.56%   | 53.36%  | 54 |
| 4    | 60.63%   | 56.84%  | 89 |
| 5    | 59.06%   | 57.59%  | 97 |
| 6    | 63.76%   | 55.10%  | 100 |
| 7    | 57.49%   | 55.60%  | 72 |
| 8    | 59.96%   | 62.94%  | 100 |
| 9    | 54.36%   | 56.84%  | 81 |
| 10   | 63.98%   | 64.43%  | 100 |
| **Average**    | **58.84% ± ~4.0%**  | **56.95% ± ~3.0%** | - |
---

### Discussão
- **Repouso**: continua sendo a classe mais fácil, com recall alto.  
- **Imaginação (Flexão/Extensão)**: ainda desafiadora, mas o EEGConformer conseguiu melhorar a separação em relação ao EEGNet.  
- **Movimento (Flexão/Extensão)**: desempenho intermediário, com ganhos consistentes em relação ao baseline.  
- **Acurácia geral**: ~59%, mostrando avanço sobre o EEGNet (54%).  

---

## Experimento 4: Análise de Grupos de Canais

### Objetivo

Avaliar o impacto da seleção de canais EEG no desempenho das redes neurais utilizadas,
identificando quais regiões cerebrais contribuem mais para a discriminação entre
Repouso, Imaginação e Movimento de punho.

### Número de Épocas por Classe

|Total de épocas inicial | 7278 |
|---------|------------------|----------|
|Balanceando |894 épocas por Classe |
|Classe 0 (Repouso)| 3639 | 894 |
|Classe 1 (Imag. Flexão)| 918 | 894 |
|Classe 2 (Imag. Extensão)| 907 | 894 |
|Classe 3 (Mov. Flexão)| 920 | 894 |
|Classe 4 (Mov. Extensão)| 894 | 894 |
|Total após balanceamento | 7278 | 4470|

### EEGNet

| Ranking | Grupo             | Nº Canais | Test Acc | Val Acc |
|---------|------------------|-----------|----------|---------|
| 1       | Todos os canais  | 30        | 75.98% ± 1.86 | 77.33% ± 1.00 |
| 2       | Motor completo   | 14        | 72.34% ± 2.40 | 72.09% ± 1.27 |
| 3       | Parietal         | 12        | 69.17% ± 1.77 | 69.92% ± 1.08 |
| 4       | Sensoriomotor    | 9         | 69.13% ± 2.65 | 68.57% ± 1.72 |
| 5       | Frontal motor    | 10        | 68.91% ± 1.56 | 68.56% ± 1.87 |
| 6       | Linha média      | 7         | 68.60% ± 2.07 | 68.99% ± 1.30 |
| 7       | Hemisfério esq.  | 9         | 67.95% ± 2.15 | 68.93% ± 1.61 |
| 8       | Hemisfério dir.  | 9         | 66.63% ± 2.48 | 67.76% ± 1.20 |
| 9       | Motor primário   | 5         | 66.12% ± 2.14 | 67.02% ± 1.60 |
| 10      | Lateral motor    | 8         | 65.86% ± 1.65 | 66.49% ± 1.78 |
| 11      | Temporal         | 6         | 65.05% ± 1.84 | 65.55% ± 0.78 |

 
### Composição dos Grupos

| Grupo | Canais |
|---|---|
| `todos_canais (30)` | FpZ, F3, Fz, F4, FC5, FC3, FC1, FC2, FC4, FC6, C5, C3, C1, Cz, C2, C4, C6, CP3, CP1, CPZ, CP2, CP4, P7, P3, P1, Pz, P2, P4, P8, Oz |
| `motor_completo (14)` | F3, Fz, F4, FC1, FC2, FC5, FC6, C3, Cz, C4, CP1, CP2, CP3, CP4 |
| `parietal (12)` | CP3, CP1, CPZ, CP2, CP4, P7, P3, P1, Pz, P2, P4, P8 |
| `sensoriomotor (9)` | C5, C3, Cz, C4, C6, CP1, CP2, CP3, CP4 |
| `frontal_motor (10)` | FpZ, F3, Fz, F4, FC5, FC3, FC1, FC2, FC4, FC6 |
| `linha_media (7)` | Fz, FC1, FC2, Cz, CP1, CP2, Pz |
| `hemisferio_esquerdo (9)` | F3, FC5, FC1, C3, C5, CP3, CP1, P3, P7 |
| `hemisferio_direito (9)` | F4, FC6, FC2, C4, C6, CP4, CP2, P4, P8 |
| `motor_primario (5)` | C5, C3, Cz, C4, C6 |
| `lateral_motor (8)` | C5, C3, C4, C6, FC5, FC6, CP3, CP4 |
| `temporal (6)` | C5, C6, FC5, FC6, CP3, CP4 |

---
Experimento 5: Mapas de atenção

---
## Análise por Classe

### Repouso (label 0)
A classe mais fácil em ambos os modelos. O EEGConformer reduziu os erros de confusão com Movimento de 11 para apenas 5 casos, sugerindo melhor captura das diferenças espectrais entre repouso e estado ativo.

### Imaginação Motora (label 1)
A classe mais difícil em ambos os modelos, como esperado fisiologicamente — os padrões de ERD/ERS da imaginação motora são mais sutis que os do movimento real. O EEGConformer obteve F1=0.79 contra F1=0.65 da EEGNet, uma melhora de **+14 p.p.** A principal confusão restante é com Movimento (18 casos), o que é compreensível dado que imaginação e execução motora compartilham circuitos neurais semelhantes.

### Movimento (label 2)
Excelente desempenho em ambos os modelos. O EEGConformer atingiu F1=0.87, com apenas 23 erros totais em 1814 amostras de teste agregadas.

---
## Discussão dos Resultados

- **Todos os canais (30)**: melhor desempenho geral (≈76% acurácia), confirmando que a informação distribuída em múltiplas regiões é relevante para a tarefa  
- **Motor completo (14 canais)**: segunda melhor performance (≈72%), mostrando que focar em regiões motoras e pré-motoras mantém boa acurácia com menos canais  
- **Parietal e sensoriomotor (≈69%)**: desempenho intermediário, sugerindo que essas regiões contribuem, mas não isoladamente  
- **Motor primário e lateral/temporal (≈65–66%)**: pior desempenho, indicando que usar apenas áreas focais reduz a capacidade de distinguir imaginação de movimento  
- **Assimetria hemisférica**: hemisfério esquerdo teve ligeira vantagem sobre o direito (≈68% vs. 67%), consistente com lateralização motora em sujeitos destros  
---

## Conclusões

- **Imaginação motora** envolve redes distribuídas além do córtex motor primário, incluindo áreas pré-frontais e parietais  
- O melhor desempenho com **todos os canais** e **motor completo** reforça que a classificação depende de padrões globais e não apenas focais, ou que a alta correlação entre os canais de EEG facilita a classificação quando mais canais são adicionados.  
- A diferença entre hemisférios pode refletir lateralização funcional, mas precisa ser confirmada com análise por sujeito  

O ganho expressivo do EEGConformer (+10.24% de acurácia no treinamento base) pode ser atribuído a 

**Arquitetura híbrida CNN + Transformer:** a CNN extrai features locais temporais e espaciais do EEG (como filtros de banda e padrões espaciais), enquanto o Transformer captura dependências temporais de longo alcance entre os patches. A EEGNet captura apenas features locais.

O maior desvio padrão do EEGConformer (± 2.19% vs. ± 1.68%) indica maior variabilidade entre folds, provavelmente porque o modelo tem mais parâmetros e é mais sensível à composição de cada fold.

---

## Próximos Passos

- **EEGPT:** foundation model pré-treinado em ~2.500h de EEG. Expectativa: superar o EEGConformer explorando o conhecimento transferido do pré-treinamento, especialmente para a classe Imaginação
- **Testar EEGConformer/EEG‑former** com subsets de canais para verificar se a arquitetura transformer melhora a generalização em grupos menores  
- **Explorar seleção automática de canais** (feature selection) para identificar subconjuntos ótimos sem depender de agrupamentos manuais  
- **Analisar por sujeito**: verificar se a lateralização e os padrões de acurácia se mantêm individualmente  
- **Explainability**: usar mapas de atenção ou Grad-CAM para visualizar quais canais mais contribuem para a decisão do modelo  

*Gerado em fevereiro de 2026*
