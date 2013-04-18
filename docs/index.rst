.. Requests documentation master file, created by
   sphinx-quickstart on Sun Feb 13 23:54:25 2011.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Requests: HTTP para Humanos
=========================

Release v\ |version|. (:ref:`Instalação <install>`)

Requests é uma biblioteca HTTP :ref:`licensiada sob Apache2<apache2>`, escrita em Python, para seres humanos.

O módulo **urllib2**, parte da biblioteca padrão do Python, oferece a maioria das funcionalidades do protocolo HTTP que você precisa,
mas a API está completamente **quebrada**. Ela foi feita para uma época diferente — e uma web diferente. Ela demanda uma **enorme**
quantidade de trabalho (inclusive para sobrescrever métodos) para realizar as tarefas mais simples.

As coisas não deveriam ser desse jeito. Não em Python.

::

    >>> r = requests.get('https://api.github.com/user', auth=('user', 'pass'))
    >>> r.status_code
    200
    >>> r.headers['content-type']
    'application/json; charset=utf8'
    >>> r.encoding
    'utf-8'
    >>> r.text
    u'{"type":"User"...'
    >>> r.json()
    {u'private_gists': 419, u'total_private_repos': 77, ...}

Veja `um código similiar, sem Requests <https://gist.github.com/973705>`_.

Requests retira todo o trabalho do protocolo HTTP/1.1 do Python - deixando a integração dos seus serviços web sem problemas. Não há necessidade para adicionar query string nas suas URLs manualmente, ou de codificar seus dados de formulário POST. Keep-alive e gerência de conexões HTTP são 100% automáticas, feitas com `urllib3 <https://github.com/shazow/urllib3>`_, que está incluso no Requests.


Depoimentos
-----------------

Her Majesty's Government, Amazon, Google, Twilio, Mozilla, Heroku, PayPal, NPR, Obama for America, Transifex, Native Instruments, The Washington Post, Twitter, SoundCloud, Kippt, Readability, e Institutos Federais dos Estados Unidos usam Requests internamente. Ele já foi baixado mais de 2.000.000 de vezes pelo PyPI.

**Armin Ronacher**
    Requests é o exemplo perfeito de como uma API pode ser bonita com o nível certo de abstração.

**Matt DeBoard**
    Eu vou tatuar o módulo Python do @kennethreitz no meu corpo, de algum jeito. Ele todo.

**Daniel Greenfeld**
    Refiz uma biblioteca com 1200 linhas de código confusa com 10 linhas de código graças à biblioteca requests do @kennethreitz. Hoje foi SENSACIONAL.

**Kenny Meyers**
    HTTP em Python: Com dúvidas, sem dúvidas, use Requests. Bonito, simples, Pythônico.


Funcionalidades
---------------

Requests está pronto para a web de hoje.

- Domínios e URLs internacionais
- Keep-Alive e pooling de conexões
- Sessões com persistência de cookies
- Verificação SSL igual aos browsers
- Autenticação com métodos Basic/Digest
- Cookies com chave/valor elegantes
- Descompressão automática
- Corpos de resposta Unicode
- Uploads de arquivos
- Tempo máximo de conexões
- Suporte a ``.netrc``
- Python 2.6-3.3
- Thread-safe.


Guia do Usuário
-----------------

Esta parte da documentação, que é principalmente prosa, começa com algumas informações
sobre o Requests e então foca em instruções passo-a-passo para aproveitar o máximo do Requests.

.. toctree::
   :maxdepth: 2

   user/intro
   user/install
   user/quickstart
   user/advanced
   user/authentication


Guia da Comunidade
-----------------

Esta parte da documentação, que é principalmente prosa, detalha o ecossistema
e a comunidade do Requests.

.. toctree::
   :maxdepth: 1

   community/faq
   community/out-there.rst
   community/support
   community/updates

Documentação da API
-----------------

Se você estiver procurando por informações sobre uma função, classe ou método específico,
esta parte da documentação é para você.

.. toctree::
   :maxdepth: 2

   api


Guia de Contribuição
-----------------

Se você quiser contribuir para o projeto, esta parte da documentação é para você.

.. toctree::
   :maxdepth: 1

   dev/philosophy
   dev/internals
   dev/todo
   dev/authors
