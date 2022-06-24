<img src='https://static.djangoproject.com/img/logos/django-logo-negative.png' width='150' align='right'>
<h1> Sistema de Gerenciamento de Eventos desenvolvido com Django - Estudo</h1>
<br/>
<br/>

<h2>Qual versão do Python eu posso usar com Django?</h2>
<table>
<colgroup>
<col width="19%">
<col width="81%">
</colgroup>
<thead valign="bottom">
<tr class="row-odd"><th class="head">Versão do Django</th>
<th class="head">Versões do Python</th>
</tr>
</thead>
<tr class="row-even"><td>4.0</td>
<td>3.8, 3.9, 3.10</td>
</tr>
</tbody>
</table>

## Criação do diretório do repositório do projeto

> Crie o diretório  que servirá de contêiner para seu projeto. Com o nome que desejar e o acesse.
> Por Exemplo:

```
mkdir wttd
cd wttd
```

## Criação e ativação de um ambiente virtual

<img src='https://miro.medium.com/max/750/1*Z14kLUEB69SsYdNORxQzlw.jpeg' width='200' align='right'>

```
python -m venv .wttd
.wttd\Scripts\activate
```

## Instalação do Django

```
pip install django
```

## Criação do projeto django eventex

```
django-admin startproject eventex .


wttd\
    manage.py
    eventex\
        __init__.py
        settings.py
        urls.py
        asgi.py
        wsgi.py

```
> O diretório eventex\ é o pacote Python para o projeto.

## Criaçao de um alias do manage.py

> Dentro do diretório .wttd\Scripts\
> Criar um arquivo manage.bat contendo o seguinte comando:
```
@python "%VIRTUAL_ENV%\..\manage.py" %*
```

## Executar o servidor runserver
```
manage runserver 
```

## Acessar o diretório Eventex e criar a primeira aplicação do Django

```
cd eventex     
manage startapp core

core\
    __init__.py
    admin.py
    apps.py
    migrations\
        __init__.py
    models.py
    tests.py
    views.py
```
> Cada aplicação que você escreve no Django consiste de um pacote Python que segue uma certa convenção.
> O Django possui um utilitário que gera automaticamente a estrutura básica de diretório de uma aplicação.
 
## Configurar o arquivo settings.py com a app core

```
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'eventex.core',
]
```
## Atualizar rotas no arquivo urls.py

```
from django.contrib import admin
from django.urls import path
import eventex.core.views

urlpatterns = [
    path('', eventex.core.views.home),
    path('admin/', admin.site.urls),
]
```

## Criar função home no arquivo core/views.py

```
from django.shortcuts import render

def home(request):
    return render(request, 'index.html')
```

## Criar os diretórios templates\ e static\ dentro de core\

* As pastas de css, fonts, js e img ficam dentro de static/
* O html fica na pasta templates/

## Template tag {% load static %}

* No início do arquivo htlm inserir o template tag do django
* Substituir os caminhos dos arquivos estáticos
```
<script src = "{% static 'js/main.js' %}"></script>
```
# Fazendo deploy em produção

<img src='https://www3.assets.heroku.com/assets/home/hero/focus-647c57d2effb7d2dfb5871161afab3cf097de6339c02e85d84ea14747800fcb0.png' width='200' align='right'>

* Criar uma conta no https://www.heroku.com
* Instalar o Heroku CLI: https://devcenter.heroku.com/articles/heroku-cli
  * ```Conforme o seu sistema operacional ```
* Verificar a instalação ```$ heroku --version```
* Fazer login no heroku via Heroku CLI ```$ heroku login```
* Será aberto um navegador para digitar usuário e senha

## Instalação do python decouple

A biblioteca python-decouple vai ajudar gerenciar as configurações de ambientes.

```pip install python-decouple```

* Importar o módulo decouple no settings.py

```from decouple import config```

## Configurar as variáveis de ambiente:

* Criar o arquivo .env na raiz do repositório (wttd)
* Copiar os valores de SECRET_KEY e DEBUG do settings.py originais no arquivo .env, retirando os espaços antes e após o sinal de igual
* Alterar as variáveis do settings.py 
```
SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', default=False, cast=bool)
```

## Instalação do dj-database-url

```pip install dj-database-url```

* Importar o módulo decouple no settings.py

```from dj_database_url import parse as dburl```

* Alterar a configuração do settings.py

```

default_dburl = 'sqlite:///' + str(BASE_DIR / 'db.sqlite3')

DATABASES = {
	'default': config('DATABASE_URL', default=default_dburl, cast=dburl),
}
```

## Outras configurações do settings.py

```
ALLOWED_HOSTS = ['*']
...
STATIC_URL = 'static/'
STATIC_ROOT = str(BASE_DIR / 'staticfiles')
```

* Instalar o dj-static
```
pip install dj-static
```

* Configurar o arquivo wsgi.py

```
import os
from dj_static import Cling
from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'eventex.settings')

application = Cling(get_wsgi_application())
```

## Gerar o requirements.txt

```pip freeze > requirements.txt```

* Acrescentar dois módulos no requirements.txt
```
gunicorn==20.1.0
psycopg2-binary==2.9.3 
```
## Criar o arquivo Procfile na raiz do repositório

* Adicionar instruções para o heroku
> web: gunicorn eventex.wsgi --log-file -

## Criar arquivo git dentro do diretório do projeto(wttd) e verificar status

```
git init
git status
```

## Adicionar todos os arquivos ao git
```
git add .
```

## Commitar arquivos

```git commit -m "Import project"```


## Enviar projeto para ambiente de produção do heroku

```
heroku apps:create eventex-seunome
```

## Verificar se o repositório git reconhece o endereço do heroku 

```
git remote -v
```

## Abrir heroku no browser

```
heroku open         
```

## Envio de variáveis de ambiente para o heroku

* Capturar as variáveis de ambiente local

```
cat .env
```
> Será exibido:
> SECRET_KEY='sua-chave-secreta'
> DEBUG=True

* Configurar a SECRET_KEY
> No ambiente linux:
```
heroku config:set SECRET_KEY='chave secreta'
```
> No ambiente Windows(Setting environment variable in Heroku (via PowerShell) with special characters):
```
& 'heroku' @('config:set', 'SECRET_KEY=chave secreta escapando com aspas duplas caracteres especiais: ")" "&" "^"')
```
* Configurar o DEBUG
```
heroku config:set DEBUG=True
```


<img src='https://pngimg.com/uploads/rockets/rockets_PNG101062.png' width='100' align='right'>
<h2>Realizar deploy</h2>

```
git push heroku main --force 
```
