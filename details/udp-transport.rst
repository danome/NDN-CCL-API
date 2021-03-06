UdpTransport.ConnectionInfo Class
=================================

A UdpTransport::ConnectionInfo extends Transport::ConnectionInfo to hold the host and port info for the UDP connection.

:[C++]:
    | ``#include <ndn-cpp/transport/udp-transport.hpp>``
    | Namespace: ``ndn``

:[Python]:
    Module: ``pyndn.transport``

:[Java]:
    Package: ``net.named_data.jndn.transport``

UdpTransport.ConnectionInfo Constructor
---------------------------------------

Create a UdpTransport.ConnectionInfo with the given host and port.

:[C++]:

    .. code-block:: c++

        ConnectionInfo(
            const char *host
            [, unsigned short port]
        );

:[Python]:

    .. code-block:: python

        def __init__(self,
            host     # str
            [, port  # int])

:[Java]:

    .. code-block:: java

        public ConnectionInfo(
            String host
            [, int port]
        )

:Parameters:

    - `host`
        The host for the connection.

    - `port`
        (optional) The port number for the connection. If omitted, use 6363.

.. _UdpTransport:

UdpTransport Class
==================

:[C++]:
    | ``#include <ndn-cpp/transport/udp-transport.hpp>``
    | Namespace: ``ndn``

:[Python]:
    Module: ``pyndn.transport``

:[Java]:
    Package: ``net.named_data.jndn.transport``

UdpTransport Constructor
------------------------

Create a UdpTransport which extends the Transport interface to implement communication over UDP.

:[C++]:

    .. code-block:: c++

        UdpTransport();

:[Python]:

    .. code-block:: python

        def __init__(self)

:[Java]:

    .. code-block:: java

        public UdpTransport()
