.. _quickstart:

Guia de início rápido
=====================

.. module:: requests.models

Ansioso para começar? Esta página dá uma boa introdução em como começar a usar
Requests. Ela assume que você já tem Requests instalado. Se você não tem, veja
a seção sobre :ref:`Instalação <install>`.

Primeiramente, certifique-se de que:

* Requests está :ref:`instalado <install>`
* Requests está :ref:`atualizado <updates>`


Vamos começar com alguns exemplos simples.


Fazer uma requisição
--------------------

Fazer uma requisição com Requests é muito simples.

Comece importando o módulo Requests::

    >>> import requests

Agora, vamos tentar baixar uma página da web. Para este exemplo, vamos baixar
a timeline pública do GitHub::

    >>> r = requests.get('https://github.com/timeline.json')

Agora, nós temos um objeto :class:`Response` chamado ``r``. Nós podemos
pegar todas as informações que precisamos desse objeto.

A API simples de Requests significa que todas as formas de requisição HTTP
são óbvias. Por exemplo, este é o jeito que se faz uma requisição HTTP POST::

    >>> r = requests.post("http://httpbin.org/post")

Legal, né? Que tal outros tipos de requisições HTTP: PUT, DELETE, HEAD  e OPTIONS?
São todas igualmente simples::

    >>> r = requests.put("http://httpbin.org/put")
    >>> r = requests.delete("http://httpbin.org/delete")
    >>> r = requests.head("http://httpbin.org/get")
    >>> r = requests.options("http://httpbin.org/get")

Está tudo muito bem, mas também é só o começo do que Requests pode
fazer.


Passando parâmetros em URLs
---------------------------

Você geralmente quer mandar algum tipo de dado na query string da URL. Se
você estivesse construindo a URL manualmente, este dado seria dado como pares
chave/valor na URL após um ponto de interrogação, por exemplo ``httpbin.org/get?key=val``.
Requests permite que você forneça estes argumentos como um dicionário, usando
o argumento ``params``. Por exemplo, se você quisesse passar ``key1=vaue1`` e
``key2=value2`` para ``httpbin.org/get``, você usaria o seguinte
código::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.get("http://httpbin.org/get", params=payload)

Você pode ver que a URL foi corretamente gerada imprimindo a URL::

    >>> print r.url
    u'http://httpbin.org/get?key2=value2&key1=value1'


Conteúdo da resposta
--------------------

Nós podemos ler o conteúdo da resposta do servidor. Considerando a timeline
do GitHub novamente::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.text
    '[{"repository":{"open_issues":0,"url":"https://github.com/...

Requests irá automaticamente decodificar o conteúdo do servidor. A maioria
dos conjuntos de caracteres unicode são decodificados nativamente.

Quando você faz uma requisição, Requests advinha qual a codificação da reposta
baseada nos cabeçalhos HTTP. A codificação de texto advinhada por Requests é
usada quando você acessa ``r.text``. Você pode descobrir qual codificação Requests
está usando, e mudá-la, utilizando a propriedade ``r.enconding``::

    >>> r.encoding
    'utf-8'
    >>> r.encoding = 'ISO-8859-1'

Se você mudar a codificação, Requests irá usar o novo valor de ``r.enconding``
sempre que você chamar ``r.text``.

Requests também usará codificações personalizadas na ocasião de você precisar delas.
Se você criou sua própria codificação e a registrou com o módulo ``codecs``,
você pode simplesmente usar o nome do codec como valor de ``r.enconding `` e
Requests irá cuidar da decodificação para você.

Resposta binária
----------------

Você pode acessar o corpo da resposta como bytes, para requisições que não são textos::

    >>> r.content
    b'[{"repository":{"open_issues":0,"url":"https://github.com/...

As codificações de transferências ``gzip`` e ``deflate`` são decodificadas automaticamente para você.

Por exemplo, para criar uma imagem de dados binários retornados por uma requisição,
você pode usar o seguinte código:

    >>> from PIL import Image
    >>> from StringIO import StringIO
    >>> i = Image.open(StringIO(r.content))


Resposta JSON
------------

