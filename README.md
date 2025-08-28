# analise-sentimentos-ecommerce-api
Análise de Sentimentos em Reviews de E-commerce com Deploy de API

# Análise de Sentimentos em Reviews de E-commerce com Deploy de API

*Desenvolvimento de um modelo de Processamento de Linguagem Natural (NLP) para classificar reviews de produtos como positivos ou negativos, e seu deploy como uma API REST utilizando FastAPI.*

![Banner do Projeto](https://i.imgur.com/your-image-link-here.png)

## 1. Sumário do Problema e Objetivo de Negócio

Empresas de e-commerce recebem milhares de reviews de produtos diariamente. Analisar manualmente esse volume de feedback é impraticável. A automação dessa análise permite que a empresa:
* **Entenda a Satisfação do Cliente:** Obtenha um termômetro em tempo real da percepção dos clientes sobre os produtos.
* **Identifique Problemas Rapidamente:** Detecte picos de reviews negativos para um produto específico, indicando possíveis problemas de qualidade ou logística.
* **Priorize Atendimento:** Direcione a equipe de suporte para responder a clientes insatisfeitos com maior agilidade.

Este projeto constrói uma solução de ponta a ponta (end-to-end) que treina um modelo de NLP e o expõe através de uma API, pronto para ser integrado a um dashboard de BI ou a um sistema de CRM.

**Fonte dos Dados:** [Brazilian E-Commerce Public Dataset by Olist - Kaggle](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) (utilizando o arquivo `olist_order_reviews_dataset.csv`)

## 2. Metodologia e Ferramentas

| Ferramenta / Biblioteca   | Propósito                                                                               |
| ------------------------- | --------------------------------------------------------------------------------------- |
| **Python, Pandas** | Linguagem e biblioteca para manipulação e limpeza dos dados de texto.                   |
| **NLTK, Spacy** | Ferramentas para pré-processamento de texto (tokenização, remoção de stopwords, lematização). |
| **Scikit-learn** | Vetorização de texto (`TfidfVectorizer`) e treinamento do modelo baseline (`LogisticRegression`). |
| **Hugging Face Transformers**| Utilização de um modelo pré-treinado em português (`BERTimbau`) para fine-tuning e alta performance. |
| **PyTorch / TensorFlow** | Framework de Deep Learning para treinar o modelo Transformer.                             |
| **FastAPI** | Framework web para criar a API REST de forma rápida e eficiente.                         |
| **Uvicorn** | Servidor ASGI para rodar a aplicação FastAPI.                                           |
| **Docker** | (Opcional) Containerização da aplicação para facilitar o deploy.                        |

## 3. Pipeline do Projeto

### 3.1. Pré-processamento do Texto
Os dados de review, escritos em português, passaram por um rigoroso processo de limpeza:
* Conversão para minúsculas.
* Remoção de pontuação e caracteres especiais.
* Remoção de stopwords (palavras comuns como "e", "ou", "de").
* Lematização para reduzir palavras à sua forma base (ex: "gostei", "gostando" -> "gostar").

### 3.2. Modelagem: De Baseline a Transformer

1.  **Baseline (TF-IDF + Regressão Logística):** Foi criado um primeiro modelo utilizando uma abordagem clássica. O texto foi vetorizado com TF-IDF (Term Frequency-Inverse Document Frequency) e classificado com uma Regressão Logística. Este modelo atingiu uma acurácia de **~88%**.
2.  **Modelo Avançado (Fine-tuning de BERT):** Para obter um desempenho superior e entender o contexto das frases, foi utilizado o `BERTimbau`, um modelo Transformer pré-treinado para o português do Brasil. O modelo foi ajustado (fine-tuned) para a tarefa específica de classificação de sentimentos dos nossos dados.

### 3.3. Deploy com FastAPI
O modelo `BERTimbau` ajustado foi salvo e encapsulado em uma API REST com dois endpoints:
* `GET /`: Endpoint raiz para verificar se a API está online.
* `POST /predict`: Recebe um JSON com um texto de review e retorna a classificação (Positivo/Negativo) e a probabilidade associada.

## 4. Resultados e Avaliação

O modelo `BERTimbau` ajustado superou significativamente o baseline, demonstrando o poder dos modelos de linguagem contextual.

| Modelo                            | Acurácia | F1-Score |
| --------------------------------- | -------- | -------- |
| TF-IDF + Regressão Logística      | 0.88     | 0.88     |
| **Fine-tuned BERTimbau** | **0.95** | **0.95** |

O deploy via FastAPI permite que o modelo seja facilmente consumido por outras aplicações, como no exemplo abaixo:

```bash
# Exemplo de requisição para a API
curl -X 'POST' \
  '[http://127.0.0.1:8000/predict](http://127.0.0.1:8000/predict)' \
  -H 'Content-Type: application/json' \
  -d '{
    "text": "Adorei o produto, chegou muito rápido e a qualidade é excelente!"
  }'

# Resposta esperada da API
# {"sentiment": "Positivo", "probability": 0.9987}
```

## 5. Conclusão e Próximos Passos

Este projeto construiu com sucesso uma solução completa de NLP, desde o processamento de texto até o deploy de um modelo de última geração como um serviço.

**Próximos Passos:**
* **Front-end:** Criar uma interface web simples com Streamlit ou React para interagir com a API.
* **Cloud Deploy:** Publicar a aplicação containerizada (Docker) em um serviço de nuvem como AWS Elastic Beanstalk, Google Cloud Run ou Heroku.
* **Extração de Tópicos:** Expandir a solução para não apenas classificar o sentimento, mas também extrair os tópicos principais dos reviews (ex: "entrega", "qualidade do produto", "atendimento").

## 6. Como Executar o Projeto

1.  Clone o repositório.
2.  Instale as dependências com `pip install -r requirements.txt`.
3.  Para treinar o modelo, execute o notebook `Treinamento_Sentimentos.ipynb`.
4.  Para iniciar a API localmente, execute o comando:
    ```bash
    uvicorn main:app --reload
    ```
5.  Acesse a documentação interativa da API em `http://127.0.0.1:8000/docs`.
