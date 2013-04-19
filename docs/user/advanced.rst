.. _advanced:

Uso avançado
============

Este documento cobre algumas funcionalidades mais avançadas do Requests.


Objetos Session
---------------

O objeto Session permite você persistir alguns parâmetros através de
requisições. Ele também permite persistência de cookies através de todas
as requisições feitas a partir de uma instância de Session.

Um objeto de sessão contém todos métodos da API principal de Requests.

Vamos persistir alguns cookies através de requisições::

    s = requests.Session()

    s.get('http://httpbin.org/cookies/set/sessioncookie/123456789')
    r = s.get("http://httpbin.org/cookies")

    print r.text
    # '{"cookies": {"sessioncookie": "123456789"}}'


Sessões também podem ser usadas para prover dados padrões para os métodos de requisição.
Isto é feito fornecendo dados para as propriedades de um objeto session::

    s = requests.Session()
    s.auth = ('user', 'pass')
    s.headers.update({'x-test': 'true'})

    # ambos 'x-test' e 'x-test2' são enviados
    s.get('http://httpbin.org/headers', headers={'x-test2': 'true'})


Quaisquer dicionários que você passar para um método de requisição será usado em conjuntos com os valores determinados no nível da sessão. Os parâmetros no nível do método sobrescrevem quaisquer parâmetros de sessão.

.. admonition:: Remover um valor de um parâmetro de dicionário

    Algumas vezes você gostaria omitir chaves do dicionário da sessão. Para fazer isto, basta definir o valor desta chave como ``None`` no parâmetro do método. Ele será omitido automaticamente.

Todos os valores contidos dentro de uma sessão estão disponíveis diretamente para você. Veja a :ref:`documentação da API Session <sessionapi>` para saber mais.

Objetos Request e Response
--------------------------

Sempre que uma chamada é feita para requests.*() você está fazendo basicamente duas coisas.
Primeiro, você está construindo um objeto ``Request`` que será enviado para um servidor
para realizar a requisição de algum recurso. Segundo, um objeto ``Response`` é gerado
assim que ``requests`` obtém uma resposta do servidor. O objeto de resposta contém
todas as informações retornadas pelo servidor e também contém o objeto ``Request``
que você criou primeiro. Aqui está uma simples requisição para pegar alguma
informação importante dos servidores da Wikipedia::

    >>> r = requests.get('http://en.wikipedia.org/wiki/Monty_Python')

