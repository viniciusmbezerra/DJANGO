# DJANGO

## Ambiente Virtual
```php
python -m venv venv
venv/Scripts/activate //windows
venv/bin/activate //linux
```
Configurações iniciais e dependências para acelerar e melhorar o desenvolvimento
```
pip install pip --upgrade
pip install setuptools wheel --upgrade
pip install pytest, pytest-django, mypy, flake8, autopep8
```
Instalando django
```
pip install django
```
Gerando dependências
```
pip freeze > requirements.txt
```
Nós estamos usando ``autopep8 para formatar nosso código`` Python automaticamente ao salvar o arquivo, o ``flake8 para forçar um estilo de código`` em conformidade com a Python PEP8 e o ``mypy para forçar o uso correto da tipagem`` no Python.

## Testes Unitários

Os testes vão te ensinar muito sobre a aplicação que você está desenvolvendo.

O Pytest tem um Test Runner (programa para executar testes) que nos dá mais informações visuais, com cores, divisões e informações que facilitam a leitura dos erros e sucessos.

Na raiz do projeto deve ser criado o ``pytest.ini``:
```ini
[pytest]
# caminho de importação do settings do projeto
DJANGO_SETTINGS_MODULE = NOME_PROJETO.settings

# em que arquivos deve-se buscar testes
python_files = test.py tests.py test_*.py tests_*.py *_test.py *_tests.py

# flags para o módulo de teste
addopts = 
  --doctest-modules
  --strict-markers
  -rP

# marcas para identificar testes por características
markers = 
  slow: Run tests that are slow 
  fast: Run fast tests
```
Este arquivo também é usado para inicializar o Pytest, então podemos passar configurações para modificar o comportamento dele. Esse arquivo é necessário para o pytest-django saber onde está o settings.py.

``Execução do testes``
```python
pytest # executa todos com Pytest
pytest -k 'nome_do_teste' # executa um único teste

python manage.py test # executa todos com Unitest
python manage.py test -k 'nome_do_teste' # executa um único teste
```
``Pytest watch``
```python
pip install pytest-watch
ptw # testes ao vivo
```

Forçando falha de um teste
```python
class AppTest(TestCase):
  ...
  def test_exemplo(self):
    ...
    self.fail('Mensagem') # 
  ...
```

Pulando um teste
```python
from unittest import skip

class AppTest(TestCase):
  @skip('Mensagem')
  def test_exemplo(self):
    ...
```

``Asserções são afirmações que o desenvolvedor faz em seus testes para que o Test Runner as "julgue".`` Em conjunto com o módulo padrão de testes do Python, Unittest, o Django criou uma série de classes com asserções que facilitam a vida de quem vai escrever os testes.

``Django TestCase é a classe que usamos para gerar subclasses de testes.`` Essa é a classe mais comum para ser utilizada em testes do Django porque ela herda de várias outras classes, tanto do Django quando da biblioteca padrão.

```python
from django.test import TestCase
```

``Test Fixtures são informações que dão suporte aos testes.`` Podem ser arquivos externos, bases de dados, dados, código Python e várias outras coisas.

Passos para criar um package do Python chamado tests, no lugar de tests.py.
- Criar uma pasta chamada "tests" dentro do app
- Criar um arquivo __init__.py dentro de tests.
- Criar arquivos test_``*``.py dentro de tests, com classes que herdam de TestCase, com métodos que iniciam com test``*``.
- Mover meus testes (caso existam) para os arquivos apropriados dentro de tests.
- Apagar o arquivo tests.py

> Sempre use o príncipio [Extract Method](https://refactoring.guru/pt-br/extract-method) na criação dos testes.

Funções para manipular URLs:
```python
from django.urls import resolve, reverse
```
reverse:

* pode ler uma string usando dados presentes em urls.py, como namespace e name das views;
* retorna uma string;

resolve:

* é usada para ler a URL e determinar a view correspondente;
* recebe uma string;

> Nunca teste de funcionalidades do Django, como o CRUD. Teste apenas a lógica da sua aplicação.

> Todos os testes devem ser isolados.

Os métodos setUp()e tearDown()permitem definir instruções que serão executadas antes e depois de cada método de teste.
```python
# Executado antes de cada teste
def setUp(self) -> None:
  return super().setUp()

# Executado depois de cada teste
def tearDown(self) -> None:
  return super().tearDown()
```

## MTV
[Model Template View](https://docs.djangoproject.com/en/4.1/faq/general/#faq-mtv)

## URLs
Para projetar URLs para uma aplicação, você cria um módulo Python que chamamos de URLconf. Uma tabela de conteúdos para sua aplicação, contendo um mapeamento entre padrões de URL e funções Python. URLconfs também servem para desacoplar as URLs do código Python.

## Views
Cada view é responsável por fazer uma entre duas coisas: Devolver um objeto HttpResponse contendo o conteúdo para a página requisitada, ou levantar uma exceção como Http404. O resto é com você.

Geralmente, uma view recupera dados de acordo com os parâmetros, carrega um template e renderiza o template com os dados recuperados.

## Templates
O Django tem um caminho de pesquisa para templates, o qual permite a você minimizar a redundância entre templates. Nas configurações do Django, você especifica uma lista de diretórios para procurar por templates em DIRS. Se um template não existir no primeiro diretório, ele verifica o segundo e assim por diante.

## Forms
No coração deste sistema de componentes está a classe Form do Django. Mais ou menos como o modelo do Django descreve a estrutura lógica de um objeto, seu comportamento, e a maneira como suas partes são apresentadas para nós, uma classe Form descreve um formulário e determina como este funciona e aparece.

De maneira similar que a classe de modelo mapeia os campos para o banco de dados, uma classe de formulário mapeia para os elementos <input> do formulário HTML. (Uma ModelForm mapeia os campos de uma classe de modelo para elementos <input> do formulário HTML através da Form; o admin do Django é baseado nisso.)

Os campos dos formulários são eles próprios classes; eles manipulam dados do formulário e fazem validações quando o formulário é enviado. A DateField e a FileField lidam com tipos de dados muito diferentes e tem que fazer coisas diferentes com isso.

Um campo de formulário, para o usuário, é representado como um “widget” HTML - um pedaço do maquinário de interface do usuário. Cada campo tem uma classe de Widget padrão, mas isso pode ser sobrescrito quando requerido.
