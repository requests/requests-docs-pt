.. _install:

Instalação
============

Esta parte da documentação cobre a instalação de Requests.
O primeiro passo para usar qualquer pacote de software é instalá-lo corretamente.


Distribute e Pip
----------------

Instalar requests é simples usando `pip <http://www.pip-installer.org/>`_::

    $ pip install requests

ou, usando `easy_install <http://pypi.python.org/pypi/setuptools>`_::

    $ easy_install requests

Porém, você realmente `não deve fazer isso <http://www.pip-installer.org/en/latest/other-tools.html#pip-compared-to-easy-install>`_.


Cheeseshop Mirror
-----------------

Se o Cheeseshop estiver fora do ar, você pode instalar Requests usando um dos
mirrors. `Crate.io <http://crate.io>`_ é um deles::

    $ pip install -i http://simple.crate.io/ requests


Usando o código
---------------

Requests é desenvolvido ativamente no GitHub, onde o código está
`sempre disponível <https://github.com/kennethreitz/requests>`_.

Você pode clonar o repositório público::

    git clone git://github.com/kennethreitz/requests.git

Baixar o `arquivo tar <https://github.com/kennethreitz/requests/tarball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/tarball/master

Ou, baixar o `arquivo zip <https://github.com/kennethreitz/requests/zipball/master>`_::

    $ curl -OL https://github.com/kennethreitz/requests/zipball/master

Uma vez que você tiver uma cópia do código, você pode incluí-lo no seu pacote Pytohn,
ou instalá-lo no seu diretório site-packages facilmente::

    $ python setup.py install
