# Classificação de Tumores de Mama com SVC e Random Forest

Projeto de aprendizado de máquina supervisionado que compara dois classificadores, Support Vector Classifier (SVC) e Random Forest, na tarefa de distinguir tumores malignos e benignos a partir de características extraídas de imagens digitalizadas de células. O trabalho inclui ajuste de hiperparâmetros com validação cruzada, seleção de atributos por importância, avaliação com métricas de classificação e visualização de fronteiras de decisão.

## Sumário

- [Dados](#dados)
- [Metodologia](#metodologia)
- [Resultados](#resultados)
- [Requisitos](#requisitos)
- [Como executar](#como-executar)

## Dados

Foi utilizado o conjunto de dados Breast Cancer Wisconsin (Diagnostic), disponível diretamente no scikit-learn por meio de `load_breast_cancer`.

| Característica | Valor |
| --- | --- |
| Amostras | 569 |
| Atributos | 30 (todos numéricos) |
| Valores ausentes | Nenhum |
| Classes | `0` = maligno (malignant), `1` = benigno (benign) |

Os 30 atributos correspondem a dez medidas (raio, textura, perímetro, área, suavidade, compacidade, concavidade, pontos côncavos, simetria e dimensão fractal), cada uma representada pela média, pelo erro padrão e pelo pior valor (worst).

## Metodologia

1. **Preparação dos dados.** Verificação de valores ausentes e divisão em treino (80%, 455 amostras) e teste (20%, 114 amostras), com `random_state=42` para reprodutibilidade.
2. **SVC com Grid Search.** Busca em validação cruzada de 3 folds, com a precisão (`precision`) como métrica, sobre os kernels `linear`, `poly` e `rbf` e diferentes valores de `C`, `degree` e `gamma`.
3. **Random Forest com Grid Search.** Mesma estratégia, variando `n_estimators`, `max_features` (`sqrt` e `log2`) e `max_depth`.
4. **Seleção de atributos.** Um Random Forest auxiliar é treinado para calcular a importância de cada atributo (redução média de impureza). Atributos com importância inferior a 0,004 são removidos, e o modelo é treinado novamente com o conjunto reduzido.
5. **Avaliação.** Matriz de confusão, relatório de classificação (precisão, recall e F1) e acurácia sobre o conjunto de teste, além da acurácia normalizada pelo número de atributos de cada modelo.
6. **Visualização.** Fronteiras de decisão de cada modelo treinado com apenas duas variáveis (`mean radius` e `mean texture`), para fins ilustrativos.

## Resultados

Resultados obtidos no conjunto de teste (114 amostras) com a configuração do repositório. Nas matrizes de confusão, "maligno classificado como benigno" corresponde aos falsos negativos para a classe maligna.

| Modelo | Melhores hiperparâmetros | Precisão na validação cruzada | Acurácia no teste | Maligno classificado como benigno | Benigno classificado como maligno |
| --- | --- | --- | --- | --- | --- |
| SVC (Grid Search) | `kernel='linear'`, `C=0.7` | 0,955 | 0,965 | 3 | 1 |
| Random Forest (Grid Search) | `n_estimators=10`, `max_depth=6`, `max_features='log2'` | 0,962 | 0,956 | 2 | 3 |
| Random Forest (atributos selecionados) | `n_estimators=15`, `min_samples_split=5` | não aplicável | 0,956 | 3 | 2 |

Os três modelos têm desempenho muito próximo. Com 114 amostras de teste, a diferença entre eles equivale a uma ou duas predições e não deve ser interpretada como superioridade de um modelo sobre outro.

A métrica `precision` do Grid Search é calculada em relação à classe positiva, que no scikit-learn é a classe `1` (benigno). Os valores exatos podem variar levemente conforme a versão das bibliotecas.

## Requisitos

- Python 3.10 ou superior
- numpy
- pandas
- matplotlib
- scikit-learn (versão 1.2 ou superior recomendada)
- Jupyter Notebook ou JupyterLab

O notebook foi executado com Python 3.13, scikit-learn 1.8.0, matplotlib 3.10.8, pandas 3.0.2 e numpy 2.4.4.

## Como executar

```bash
# Clonar o repositório
git clone https://github.com/oliveira-eduardo/breast-cancer-classification.git
cd breast-cancer-classification

# (Opcional) Criar e ativar um ambiente virtual
python -m venv .venv
source .venv/bin/activate        # Linux e macOS
.venv\Scripts\activate           # Windows

# Instalar as dependências
pip install numpy pandas matplotlib scikit-learn jupyter

# Abrir o notebook
jupyter notebook classificacao_cancer_mama.ipynb
```

Execute as células em ordem. O conjunto de dados é carregado do próprio scikit-learn, portanto não há arquivos externos a baixar.
