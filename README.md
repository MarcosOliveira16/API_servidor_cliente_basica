# Documentação do Projeto: API Servidor e Cliente

## Visão Geral

Este projeto consiste em dois notebooks Colab que funcionam em conjunto para demonstrar o uso de uma API simples utilizando Flask e ngrok. O notebook `API_servidor` cria e expõe a API, enquanto o notebook `API_cliente` realiza chamadas para essa API. A API fornece vários métodos para consulta de cotações e conversão de valores.

## Estrutura dos Notebooks

### Notebook: API_servidor

#### Instalações Necessárias

Primeiro, instale os pacotes necessários para executar a API com ngrok:

```python
!pip install flask_ngrok
!pip install pyngrok
```

#### Configuração do Ngrok

Adicione seu token de autenticação do ngrok para poder expor a API:

```python
!ngrok config add-authtoken 'SEU_TOKEN_NGROK_AQUI'
```

#### Implementação da API

Importe as bibliotecas necessárias e crie uma instância do Flask:

```python
from flask import Flask, jsonify, request
from pyngrok import ngrok

app = Flask(__name__)
```

Defina as rotas e métodos da API:

1. **Rota Padrão**
    ```python
    @app.route('/')
    def padrao():
        return "Escolha um dos métodos"
    ```

2. **Rota `cotacao`**
    ```python
    @app.route('/cotacao/')
    def cotacao():
        return '5.34'
    ```

3. **Rota `conversao`**
    - O usuário deve passar o valor no caminho que deseja fazer a conversão.

    ```python
    @app.route('/conversao/<float:val>')
    def conversao(val):
        return str(val * 5.34)
    ```

4. **Rota `cotacaocompleta`**
    - Espera-se os parâmetros `valor` (valor a ser convertido) e `mes` (mês de referência para a cotação).

    ```python
    @app.route('/cotacaocompleta', methods=['GET'])
    def cotacaocompleta():
        argumentos = request.args
        valor = float(argumentos.get('valor'))
        mes = argumentos.get('mes')

        total = 0.0
        if mes == 'Janeiro':
            total = valor * 5.34
        elif mes == 'Fevereiro':
            total = valor * 5.22
        elif mes == 'Marco':
            total = valor * 5.19

        return str(total)
    ```

5. **Rota `tabela`**
    ```python
    @app.route('/tabela/')
    def tabela():
        return jsonify(Janeiro='5.34', Fevereiro='5.22', Marco='5.19')
    ```

Inicie a API e conecte ao ngrok:

```python
if __name__ == '__main__':
    port = 5000
    public_url = ngrok.connect(port)
    print(' * ngrok tunnel "{}" -> "http://127.0.0.1:{}/"'.format(public_url, port))

    try:
        app.run(port=port)
    except KeyboardInterrupt:
        ngrok.disconnect(public_url)
```

### Notebook: API_cliente

#### Métodos de Consulta

Realize chamadas para os diferentes métodos da API usando a biblioteca `requests`:

1. **Método Padrão**

    ```python
    import requests

    x = requests.get("https://SEU_LINK_NGROK.ngrok-free.app")
    print(x.text)
    ```

2. **Método `cotacao`**

    ```python
    import requests

    x = requests.get("https://SEU_LINK_NGROK.ngrok-free.app/cotacao")
    print(x.text)
    ```

3. **Método `conversao`**
    - O valor a ser convertido deve ser passado no caminho da URL.

    ```python
    import requests

    x = requests.get("https://SEU_LINK_NGROK.ngrok-free.app/conversao/200.0")
    print(x.text)
    ```

4. **Método `cotacaocompleta`**
    - Os parâmetros `valor` (valor a ser convertido) e `mes` (mês de referência para a cotação) devem ser passados na query string.

    ```python
    import requests

    x = requests.get("https://SEU_LINK_NGROK.ngrok-free.app/cotacaocompleta?valor=100&mes=Marco")
    print(x.text)
    ```

5. **Método `tabela`**

    ```python
    import requests

    x = requests.get("https://SEU_LINK_NGROK.ngrok-free.app/tabela")
    print(x.text)
    ```

## Considerações Finais

### Requisitos

- Conta no ngrok para obter o token de autenticação.
- Google Colab ou ambiente Python configurado com Flask e ngrok.

### Comentários

- Certifique-se de substituir `SEU_LINK_NGROK` pelo link gerado pelo ngrok ao iniciar a API.
- O token ngrok é necessário para estabelecer o túnel seguro que permite acesso público à API.
- A estrutura das rotas demonstra a flexibilidade do Flask para criar endpoints que aceitam diferentes tipos de parâmetros e métodos HTTP.

Essa documentação visa orientar a configuração e uso da API de forma clara e concisa, permitindo uma fácil replicação e adaptação do projeto conforme necessário.
