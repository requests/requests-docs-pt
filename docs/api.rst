.. _api:

Interface de Desenvolvedor
==========================

.. module:: requests

Esta parte da documentação cobre todas as interfaces do Requests. Para partes
onde Requests depende de bibliotecas externas, nós documentamos o mais
imoprtante aqui e fornecemos links para a documentação oficial.


Interface principal
-------------------

Todas as funcionalidades de Requests podem ser acessadas por estes 7 métodos.
Todos retornam uma instância de :class:`Response <Response>`.

.. autofunction:: request

.. autofunction:: head
.. autofunction:: get
.. autofunction:: post
.. autofunction:: put
.. autofunction:: patch
.. autofunction:: delete


Classes baixo-nível
~~~~~~~~~~~~~~~~~~~

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: Response
   :inherited-members:

Sessões de requisição
---------------------

.. autoclass:: Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:


Exceções
~~~~~~~~

.. module:: requests

.. autoexception:: RequestException
.. autoexception:: ConnectionError
.. autoexception:: HTTPError
.. autoexception:: URLRequired
.. autoexception:: TooManyRedirects


Referência de código de status
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. autofunction:: requests.codes

::

    >>> requests.codes['temporary_redirect']
    307

    >>> requests.codes.teapot
    418

    >>> requests.codes['\o/']
    200

Cookies
~~~~~~~

.. autofunction:: dict_from_cookiejar
.. autofunction:: cookiejar_from_dict
.. autofunction:: add_dict_to_cookiejar


Codificações
~~~~~~~~~~~~

.. autofunction:: get_encodings_from_content
.. autofunction:: get_encoding_from_headers
.. autofunction:: get_unicode_from_response
.. autofunction:: decode_gzip


Classes
~~~~~~~

.. autoclass:: requests.Response
   :inherited-members:

.. autoclass:: requests.Request
   :inherited-members:

.. autoclass:: requests.PreparedRequest
   :inherited-members:

.. _sessionapi:

.. autoclass:: requests.Session
   :inherited-members:

.. autoclass:: requests.adapters.HTTPAdapter
   :inherited-members:


Migrando para 1.x
-----------------

Essa seção detalha as principais diferenças entre 0.x e 1.x e é feita para
facilitar a dor da atualização.


Mudanças na API
~~~~~~~~~~~~~~~

* ``Response.json`` agora é chamável e não uma propriedade de Response.

  ::

      import requests
      r = requests.get('https://github.com/timeline.json')
      r.json()   # Essa *chamada* levanta uma exceção quando a decodificação do JSON falha

* A API de ``Session`` mudou. Objetos Session não aceitam mais parâmetros.
  ``Session`` agora é capitalizado, mas ainda pode ser instanciado
  com o ``session`` minúsculo para retrocompatibilidade.

  ::

      s = requests.Session()    # originalmente, session recebia parâmetros
      s.auth = auth
      s.headers.update(headers)
      r = s.get('http://httpbin.org/headers')

* Todos os hooks de requisição foram removidos com exceção de 'response'.

* Helpers de autenticação foram quebrados em módulos separados. Veja
  requests-oauthlib_ and requests-kerberos_.

.. _requests-oauthlib: https://github.com/requests/requests-oauthlib
.. _requests-kerberos: https://github.com/requests/requests-kerberos

* O parâmetro para requisições de streaming oram mudados de ``prefetch`` para
  ``stream`` e a lógica foi invertida. Além disso, ``stream`` agora é exigido
  para leitura de resposta crua.

  ::

      # na 0.x, passando prefetch=False iria realizar a mesma coisa
      r = requests.get('https://github.com/timeline.json', stream=True)
      r.raw.read(10)

* O parâmetro ``config`` dos métodos de requisição foi removido. Algumas das
  opções agora são configuradas em um objeto ``Session`` como keep-alive e
  o número máximo de redirecionamentos. A opção de verbosidade deve ser manipulada
  pela configuração de logging.

  ::

      import requests
      import logging

      # essas duas linhas habilitam debugging no nível do httplib (requests->urllib3->httplib)
      # você verá o REQUEST, incluindo HEADERS e DATA, e RESPONSE com HEADERS mas sem DATA.
      # a única coisa faltando será o response.body que não será registrado.
      import httplib
      httplib.HTTPConnection.debuglevel = 1

      logging.basicConfig() # você precisa inicializar o logging, se não você não verá nada
      logging.getLogger().setLevel(logging.DEBUG)
      requests_log = logging.getLogger("requests.packages.urllib3")
      requests_log.setLevel(logging.DEBUG)
      requests_log.propagate = True

      requests.get('http://httpbin.org/headers')



Licensa
~~~~~~~

Uma diferença importante que não é relacionada com a API é a mudança da licensa
do ISC_ para a licensa `Apache 2.0`. A licensa Apache 2.0 garante que as contribuições
para requests também estão cobertas pela licensa Apache 2.0.

.. _ISC: http://opensource.org/licenses/ISC
.. _Apache 2.0: http://opensource.org/licenses/Apache-2.0

