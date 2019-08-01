# Boilerplate de Django

Este projeto tem como finalidade acelerar o processo de criação de novos
backends baseados em django. O setup já inclui:

- [Django](https://docs.djangoproject.com/en/2.2/)
- [Django Rest Framework](https://www.django-rest-framework.org/)
- [Docker](https://docs.docker.com/)
- [Docker Compose](https://docs.docker.com/compose/)
- Postgres (Já conectado com Django)
- [Variáveis de ambiente](https://github.com/joke2k/django-environ)
- [CORS middleware](https://pypi.org/project/django-cors-headers/)
- [Django Rest Auth & AllAuth](https://django-rest-auth.readthedocs.io/)
- Módulo responsável por usuários

As explicações deste readme assumem que você tenha um conhecimento básico de
 Python, Django, Git e shell.

## Instalação
Para criar um novo projeto a partir deste, clone o repositório com
```
git clone --depth=1 --branch=master https://github.com/duiliofelix/django-template projectname
```
**Substituindo _projectname_ com o nome do seu projeto.**

Em seguida, remova o repositório antigo do git e crie um novo
```
cd projectname
rm -rf .git
git init
```

Copie o arquivo `templatesite/.env.example` para `templatesite/.env`. O
 arquivo `.env` é onde as variáveis de ambiente estão localizadas. **Ele não
 deve ser adicionado ao Git e já está no gitignore.** 

Substitua o valor da variável `SECRET_KEY` dentro do arquivo
 `templatesite/.env` por alguma outra string aleatória. A string em si não tem muita
 importância no ambiente de desenvolvimento, mas deve ser gerada com carinho
 para produção.
 
Substitua **todas** as ocorrências de templatesite pelo nome do seu novo
 projeto. Isso inclui nomes de diretórios e referencias em arquivos (Simples
 usando ctrl+shit+F na maioria das IDEs)
  
Por fim, compile e suba as imagens do docker (assumindo que você já tenha
 instalado ele e o docker-compose antes)
```
docker-compose build
docker-compose up
```
O container principal está com o nome `web`

## Executando a aplicação
Sempre que precisar rodar o seu código, basta rodar
```
docker-compose up
```
O comando `docker-compose build` só é necessário caso as dependências mudem.

### Rodando comandos do Django
Para rodar comandos do Django, como `makemigrations`, `migrate` ou outros,
 basta utilizar o `docker-compose run` no container `web`. Por exemplo, para
 rodar o `migrate`, utilize:
```
docker-compose run web python manage.py migrate
```

### Criando um novo app
Para criar um novo app, execute:
```
docker-compose run web python manage.py startapp nome_do_app
```
Este comando criará uma estrutura inicial para o novo app, mas além dela
, é necessário criar mais alguns arquivos seguindo as recomendações abaixo e,
mais importante, é necessário adicionar o novo app às configurações através
dos arquivos `settings.py` e `urls.py` do app principal.

## Recomendações
Aqui, segue uma breve descrição dos nossos valores quanto à forma recomendada de
 desenvolver dentro do ambiente django.

### Arquivos de IDE
Não deixe que arquivos de configuração da sua IDE entrem no versionamento do
 código. Além de não fazer parte do projeto, pode acabar desconfigurando a
 IDE de outro desenvolvedor. 

### Não crie apps excessivos
Os apps do Django são feitos para reutilizar código entre projetos, além de
 organizá-lo. Então cada app deve ser um "módulo" do programa, contendo vários
 models que estejam intrinsecamente conectados.
### Cada entidade fica em um arquivo
Em vez de colocar todas as classes de entidades de um app em um único arquivo
 `models.py`, crie um _package_ contendo cada entidade em um arquivo. Ex:
 Em um app `estoque` com as entidades `Produto` e `ImagemProduto`, a
  estrutura de arquivos ficaria da seguinte forma:
```
estoque/
 |- models/
  |- __init__.py
  |- produto.py
  |- imagem_produto.py  
```
E para possibilitar/facilitar a importação das classes o arquivo `__init__.py`
 teria o seguinte conteúdo:
```
# estoque/models/__init__.py

from .produto import Produto
from .imagem_produto import ImagemProduto
```

### Profile e afins ficam no app Users
Toda a estrutura relacionada ao cuidado com as contas, tais como `Profile`,
 `AccountData`, entidades de especificação e etc devem ficar juntas no app
 `users`.
 
### Arquivos típicos de todo app
A grande maioria dos apps se utilizam da mesma estrutura de arquivos, que
 é uma extensão da estrutura básica do Django. Os arquivos são:
 - **app.py**: Onde fica a configuração básica do app
 - **admin.py**: Onde devemos registrar as entidades no serviço de administração
  do Django
 - **signals.py**: Onde vão os _signals_ do Django. (Apenas inclua se for
  usar _signals_)
 - **serializers.py**: Onde colocamos os serializadores do Django Rest
  Framework para as entidades desse app
 - **urls.py**: Onde está a estrutura de roteamento **interna** do app
 - **views.py**: Onde estão as views no modelo do Django Rest Framework (pode
  ser separada em um package, assim como o `models` comentado acima).
 - **tests.py**: Onde ficam os testes unitários. (pode ser separada em um
  package, assim como o `models` comentado acima).
 - **migrations**: Diretório onde o Django cria as migrações.
 - **models**: Package de entidades do banco de dados.
 
 
 ## Utilidades
 ### Outras imagens do Docker
 Lembre-se de que graças ao Docker, podemos conectar facilmente o nosso
  ambiente a outros serviços. Um exemplo muito utilizado é o
  [Selenium](https://en.wikipedia.org/wiki/Selenium_(software)), que pode ser
  conectado através das
  [Imagens Oficiais](https://github.com/SeleniumHQ/docker-selenium).