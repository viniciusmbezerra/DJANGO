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
Nós estamos usando autopep8 para formatar nosso código Python automaticamente ao salvar o arquivo, o flake8 para forçar um estilo de código em conformidade com a Python PEP8 e o mypy para forçar o uso correto da tipagem no Python.

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

Instalando django
```
pip install django
```
Gerenciando dependências
```
pip freeze
```

## URLs

## Views

## Templates