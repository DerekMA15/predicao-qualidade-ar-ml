# Predição de Níveis de Benzeno (C6H6) com Aprendizado de Máquina

- Projeto Final — Ciência da Computação
- Disciplina: _Inteligência Artificial_
- **Universidade Federal de Sergipe (UFS)**
- Período: [2026.1]

---

## Integrantes do Grupo

- Derek Marques Almeida
- Erick Oliveira da Silva
- Guilherme Góis Araujo


## Vídeo de Apresentação

> **[[Apresentações estão aqui!](https://drive.google.com/drive/u/2/folders/1tSbB4c0CIHwYFFJ87xbJ_B7VvYuCcWBy)]**

---

## Sobre o Projeto

O monitoramento da qualidade do ar é essencial para a saúde pública, mas as estações de referência tradicionais possuem **alto custo de instalação e manutenção**, o que limita a densidade de pontos de coleta em uma cidade. Uma alternativa promissora é o uso de **sensores químicos de baixo custo**, que, embora menos precisos, podem ser combinados a modelos de Aprendizado de Máquina para estimar poluentes com boa acurácia.

Este projeto utiliza o dataset **AirQualityUCI**, contendo leituras horárias de sensores de qualidade do ar instalados em uma cidade italiana, para construir um modelo capaz de **prever a concentração de Benzeno (C6H6)** a partir das respostas de sensores de baixo custo (como o PT08.S2) e de variáveis ambientais (temperatura, umidade, etc).

O problema foi tratado como uma tarefa de **Regressão**, já que o objetivo é estimar um valor contínuo (concentração de C6H6 em µg/m³), e não classificar categorias.

---

## Estruturação do Repositório

A raiz do repositório contém os seguintes arquivos principais:

```
📦 repositório
 ┣ 📜 LICENSE                  # licença do MIT
 ┣ 📜 AirQualityUCI.csv        # Dataset original utilizado no projeto
 ┣ 📜 predicao_arl.ipynb       # Notebook com todo o pipeline (EDA, pré-processamento, modelagem e avaliação)
 ┗ 📜 README.md                # Este arquivo
```

- **`AirQualityUCI.csv`**: dataset bruto, contendo as leituras horárias dos sensores e das estações de referência.
- **`projeto_final.ipynb`**: notebook Jupyter com a análise exploratória, o tratamento dos dados, o treinamento dos modelos e a avaliação dos resultados.

## Instruções para Execução no Google Colab

Para abrir e executar os testes deste projeto diretamente no seu navegador, siga os passos abaixo:

1. Acesse o ambiente do [Google Colab]([https://colab.research.google.com/](https://colab.research.google.com/drive/1lpBryqWCRKIDNUhfn0UrHCJnngNPRsaP?usp=sharing)).
2. Na janela inicial, selecione a aba **GitHub**.
3. Cole a URL deste repositório na barra de pesquisa e pressione Enter.
4. Clique no arquivo `projeto_final.ipynb` que aparecerá na lista para abri-lo.
5. Com o notebook aberto, vá no menu superior e clique em **Runtime** (Ambiente de execução) > **Run all** (Executar tudo). 

*Nota: O código já está configurado para baixar o dataset `AirQualityUCI.csv` automaticamente direto da nuvem, não sendo necessário realizar o upload manual de nenhum arquivo.*

---

## Análise Exploratória e Visualizações

Antes da modelagem, foi realizada uma análise exploratória dos dados (EDA) para compreender a distribuição da variável-alvo e as relações entre as features disponíveis.

### Distribuição da Variável-Alvo (C6H6)

<img width="870" height="479" alt="image" src="https://github.com/user-attachments/assets/b3f1bbed-d2e4-4efa-89f0-7c01330e9618" />


A análise da distribuição do Benzeno mostrou um comportamento assimétrico, com concentração da maioria dos valores em faixas mais baixas e uma cauda mais longa em direção a valores mais altos, típico de variáveis ambientais de poluição.

### Matriz de Correlação

<img width="893" height="792" alt="image" src="https://github.com/user-attachments/assets/ab1ff6bc-2107-4d40-bc7f-44507d7a22ba" />


A matriz de correlação permitiu identificar quais variáveis possuem maior relação linear com o Benzeno. Destaca-se o sensor **PT08.S2 (Titânia)**, que apresentou uma correlação extremamente forte com o alvo (**r ≈ 0.98**), indicando que esse sensor de baixo custo é altamente informativo para estimar a concentração de C6H6, mesmo sem o uso de instrumentos de referência.

### Dispersão entre PT08.S2 e Benzeno

<img width="698" height="556" alt="image" src="https://github.com/user-attachments/assets/d44438dd-b3e4-4ca3-a79e-c2fef4442d30" />

O gráfico de dispersão confirma visualmente a forte relação (quase linear) entre a resposta do sensor PT08.S2 e a concentração real de Benzeno, reforçando seu papel como principal preditor do modelo.

---

## Pré-processamento e Modelagem

As seguintes decisões técnicas foram tomadas ao longo do pipeline:

- **Tratamento de valores ausentes**: no dataset original, valores ausentes são codificados como `-200`. Todos esses valores foram convertidos para `NaN` e, em seguida, imputados por **preenchimento sequencial (`ffill` e `bfill`)**, técnica que preserva a continuidade temporal das medições, adequada ao caráter de série temporal do dataset.
- **Validação Cruzada**: a robustez e a comparação entre os modelos foram avaliadas por meio de **Validação Cruzada de 5 dobras (5-Fold Cross-Validation)** no conjunto de treinamento, garantindo maior confiabilidade na escolha do melhor algoritmo.
- **Prevenção de Data Leakage**: colunas correspondentes às medições das **estações de referência** (instrumentos de alta precisão) foram removidas do conjunto de features, garantindo que o modelo aprenda a prever o Benzeno **apenas a partir dos sensores de baixo custo** — cenário fiel ao problema real que o projeto busca resolver.
- **Divisão dos dados**: separação em conjuntos de **treino (80%)** e **teste (20%)**, permitindo uma avaliação honesta da capacidade de generalização dos modelos.
- **Escalonamento**: aplicação do **StandardScaler** para padronizar as features numéricas, etapa importante especialmente para modelos sensíveis à escala, como a Regressão Linear.

---

## Resultados e Conclusão

Foram treinados e comparados quatro modelos, em ordem crescente de complexidade:

| Modelo | Descrição |
|---|---|
| **Baseline** | Modelo simples (ex: previsão pela média), utilizado como referência mínima de desempenho |
| **Regressão Linear** | Modelo linear, avaliando a relação direta entre sensores e o alvo |
| **Árvore de Decisão** | Modelo não-linear, capaz de capturar interações mais complexas entre as features |
| **Random Forest** | Ensemble de árvores, buscando maior robustez e melhor generalização |

---

## Divisão das Contribuições

O desenvolvimento deste projeto foi realizado de forma colaborativa, com a seguinte divisão principal de tarefas:

* **Derek Marques Almeida:** Implementação da Árvore de Decisão revisão técnica do notebook e formatação da documentação final no repositório.
* **Erick Oliveira da Silva:** Responsável pela Análise Exploratória de Dados (EDA), geração de gráficos de correlação e roteirização do vídeo.
* **Guilherme Gois Araujo:** Implementação dos modelos Baseline e Regressão Linear, e cálculo das métricas de avaliação MAE e RMSE.

---

## Declaração de Uso de Ferramentas de IA

Em conformidade com as diretrizes da disciplina, o grupo declara a utilização de modelos de linguagem no desenvolvimento deste trabalho:

* **Ferramentas utilizadas:** Google Gemini e Anthropic Claude.
* **Finalidade:** Apoio como tutor de estudos para estruturação do raciocínio lógico do pipeline de dados, resolução de erros de biblioteca (como falhas de leitura no Pandas) e auxílio na formatação do esqueleto textual em Markdown.
* **Parte do trabalho em que foi utilizada:** Discussão sobre metodologias para prevenção de *Data Leakage*, entendimento do comportamento do sensor PT08.S2 e estruturação do documento `README.md`.
* **Forma de verificação do conteúdo:** Nenhum código gerado foi incluído de forma cega. Todos os blocos sugeridos foram testados interativamente célula a célula, ajustando-se parâmetros e variáveis. As interpretações finais dos gráficos e das decisões de pré-processamento foram elaboradas pelos próprios integrantes, e a documentação textual revisada para refletir fielmente as saídas reais geradas no nosso ambiente Colab.

### Melhor Modelo

O **Random Forest** apresentou o melhor desempenho geral entre os modelos avaliados, superando a Regressão Linear e a Árvore de Decisão isolada nas métricas de erro utilizadas na avaliação — **MAE (Erro Absoluto Médio)**, **MSE (Erro Quadrático Médio)** e **RMSE (Raiz do Erro Quadrático Médio)**. Isso é esperado, dado que o ensemble reduz o overfitting característico de uma única árvore e consegue capturar relações não-lineares presentes nos dados dos sensores.

### Limitações

- **Drift dos sensores**: sensores químicos de baixo custo tendem a sofrer degradação e desvio de calibração (*sensor drift*) ao longo do tempo, o que pode comprometer a performance do modelo em períodos distantes do intervalo de treinamento.
- O dataset é referente a uma única localidade e período específico, o que pode limitar a generalização do modelo para outras cidades ou condições climáticas distintas.

---

<div align="center">

Projeto desenvolvido como Trabalho Final da disciplina de Inteligência Artificial — Ciência da Computação

</div>
