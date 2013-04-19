.. _authentication:

Autenticação
============

Este documento discute o uso de vários tipos de autenticação com Requests.

Muitos serviços web exigem autenticação, e existem vários tipos diferentes.
Abaixo, vamos esboçar várias formas de autenticação disponíveis no Requests,
do simples ao complexo.


HTTP Basic Auth
---------------

Muitos serviços web que exigem autenticação aceitam HTTP Basic Auth.
Esta é a maneira mais simples e Requests suporta-a nativamente.

Fazer requisições usando HTTP Basic Auth é muito simples::

    >>> from requests.auth import HTTPBasicAuth
    >>> requests.get('https://api.github.com/user', auth=HTTPBasicAuth('user', 'pass'))
    <Response [200]>

Na verdade, HTTP Basic Auth é tão comum que Requests fornece um atalho útil
para usá-lo::

    >>> requests.get('https://api.github.com/user', auth=('user', 'pass'))
    <Response [200]>

Fornecendo as credenciais em uma tupla desse jeito é exatamente o mesmo
que usar ``HTTPBasicAuth`` como no exemplo acima.


Autenticação Digest
-------------------

Outra forma bastante popular de autenticação HTTP é autenticação Digest,
e Requests também a suporta nativamente::

    >>> from requests.auth import HTTPDigestAuth
    >>> url = 'http://httpbin.org/digest-auth/auth/user/pass'
    >>> requests.get(url, auth=HTTPDigestAuth('user', 'pass'))
    <Response [200]>


Autenticação com OAuth 1
------------------------

Uma forma comum de autenticação para várias APIs web é OAuth. A biblioteca ``requests-oauthlib`` permite que usuários do Requests façam requisições autenticadas facilmente::

    >>> import request
    >>> from requests_oauthlib import OAuth1

    >>> url = 'https://api.twitter.com/1.1/account/verify_credentials.json'
    >>> auth = OAuth1('YOUR_APP_KEY', 'YOUR_APP_SECRET',
                      'USER_OAUTH_TOKEN', 'USER_OAUTH_TOKEN_SECRET')

    >>> requests.get(url, auth=auth)
    <Response [200]>

Para mais informações sobre como o fluxo do OAuth funciona, veja o site official do `OAuth`_.
Para exemplo e documentação do requests-oauthlib, veja o repositório do `requests_oauthlib`_ no GitHub.


Outras formas de autenticação
-----------------------------

Requests é feito para permitir que outras formas de autenticação sejam
fácil e rapidamente plugadas. Membros da comunidade open-source frequentemente
escrevem handlers para formas de autenticação mais complicadas ou menos
comumente usadas. Algumas das melhores foram reunidas sob a
`organização Requests`_, incluindo:

- Kerberos_
- NTLM_

Se você quiser usar algumas dessas formas de autenticação, vá direto para
a página delas no GitHub e siga as instruções.


Novas formas de autenticação
----------------------------

Se você não conseguir achar uma boa implementação para a forma de autenticação
que quiser, você mesmo pode implementá-la. Requests deixa fácil a adição
da sua própria forma de autenticação.

Para fazer isto, herde a classe :class:`requests.auth.AuthBase` e implemente
o método ``__call__()``. Quando um handler de autenticação é anexada a uma
requisição, ele é chamado durante a configuração da requisição. O método
``__call__()`` deve, então, fazer o que tiver que ser feito para fazer
a autenticação funcionar. Algumas formas de autenticação poderão adicionar
hooks para fornecer alguma funcionalidade posterior.

Exemplos podem ser encontrados na `organização Requests`_ e no arquivo
``auth.py``.

.. _OAuth: http://oauth.net/
.. _requests_oauthlib: https://github.com/requests/requests-oauthlib
.. _Kerberos: https://github.com/requests/requests-kerberos
.. _NTLM: https://github.com/requests/requests-ntlm
.. _organização Requests: https://github.com/requests