Também existe um decodificador JSON integrado, no caso de você lidar com dados JSON::

    >>> import requests
    >>> r = requests.get('https://github.com/timeline.json')
    >>> r.json()
    [{u'repository': {u'open_issues': 0, u'url': 'https://github.com/...

No caso da decodificação do JSON falhar, ``r.json`` levanta uma exceção. Por exemplo,
se a resposta tem código 401 (Não autorizado), tentar usar ``r.json`` levanta
``Value Error: No JSON objecto could be decoded``


Resposta crua
-------------

No caso raro de você querer recuperar a resposta crua do socket vinda
do servidor, você pode acessar ``r.raw``. Se você quiser fazer isto,
certifique-se de que você definiu ``stream=True`` na sua requisição inicial.
Uma vez que você fizer, poderá utilizar::

    >>> r = requests.get('https://github.com/timeline.json', stream=True)
    >>> r.raw
    <requests.packages.urllib3.response.HTTPResponse object at 0x101194810>
    >>> r.raw.read(10)
    '\x1f\x8b\x08\x00\x00\x00\x00\x00\x00\x03'


Cabeçalhos personalizados
-------------------------

Se você quiser adicionar cabeçalhos HTTP para uma requisição, simplesmente
passe-os em um ``dict`` para o parâmetro ``headers``.

Por exemplo, nós não especificamos o content-type no exemplo anterior::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}
    >>> headers = {'content-type': 'application/json'}

    >>> r = requests.post(url, data=json.dumps(payload), headers=headers)


Requisições POST mais complicadas
---------------------------------

Tipicamente, você quer enviar algum dado em forma de formulário - assim
como um formulário HTML. Para fazer isto, simplesmente passe um dicionário
para o argumento ``data``. O seu dicionário de dados será automaticamente
formatado como formulário e a requisição é feita::

    >>> payload = {'key1': 'value1', 'key2': 'value2'}
    >>> r = requests.post("http://httpbin.org/post", data=payload)
    >>> print r.text
    {
      ...
      "form": {
        "key2": "value2",
        "key1": "value1"
      },
      ...
    }

Existem várias ocasiões que você quer enviar dados que não estejam como formulário. Se você passar uma ``string`` em vez de um ``dict``, o dado será enviado diretamente.

Por exemplo, a API v3 do GitHub aceita dados codificados como JSON em POST/PATCH::

    >>> import json
    >>> url = 'https://api.github.com/some/endpoint'
    >>> payload = {'some': 'data'}

    >>> r = requests.post(url, data=json.dumps(payload))


POST de arquivo Multipart
-------------------------

Requests simplifica o upload de arquivos codificados Multipart::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': open('report.xls', 'rb')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<dados...binários...censurados>"
      },
      ...
    }

Você pode definir o nome do arquivo explicitamente::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.xls', open('report.xls', 'rb'))}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "<dados...binários...censurados>"
      },
      ...
    }

Se você quiser, você pode enviar strings para serem recebidas como arquivos::

    >>> url = 'http://httpbin.org/post'
    >>> files = {'file': ('report.csv', 'some,data,to,send\nanother,row,to,send\n')}

    >>> r = requests.post(url, files=files)
    >>> r.text
    {
      ...
      "files": {
        "file": "algum,dado,para,enviar\\noutra,linha,para,enviar\\n"
      },
      ...
    }


Código do status da resposta
----------------------------

Você pode verificar o código do status da resposta::

    >>> r = requests.get('http://httpbin.org/get')
    >>> r.status_code
    200

Requests também vem com um objeto de referência de códigos de status
integrado::

    >>> r.status_code == requests.codes.ok
    True

Se nós fizermos uma requisição mal-feita (resposta que não tenha código 200),
podemos levantar uma exceção usando :class:`Response.raise_for_status()`::

    >>> bad_r = requests.get('http://httpbin.org/status/404')
    >>> bad_r.status_code
    404

    >>> bad_r.raise_for_status()
    Traceback (most recent call last):
      File "requests/models.py", line 832, in raise_for_status
        raise http_error
    requests.exceptions.HTTPError: 404 Client Error

Mas, já que nosso ``status_code`` para ``r`` foi ``200``, quando nós chamamos
``raise_for_status()``, recebemos::

    >>> r.raise_for_status()
    None