Se quisermos acessar os cabeçalhos enviados pelo servidor, nós fazemos isso::

    >>> r.headers
    {'content-length': '56170', 'x-content-type-options': 'nosniff', 'x-cache':
    'HIT from cp1006.eqiad.wmnet, MISS from cp1010.eqiad.wmnet', 'content-encoding':
    'gzip', 'age': '3080', 'content-language': 'en', 'vary': 'Accept-Encoding,Cookie',
    'server': 'Apache', 'last-modified': 'Wed, 13 Jun 2012 01:33:50 GMT',
    'connection': 'close', 'cache-control': 'private, s-maxage=0, max-age=0,
    must-revalidate', 'date': 'Thu, 14 Jun 2012 12:59:39 GMT', 'content-type':
    'text/html; charset=UTF-8', 'x-cache-lookup': 'HIT from cp1006.eqiad.wmnet:3128,
    MISS from cp1010.eqiad.wmnet:80'}

Contudo, se quisermos acessar os cabeçalhos que nós mandamos para o servidor,
nós simplesmente acessamos a requisição e, então, os cabelhos da requisição::

    >>> r.request.headers
    {'Accept-Encoding': 'identity, deflate, compress, gzip',
    'Accept': '*/*', 'User-Agent': 'python-requests/1.2.0'}

Requisições preparadas
----------------------

Sempre que você recebe um objeto :class:`Response <requests.models.Response>` de
uma chamada da API ou de Session, o atributo ``request`` é, na verdade, o objeto
``PreparedRequest`` que foi utilizado. Em alguns casos, você pode querer realizar
algum trabalho extra no corpo ou nos cabeçalhos (ou qualquer outra coisa, na verdade)
antes de enviar uma requisição. A receita simples para isto é a seguinte::

    from requests import Request, Session

    s = Session()
    prepped = Request('GET',  # ou qualquer outro método, 'POST', 'PUT', etc.
                      url,
                      data=data
                      headers=headers
                      # ...
                      ).prepare()
    # faça algo com prepped.body
    # faça algo com prepped.headers
    resp = s.send(prepped,
                  stream=stream,
                  verify=verify,
                  proxies=proxies,
                  cert=cert,
                  timeout=timeout,
                  # etc.
                  )
    print(resp.status_code)

Como você não está fazendo nada de especial com o objeto ``Request``, você
o preparou imediatamente e modificou o objeto ``PreparedRequest``. Então,
este com os outros parâmetros que você mandaria com ``requests.*`` ou ``Session.*``.

Verificação de certificado SSL
------------------------------

Requests pode verificar certificados SSL para requisições HTTPS, assim como um navegador. Para conferir um certificado SSL de um host, você usar o argumento ``verify``::

    >>> requests.get('https://kennethreitz.com', verify=True)
    requests.exceptions.SSLError: hostname 'kennethreitz.com' doesn't match either of '*.herokuapp.com', 'herokuapp.com'

Eu não tenho configuração SSL neste domínio, então ele falha. Excelente. GitHub tem, no entanto::

    >>> requests.get('https://github.com', verify=True)
    <Response [200]>

Você também pode passar para ``verify`` o caminho para um arquivo CA_BUNDLE para certificados privados. Você também pode definir a variável de ambiente ``REQUESTS_CA_BUNDLE``.

Requests também pode ignorar a verificação do certificado SSL se você definir ``verify`` como False.

::

    >>> requests.get('https://kennethreitz.com', verify=False)
    <Response [200]>

Por padrão, ``verify`` está definido como True. A opção ``verify`` é somente aplicada para certificados de hosts.

Você também pode especificar um certificado local para ser usado como certificado do lado do cliente, como um único arquivo (contendo a chave privada e o certificado) ou como uma tupla do caminho de ambos arquivos::

    >>> requests.get('https://kennethreitz.com', cert=('/path/server.crt', '/path/key'))
    <Response [200]>

Se você especificar um caminho errado ou um certificado inválido::

    >>> requests.get('https://kennethreitz.com', cert='/wrong_path/server.pem')
    SSLError: [Errno 336265225] _ssl.c:347: error:140B0009:SSL routines:SSL_CTX_use_PrivateKey_file:PEM lib


Fluxo do conteúdo do corpo
--------------------------

Por padrão, quando você faz uma requisição, o corpo da resposta é baixado imediatamente. Você pode mudar este comportamento e prevenir o download do corpo da resposta até que você acesse o atribute :class:`Response.content` com o parâmetro ``stream``::

    tarball_url = 'https://github.com/kennethreitz/requests/tarball/master'
    r = requests.get(tarball_url, stream=True)

Neste ponto, apenas os cabeçalhos das respostas forão baixados e a conexão continua aberta, nos permitindo a recuperação condicional do conteúdo::

    if int(r.headers['content-length']) < TOO_LONG:
      content = r.content
      ...

Você pode então controlar o fluxo com o uso dos métodos :class:`Response.iter_content` e :class:`Response.iter_lines`, ou lendo do objeto subjacente da urllib3 :class:`urllib3.HTTPResponse` em :class:`Response.raw`.


Keep-Alive
----------

Excelente novidades - graças à urllib3, keep-alive é 100% automático dentro de uma sessão! Todas as requisições que forem feitas dentro de uma sessão irão automaticamente reusar a conexão apropriada!

Note que conexões são somente liberadas de volta para o pool para serem reusadas uma vez que você todos os dados do corpo foram lidos; certifique-se de definir ``stream`` para ``False`` ou de ler a propriedade ``content`` do objeto ``Response``.


Uploads com Streaming
---------------------

Requests suporta uploads com streaming, que permitem que você mande grandes streams ou arquivos sem precisar carregá-los na memória. Para stream e upload, simplesmente forneça um objeto do tipo de arquivo para o corpo::

    with open('massive-body') as f:
        requests.post('http://some.url/streamed', data=f)


Requisições com blocos codificados
----------------------------------

Requests também suporta codificação de transferência em bloco para requisições de saída e de entrada. Para mandar uma requisição com um bloco codificado, simplesmente forneça um gerador (ou qualquer iterador sem tamanho) para o corpo::


    def gen():
        yield 'olá'
        yield 'você'

    requests.post('http://some.url/chunked', data=gen())


Hooks de eventos
----------------

Requests tem um sistema de hooks que você pode usar para manipular
pedaços do processo de requisição, ou tratamento de eventos de sinais.

Hooks disponíveis:

``response``:
    A resposta gerada por um objeto Request.

Você pode atribuir uma função de hook para cada request passando um
dicionário ``{hook_name: callback_function}`` para o parâmetro ``hooks``
do método de requisição::

    hooks=dict(response=print_url)

A ``callback_function`` irá receber um bloco de dado como seu primeiro argumento.

::

    def print_url(r):
        print(r.url)

Se ocorrer um erro durante a execução do seu callback, é dado um warning.

Se a função de callback retorna um valor, assume-se que este substitui os dados
que foram passados. Se a função não retorna nada, nada mais é afetado.

Vamos imprimir alguns argumentos do método de requisição em tempo de execução::

    >>> requests.get('http://httpbin.org', hooks=dict(response=print_url))
    http://httpbin.org
    <Response [200]>


Autenticação personalizada
--------------------------

Requests permite que você especifique seu próprio mecanismo de autenticação.

Todo objeto executável que é passado como argumento ``auth`` para um método de
requisição terá a oportunidade de modificar a requisição antes que ela seja enviada.

Implementações de autenticação são subclasses de ``requests.auth.AuthBase``,
e são fáceis de se definir. Requests fornece duas implementações comuns de esquemas
de autenticação em ``requests.auth``: ``HTTPBasicAuth`` e ``HTTPDigestAuth``.

Vamos supor que temos um serviço web que irá somente responder se o cabeçalho
``X-Pizza`` for definido para um valor de senha. Improvável, mas vamos continuar assim.

::

    from requests.auth import AuthBase

    class PizzaAuth(AuthBase):
        """Inclui autenticação HTTP Pizza para o dado objeto Request."""
        def __init__(self, username):
            # configura qualquer dado relacionado a autenticação aqui
            self.username = username

        def __call__(self, r):
            # modifica e retorna a requisição
            r.headers['X-Pizza'] = self.username
            return r

Então, nós podemos fazer uma requisição utilizando a nossa autenticação Pizza::

    >>> requests.get('http://pizzabin.org/admin', auth=PizzaAuth('kenneth'))
    <Response [200]>

Requisições de Streaming
------------------------

Com ``requests.Response.iter_lines()`` você pode facilmente iterar por APIs de streaming
como a `API de Streaming do Twitter <https://dev.twitter.com/docs/streaming-api>`_.

Para usar API de Streaming do Twitter para monitorar a palavra "requests"::

    import json
    import requests

    r = requests.post('http://httpbin.org/stream/20', stream=True)

    for line in r.iter_lines():

        # filtrar novas linhas de keep-alive
        if line:
            print json.loads(line)


Proxies
-------

Se você precisar um proxy, você pode configurar requisições individuais
com o argumento ``proxies`` de qualquer método de requisição::

    import requests

    proxies = {
      "http": "http://10.10.1.10:3128",
      "https": "http://10.10.1.10:1080",
    }

    requests.get("http://example.org", proxies=proxies)

Você também pode configurar proxies com as variáveis de ambiente ``HTTP_PROXY`` e ``HTTPS_PROXY``.

::

    $ export HTTP_PROXY="http://10.10.1.10:3128"
    $ export HTTPS_PROXY="http://10.10.1.10:1080"
    $ python
    >>> import requests
    >>> requests.get("http://example.org")

Para usar autenticação básica HTTP com seu proxy, use a síntaxe `http://user:password@host/`::

    proxies = {
        "http": "http://user:pass@10.10.1.10:3128/",
    }

Concordância
------------

Requests tem intenção de estar de acordo com todas as especificações relevantes
e RFCs onde está concordância não causar dificuldades para os usuários. Esta
atenção para a especificação pode levar a alguns comportamentos que podem parecer
incomuns para aqueles não familiarizados com a especificação relevante.

Codificações
^^^^^^^^^^^^

Quando você recebe uma resposta, Requests tenta adivinhar a codificação a ser usada
quando decodificar a resposta quando você chamar o método ``Response.text``. Requests
irá primeiro checar por uma codificação nos cabeçalhos HTTP, e se não houver nenhuma presente,
irá usar `charade <http://pypi.python.org/pypi/charade>`_ para tentar adivinhar
a codificação.

A única vez que Requests não fará isso é nenhum conjunto de caracteres explícito
estiver presente nos cabeçalhos HTTP **e** o cabeçalho ``Content-Type`` contiver
``text``. Nesta situação, a
`RFC 2616 <http://www.w3.org/Protocols/rfc2616/rfc2616-sec3.html#sec3.7.1>`_
especifica que o conjunto de caracteres padrão deve ser ``ISO-8859-1``. Requests
segue a especificação neste caso. Se você precisa de uma codficação diferente,
você pode definir manualmente a propriedade ``Response.encoding`` ou usar a
resposta crua com ``Response.content``.

Verbos HTTP
-----------

Requests fornece acesso para praticamente todos os verbos HTTP: GET, OPTIONS,
HEAD, POST, PUT, PATCH e DELETE. A seguir encontram-se exemplos detalhados
de como usar estes vários verbos em Requests, usando a API do GitHub.

Nós começaremos com o verbo mais comumente usado: GET. HTTP GET é um método
idempotente que retorna um recurso de uma URL dada. Como resultado, é o
verbo que você deve usar quando tentar buscar dados de um local na web. Um
exemplo de uso seria tentar pegar informações sobre um commit específico
go GitHub. Supondo que queremos ver o commit ``a050af`` de Requests.
Nós faríamos de tal forma::

    >>> import requests
    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/git/commits/a050faf084662f3a352dd1a941f2c7c9f886d4ad')

Nós devemos confirmar que o GitHub respondeu corretamente. Se respondeu, nós
queremos verificar o tipo de conteúdo respondido. Fazendo da seguinte forma::

    >>> if (r.status_code == requests.codes.ok):
    ...     print r.headers['content-type']
    ...
    application/json; charset=utf-8

Então, GitHub retorna JSON. Isto é ótimo, nós podemos usar o método ``r.json``
para intepretá-lo como objetos Python.

::

    >>> commit_data = r.json()
    >>> print commit_data.keys()
    [u'committer', u'author', u'url', u'tree', u'sha', u'parents', u'message']
    >>> print commit_data[u'committer']
    {u'date': u'2012-05-10T11:10:50-07:00', u'email': u'me@kennethreitz.com', u'name': u'Kenneth Reitz'}
    >>> print commit_data[u'message']
    makin' history

Por enquanto, tudo simples. Bem, vamos investigar um pouco a API do GitHub. Agora,
poderíamos olhar a documentação, mas nós vamos nos divertir um pouco mais
usando Requests. Nós podemos tirar proveito do verbo OPTIONS do Requests para ver
que tipos de métodos HTTP são suportados pelo verbo na URL que acabamos de usar.

::

    >>> verbs = requests.options(r.url)
    >>> verbs.status_code
    500

Hein, o que? Isso não ajuda nada! Aparentemente, GitHub, como muitos outras APIs,
não implementam de fato o método OPTIONS. Isso é um descuido que incomoda,
mas tudo bem, nós podemos simplesmente usar a documentação chata. Se o GitHub
tivesse implementado OPTIONS corretamente, eles deverião retornar os métodos
permitidos nos cabeçalhos, por exemplo

::

    >>> verbs = requests.options('http://a-good-website.com/api/cats')
    >>> print verbs.headers['allow']
    GET,HEAD,POST,OPTIONS

Observando a documentação, nós podemos ver que o único método permitido para
commits é POST, que cria um novo commit. Como nós estamos o repositório do Requests,
nós provavelmente deveríamos evitar fazer POSTS descuidados nele. Ao invés disso,
vamos brincar com a funcionalidade de Issues do GitHub.

Essa documentação foi adicionada em resposta a Issue #482. Dado que esta issue
já existe, nós a usaremos como exemplo. Começaremos buscando-a.

::

    >>> r = requests.get('https://api.github.com/repos/kennethreitz/requests/issues/482')
    >>> r.status_code
    200
    >>> issue = json.loads(r.text)
    >>> print issue[u'title']
    Feature any http verb in docs
    >>> print issue[u'comments']
    3

Legal, temos 3 comentários. Vamos dar uma olhada no último deles.

::

    >>> r = requests.get(r.url + u'/comments')
    >>> r.status_code
    200
    >>> comments = r.json()
    >>> print comments[0].keys()
    [u'body', u'url', u'created_at', u'updated_at', u'user', u'id']
    >>> print comments[2][u'body']
    Probably in the "advanced" section

Bem, esse comentário é meio bobo. Vamos postar um comentário contando ao
autor que ele é bobo. Quem é o autor, mesmo?

::

    >>> print comments[2][u'user'][u'login']
    kennethreitz

Tudo bem, então falaremos para esse tal Kenneth que achamos que esse exemplo
deveria aparecer no guia de início rápido. De acordo com a documentação
da API do GitHub, o jeito de fazer isso é fazer um POST no tópico. Faremos isto.

::

    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it!"})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/482/comments"
    >>> r = requests.post(url=url, data=body)
    >>> r.status_code
    404

Hm, isso é estranho. Provavelmente precisamos nos autenticar. Isso vai ser chato, né?
Errado. Requests deixa simples o uso de várias formas de autenticação, incluindo
a bastante comum Basic Auth.

::

    >>> from requests.auth import HTTPBasicAuth
    >>> auth = HTTPBasicAuth('fake@example.com', 'not_a_real_password')
    >>> r = requests.post(url=url, data=body, auth=auth)
    >>> r.status_code
    201
    >>> content = r.json()
    >>> print content[u'body']
    Sounds great! I'll get right on it.

Brilhanete. Mas, espere, não! Eu queria adicionar que eu demoraria um pouco,
porque eu tinha que alimentar meu gato. Se pelo menos eu pudesse editar esse comentário!
Ainda bem que o GitHub nos permite usar outro verbo HTTP, PATCH, para editar esse comentário.
Vamos fazer isso.

::

    >>> print content[u"id"]
    5804413
    >>> body = json.dumps({u"body": u"Sounds great! I'll get right on it once I feed my cat."})
    >>> url = u"https://api.github.com/repos/kennethreitz/requests/issues/comments/5804413"
    >>> r = requests.patch(url=url, data=body, auth=auth)
    >>> r.status_code
    200

Excelente. Agora, apenas para torturar esse tal Kenneth, eu decidi que vou fazer ele
suar e não vou contar que estou trabalhando nisso. Isso significa que eu quero apagar
esse comentário. GitHub nos deixa apagar comentários usando o método incrível, e
de nome adequado, DELETE. Vamos nos livrar do comentário.

::

    >>> r = requests.delete(url=url, auth=auth)
    >>> r.status_code
    204
    >>> r.headers['status']
    '204 No Content'

Excelente. Apagado. Agora a última que eu quero saber o quanto do meu limite eu já usei.
Vamos descobrir. GitHub envia essa informação nos cabeçalhos, então, ao invé de baixar
toda a página, enviarei uma requisição HEAD para pegar os cabeçalhos.

::

    >>> r = requests.head(url=url, auth=auth)
    >>> print r.headers
    ...
    'x-ratelimit-remaining': '4995'
    'x-ratelimit-limit': '5000'
    ...

Excelente. Agora, devemos escrever um programa Python que abusa a API do GitHub
de vários jeitos legais, mais 4995 vezes.

Cabeçalhos Link
---------------

Várias APIs HTTP fornecem cabeçalhos Link. Eles deixam as APIs mais descritiva e facilmente descoberta.

GitHub os usa para `paginação <http://developer.github.com/v3/#pagination>`_ na sua API, por exemplo::

    >>> url = 'https://api.github.com/users/kennethreitz/repos?page=1&per_page=10'
    >>> r = requests.head(url=url)
    >>> r.headers['link']
    '<https://api.github.com/users/kennethreitz/repos?page=2&per_page=10>; rel="next", <https://api.github.com/users/kennethreitz/repos?page=6&per_page=10>; rel="last"'

Requests irá automaticamente interpretar esses cabeçalhos de link e os fará facilmente consumíveis::

    >>> r.links["next"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=2&per_page=10', 'rel': 'next'}

    >>> r.links["last"]
    {'url': 'https://api.github.com/users/kennethreitz/repos?page=7&per_page=10', 'rel': 'last'}

Transport Adapters
------------------

A partir da versão v1.0.0, Requests mudou para um design interno modular. Parte do
motivo que isso foi feito era pra implementar Transport Adapters, originalmente
`descrito aqui`_. Transport Adapters fornecem um mecanismo para definir métodos
de interação para um serviço HTTP. Em particular, eles permitem que você
aplique configurações por serviço.

Requests é lançado com um único Transport Adapter, o
:class:`HTTPAdapter <requests.adapters.HTTPAdapter>`. Este adaptador fornece
a interação padrão do Requests com HTTp e HTTPS usando a poderosa biblioteca
`urllib3`_. Sempre que uma :class:`Session <Session>` de Requests é inicializada,
um desses é anexado ao objeto :class:`Session <Session>` para HTTP e
outro para HTTPS.

Requests habilita que usuários criem e usem seus próprios Transport Adapters
que fornecam funcionalidades específicas. Uma vez criado, um Transport Adapter
pode ser incluído em um objeto Session, junto com uma indicação de quais
serviços web ele deve ser aplicado.

::

    >>> s = requests.Session()
    >>> s.mount('http://www.github.com', MyAdapter())

A chamada mount registra uma instância específica a um prefixo. Uma vez incluso,
qualquer requisição HTTP usando a sessão cuja URL começa com o dado prefixo
usará o Transport Adapter dado.

Implementando um Transport Adapter está além do escopo desta documentação, mas
um bom começo seria herdar a classe ``requests.adapters.BaseAdapter``.

.. _`descrito aqui`: http://kennethreitz.org/exposures/the-future-of-python-http
.. _`urllib3`: https://github.com/shazow/urllib3

