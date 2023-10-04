# Explorando IA Generativa em um Pipeline de ETL com Python

## Desafio:
Construir um pipeline ETL (Extração, Transformação e Carregamento), demonstrando a relação entre dados, Inteligência Artificial (IA) e APIs. 

## Etapas ETL:
- Extract (Extração)
	* Pandas e consumo de API REST (GET)
- Transform (Transformação ou Enriquecimento)
	* Integração com a API do ChatGPT (OpenAI)
	* IA Generativa
	* Prompts
- Load (Carregamento)
	* Consumo de API REST (PUT)

#### Extração: 
O desafio começa com uma planilha simples (arquivo SDW2023.csv), de onde são extraídos os IDs dos usuários. Depois, esses IDs serão usados para acessar a API da ['Santander Dev Week 2023'](https://sdw-2023-prd.up.railway.app/swagger-ui/index.html) e obter dados mais detalhados. 

Obs.: consumindo a API REST (POST) foram criados os usuários de IDs 4206, 4207 e 4208

```
# TODO: Extrair os IDs do arquivo CSV

import pandas as pd

df = pd.read_csv('SDW2023.csv')
user_ids = df['UserID'].tolist()
print(user_ids)
```

```
# TODO: Obter os dados de cada ID usando a API da Santander Dev Week 2023

import requests
import json

def get_user(id):
  response = requests.get(f'{sdw2023_api_url}/users/{id}')
  return response.json() if response.status_code == 200 else None

users = [user for id in user_ids if (user := get_user(id)) is not None]
print(json.dumps(users, indent=2))
```

#### Transformação: 
Adentramos o universo da IA com o GPT-3 da OpenAI, transformando esses dados em mensagens personalizadas de marketing. 

```
# TODO: Instalar OpenAI em Python

!pip install openai
```

```
# TODO: Substituir o texto TODO por sua API Key da OpenAI, ela será salva como uma variável de ambiente

openai_api_key = 'TODO'
```

```
# TODO: Implementar a integração com o ChatGPT usando o modelo GPT-3

import openai
openai.api_key = openai_api_key

def generate_ai_news(user):
  completion = openai.ChatCompletion.create(
    model="gpt-3.5-turbo",
    messages=[
      {
        "role": "system",
        "content": "Você é um especialista em marketing bancário."
      },
      {
        "role": "user",
        "content": f"Crie uma mensagem para {user['name']} sobre a importância dos investimentos (máximo de 100 caracteres e citando {user['name']} para que a mensagem seja personalizada)"
      }
    ]
  )
  return completion.choices[0].message.content.strip('\"')

for user in users:
  news = generate_ai_news(user)
  print(news)
  user['news'].append({
      "icon": "https://digitalinnovationone.github.io/santander-dev-week-2023-api/icons/insurance.svg",
      "description": news
  })
```

#### Carregamento: 
Finalizamos o processo enviando essas mensagens de volta para a API da ['Santander Dev Week 2023'](https://sdw-2023-prd.up.railway.app/swagger-ui/index.html). Este passo ilustra como dados transformados são reintegrados em sistemas, completando o ciclo de um pipeline ETL.

```
# TODO: Atualizar os usuários na API da Santander Dev Week 2023 com os dados enriquecidos

def update_user(user):
  response = requests.put(f"{sdw2023_api_url}/users/{user['id']}", json=user)
  return True if response.status_code == 200 else False

for user in users:
  success = update_user(user)
  print(f"User {user['name']} updated? {success}!")
```

## Links Úteis:
- [colab.research.google.com](https://colab.research.google.com/drive/1SF_Q3AybFPozCcoFBptDSFbMk-6IVGF-?usp=sharing): link do Notebook criado via Google Colab com todo o **código-fonte original** deste Desafio de Projeto (Lab);
- [github.com/digitalinnovationone](https://github.com/digitalinnovationone/santander-dev-week-2023-api): GitHub com a API desenvolvida para a Santander Dev Week 2023 com informações úteis (incluindo o link do Swagger e dados importantes sobre a disponibilidade da API).