Está tudo bem


Cabeçalhos da resposta
----------------------

Nós podemos visualizar os cabeçalhos da resposta do servidor usando um dicionário Python::

    >>> r.headers
    {
        'content-encoding': 'gzip',
        'transfer-encoding': 'chunked',
        'connection': 'close',
        'server': 'nginx/1.0.4',
        'x-runtime': '148ms',
        'etag': '"e1ca502697e5c9317743dc078f67693f"',
        'content-type': 'application/json; charset=utf-8'
    }

No entanto, o dicionário é especial: ele é feito sometendo para cabeçalhos HTTP.
De acordo com a `RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html>`_,
cabeçalhos HTTP são case-insensitive.

Assim, nós podemos acessar os cabeçalhos usando qualquer capitalização que quisermos::

    >>> r.headers['Content-Type']
    'application/json; charset=utf-8'

    >>> r.headers.get('content-type')
    'application/json; charset=utf-8'

Se um cabeçalho não existe no objeto Response, seu valor padrão é ``None``::

    >>> r.headers['X-Random']
    None


Cookies
-------

Se uma resposta contém alguns cookies, você tem acesso rápido a eles::

    >>> url = 'http://example.com/some/cookie/setting/url'
    >>> r = requests.get(url)

    >>> r.cookies['example_cookie_name']
    'example_cookie_value'

Para enviar seus próprios cookies par ao servidor, você pode usar o
parâmetro ``cookies``::

    >>> url = 'http://httpbin.org/cookies'
    >>> cookies = dict(cookies_are='working')

    >>> r = requests.get(url, cookies=cookies)
    >>> r.text
    '{"cookies": {"cookies_are": "working"}}'


Redirecionamento e Histórico
----------------------------

Requests irá automaticamente realizar redirecionamentos quando utilizados
os verbos GET e OPTIONS.

GitHub redireciona todas as requisições HTTP para HTTPS. Nós podemos usar o método ``history``
do objeto Response para acompanhar o redirecionamento. Vamos ver o que o GitHub faz::

    >>> r = requests.get('http://github.com')
    >>> r.url
    'https://github.com/'
    >>> r.status_code
    200
    >>> r.history
    [<Response [301]>]

A lista :class:`Response.history` contém uma lista de objetos
:class:`Request` que foram criados para completar a requisição. A lista é ordenada da requisição mais antiga para a mais nova.

Se você estiver usando GET ou OPTIONS, você pode desabilitar o redirecionamento com o
parâmetro ``allow_redirects``::

    >>> r = requests.get('http://github.com', allow_redirects=False)
    >>> r.status_code
    301
    >>> r.history
    []


Se você estiver usando POST, PUT, PATCH, DELETE ou HEAD, você pode
habilitar o redirecionamento também::

    >>> r = requests.post('http://github.com', allow_redirects=True)
    >>> r.url
    'https://github.com/'
    >>> r.history
    [<Response [301]>]


Timeouts
--------

Você pode dizer para as requisições pararem de esperar por uma resposta depois de um
dado número de segundos com o parâmetro ``timeout``::

    >>> requests.get('http://github.com', timeout=0.001)
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
    requests.exceptions.Timeout: HTTPConnectionPool(host='github.com', port=80): Request timed out. (timeout=0.001)


.. admonition:: Note:

    ``timeout`` somente afeta o processo da conexão, não o download
    do corpo da resposta.


Erros e Exceções
----------------

Na ocasião de um problema de rede (por exemplo, falha de DNS, conexão recusada, etc.),
Requests irá levantar uma exceção :class:`ConnectionError`.

Na ocasião de uma rara resposta HTTP inválida, Requests irá levantar uma
exceção :class:`HTTPError`.

Se uma requisição excede o tempo limite, uma exceção :class:`Timeout` é levantada.

Se uma requisição excede o número máximo de redirecionamentos configurado, uma exceção
:class:`TooManyRedirects` é levantada.

Todas as exceções levantadas explicitamente pelo Requests são herdadas de
:class:`requests.exceptions.RequestException`.

-----------------------

Ready for more? Check out the :ref:`advanced <advanced>` section.
