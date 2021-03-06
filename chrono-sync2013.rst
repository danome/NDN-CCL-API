ChronoSync2013 Class
====================

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    A ChronoSync2013 implements the NDN ChronoSync protocol as described in the
    2013 paper [ChronoSync 2013] "Let’s ChronoSync: Decentralized Dataset
    State Synchronization in Named Data Networking".
    http://named-data.net/publications/chronosync

    :[C++]:
        | ``#include <ndn-cpp/sync/chrono-sync2013.hpp>``
        | Namespace: ``ndn``

    :[Python]:
        Module: ``pyndn.sync``

    :[Java]:
        Package: ``net.named_data.jndn.sync``

ChronoSync Protocol
-------------------

As in [ChronoSync2013], we describe the ChronoSync protocol using
the example of ChronoChat, a chat application.

Broadcast prefix and application data prefix
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As described in [ChronoSync 2013] section III.B "Naming Rules",
a ChronoSync application uses two types of name prefixes: A broadcast
prefix for sync state messages that update the current sequence number of
each publisher, and an application data prefix used to fetch the
application-specific data from a producer. 

In ChronoChat, a broadcast prefix for the chat room "lunch-talk"
would be "/ndn/broadcast/ChronoChat-0.3/lunch-talk".

As part of the application data prefix, ChronoChat always prompts the user for a
"hub prefix" under which the user is allowed to pubish, for example
"/ndn/edu/ucla/remap". Also, instead of putting the actual user name in the
data prefix, we generate a random string at application startup, for example
"GL7VUW32Ft". So, the application data prefix for the chat room "lunch-talk"
would be "/ndn/edu/ucla/remap/lunch-talk/GL7VUW32Ft".

The digest tree: Tracking each publisher's sequence number
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

In ChronoSync, each publisher publishes a sequence of data packets, and
at any point in time each publisher has a current (maximum) sequence number. The
purpose of the ChronoSync2013 class is to track the current sequence number
of each publisher, and to announce a new sequence number when the local
application publishes new data. ChronoSync2013 does this by maintaining a
"digest tree" with an entry for each publisher and their current sequence number.

In the digest tree in [ChronoSync 2013] Fig. 4, the node for each publisher
has a "name prefix" (application data prefix) and "sequence number". In our
implementation, we also add a separate "session number", which is based on the
timestamp at application start-up. So the node digest is computed over
"application data prefix" + "session number" + "current sequence number". The
ChronoSync2013 class computes the digest of each node in the tree (sorted on
"application data prefix" + "session number") and then computes the
"root digest" over each node digest.

Syncing the digest tree root digest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

As described in [ChronoSync 2013] section III.D, ChronoSync2013 keeps an
outstanding interest for the root digest of the digest tree, so
that another application will answer with a data packet when it publishes
data under a new sequence number. For example, if the current root digest is
"74d13bb1081d457b5798f796399a7833657fe6ea65b7a6891c07f84fc7c26e93", then the
outstanding interest would be
"/ndn/broadcast/ChronoChat-0.3/lunch-talk/74d13bb1081d457b5798f796399a7833657fe6ea65b7a6891c07f84fc7c26e93".
(Note that the root digest of an empty tree at startup is always "00".)

When another application answers this interest with a sync state message,
ChronoSync2013 updates its internal digest tree, computes a new root digest
and expresses an outstanding interest with the new root digest for a new
sync state message.  It also calls the `onReceivedSyncState` callback given to
the constructor to provide the application with a list of
:ref:`SyncState <ChronoSync2013.SyncState>` objects. For example, the
SyncState data prefix (of the other application) is
"/ndn/edu/ucla/remap/lunch-talk/48nQn3KyXV", the session number
(of the other application) is 1405718551, and the new sequence number
is 791. In this case, the ChronoChat application would express an interest for
"/ndn/edu/ucla/remap/lunch-talk/48nQn3KyXV/1405718551/791". The other ChronoChat
application will receive this interest and send the application-specific content,
e.g. a chat message.

Publishing new data
^^^^^^^^^^^^^^^^^^^

Finally, when the application wants to publish new data, it calls
:ref:`publishNextSequenceNo <ChronoSync2013.publishNextSequenceNo>` which
increments the current sequence number and answers the outstanding interest 
from other applications by publishing a sync message with the new
sequence number. ChronoChat2013 then adds this sync message to its internal
digest tree which creates a new root digest. Finally, it expresses an interest
for the next sync update where the name has the new root digest. After the
application calls publishNextSequenceNo, it calls
:ref:`getSequenceNo <ChronoSync2013.getSequenceNo>`
to get the new sequence number and publishes the application-specific content
for the this sequence number.

In addition to the above operations, each time that ChronoSync2013 receives
or generates a new set of sync state messages for a particular root digest, it
saves this in a "digest log" as described in [ChronoSync 2013] section III.C
used with "recovery" interests.

Packet encoding with Google protocol buffers
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When the ChronoSync2013 class sends a sync state message, the content of the
data packet needs a way to encode the sync state. So, it uses a Google protocol
buffer (https://developers.google.com/protocol-buffers/)
encoding using the definition in sync-state.proto
(https://github.com/named-data/ndn-cpp/blob/master/src/sync/sync-state.proto)
which specifies `applicationDataPrefix` as the name, along with the session
number and sequence number.

Likewise, the application can define its own message format for the content
in the application-specific data packet. So, the ChronoChat example uses a
protocol buffer encoding using the definition in chatbuf.proto
(https://github.com/named-data/ndn-cpp/blob/master/tests/chatbuf.proto)
which specifies a message type of CHAT, HELLO, LEAVE and JOIN, along with
details for each message type. The application-specific message is cleanly
separated from the core sync state message used by ChronoChat2013.

ChronoSync2013 Constructor
--------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Create a new ChronoSync2013 to communicate using the given face. Initialize
    the digest log with a digest of "00" and and empty content. Register the
    applicationBroadcastPrefix to receive interests for sync state messages and
    express an interest for the initial root digest "00".

    .. note::

        [except JavaScript] Your application must call :ref:`processEvents <processEvents>`.
        Since processEvents modifies the internal ChronoSync data structures, your
        application should make sure that it calls processEvents in the same
        thread as this constructor (which also modifies the data structures).

    :[C++]:

        .. code-block:: c++

            ChronoSync2013(
                OnReceivedSyncState onReceivedSyncState,
                OnInitialized onInitialized,
                const Name& applicationDataPrefix,
                const Name& applicationBroadcastPrefix,
                int sessionNo,
                Face& face,
                KeyChain& keyChain,
                const Name& certificateName,
                Milliseconds syncLifetime,
                const OnRegisterFailed& onRegisterFailed
            );

    :[Python]:

        .. code-block:: python

            def __init__(self
                onReceivedSyncState,         # function object
                onInitialized,               # function object
                applicationDataPrefix,       # Name
                applicationBroadcastPrefix,  # Name
                sessionNo,                   # int
                face,                        # Face
                keyChain,                    # KeyChain
                certificateName,             # Name
                syncLifetime,                # float
                onRegisterFailed             # function object
            )

    :[JavaScript]:

        .. code-block:: javascript

            var ChronoSync2013 = function ChronoSync2013(
                onReceivedSyncState,         // function
                onInitialized,               // function
                applicationDataPrefix,       // Name
                applicationBroadcastPrefix,  // Name
                sessionNo,                   // number
                face,                        // Face
                keyChain,                    // KeyChain
                certificateName,             // Name
                syncLifetime,                // number
                onRegisterFailed             // function
            )

    :[Java]:

        .. code-block:: java

            public ChronoSync2013(
                OnReceivedSyncState onReceivedSyncState,
                OnInitialized onInitialized,
                Name applicationDataPrefix,
                Name applicationBroadcastPrefix,
                long sessionNo,
                Face face,
                KeyChain keyChain,
                Name certificateName,
                double syncLifetime,
                OnRegisterFailed onRegisterFailed
            )

    :Parameters:

        - `onReceivedSyncState`
            When ChronoSync receives a sync state message this calls
            onReceivedSyncState(syncStates, isRecovery) where:

            - `syncStates`
                is the list of :ref:`SyncState <ChronoSync2013.SyncState>` messages.
            - `isRecovery`
                is true if this is the initial list of SyncState messages or from
                a recovery interest. (For example, if isRecovery is true, a chat
                application would not want to re-display all the associated chat
                messages.) The callback should send interests to fetch the
                application data for the sequence numbers in the sync state.

          .. note::

              The library will log any exceptions thrown by this callback, but for better
              error handling the callback should catch and properly handle any exceptions.

        - `onInitialized`
            This calls onInitialized() when the first sync data is received (or
            the interest times out because there are no other publishers yet).

          .. note::

              The library will log any exceptions thrown by this callback, but for better
              error handling the callback should catch and properly handle any exceptions.

        - `applicationDataPrefix`
            The prefix used by this application instance for application data.
            For example, "/my/local/prefix/lunch-talk/0K4wChff2v". This is used
            when sending a sync message for a new sequence number. In the sync
            message, this uses applicationDataPrefix.toUri().

        - `applicationBroadcastPrefix`
            The broadcast name prefix including the application name. For
            example, "/ndn/broadcast/ChronoChat-0.3/lunch-talk". This makes a copy
            of the name.

        - `sessionNo`
            The session number used with the applicationDataPrefix in sync state
            messages.

        - `face`
            The :ref:`Face <Face>` for calling registerPrefix and expressInterest.
            The Face object must remain valid for the life of this
            ChronoSync2013 object.

        - `keyChain`
            To sign a data packet containing a sync state message, this calls
            keyChain.sign(data, certificateName).

        - `certificateName`
            The certificate name of the key to use for signing a data packet
            containing a sync state message.

        - `syncLifetime`
            The interest lifetime in milliseconds for sending sync interests.

        - `onRegisterFailed`
            If failed to register the prefix to receive interests for the
            `applicationBroadcastPrefix`, this calls
            onRegisterFailed(applicationBroadcastPrefix).

          .. note::

              The library will log any exceptions thrown by this callback, but for better
              error handling the callback should catch and properly handle any exceptions.

.. _ChronoSync2013.getProducerSequenceNo:

ChronoSync2013.getProducerSequenceNo Method
-------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the current sequence number in the digest tree for the given producer
    dataPrefix and sessionNo.

    :[C++]:

        .. code-block:: c++

            int getProducerSequenceNo(
                const std::string& dataPrefix,
                int sessionNo
            );

    :[Python]:

        .. code-block:: python

            # Returns int
            def getProducerSequenceNo(self,
                dataPrefix,  # str
                sessionNo    # int
            )

    :[JavaScript]:

        .. code-block:: javascript

            // Returns number
            ChronoSync2013.prototype.getProducerSequenceNo = function(
                dataPrefix,  // string
                sessionNo    // number
            )

    :[Java]:

        .. code-block:: java

            public final long getProducerSequenceNo(
                String dataPrefix,
                long sessionNo
            )

    :Parameters:

        - `dataPrefix`
            The producer data prefix as a Name URI string.

        - `sessionNo`
            The producer session number.

    :Returns:

        The current producer sequence number, or -1 if the producer namePrefix
        and sessionNo are not in the digest tree.

.. _ChronoSync2013.getSequenceNo:

ChronoSync2013.getSequenceNo Method
-----------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the sequence number of the latest data published by this application
    instance.

    :[C++]:

        .. code-block:: c++

            int getSequenceNo();

    :[Python]:

        .. code-block:: python

            # Returns int
            def getSequenceNo(self)

    :[JavaScript]:

        .. code-block:: javascript

            // Returns number
            ChronoSync2013.prototype.getSequenceNo = function()

    :[Java]:

        .. code-block:: java

            public final long getSequenceNo()

    :Returns:

        The sequence number.

.. _ChronoSync2013.publishNextSequenceNo:

ChronoSync2013.publishNextSequenceNo Method
-------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Increment the sequence number, create a sync message with the new sequence
    number and publish a data packet where the name is the
    applicationBroadcastPrefix + the root digest of the current digest tree.
    Then add the sync message to the digest tree and digest log which creates a
    new root digest. Finally, express an interest for the next sync update with
    the name applicationBroadcastPrefix + the new root digest. After this, your
    application should publish the content for the new sequence number. You can
    get the new sequence number with
    :ref:`getSequenceNo() <ChronoSync2013.getSequenceNo>`.

    .. note::

        [except JavaScript] Your application must call :ref:`processEvents <processEvents>`.  
        Since processEvents modifies the internal ChronoSync data structures, your
        application should make sure that it calls processEvents in the same
        thread as publishNextSequenceNo (which also modifies the data structures).

    :[C++]:

        .. code-block:: c++

            void publishNextSequenceNo(
                [const Blob& applicationInfo]
            );

    :[Python]:

        .. code-block:: python

            def publishNextSequenceNo(self
                [, applicationInfo  # Blob]
            )

    :[JavaScript]:

        .. code-block:: javascript

            ChronoSync2013.prototype.publishNextSequenceNo = function(
                [applicationInfo  // Blob]
            )

    :[Java]:

        .. code-block:: java

            public final void publishNextSequenceNo(
                [Blob applicationInfo]
            )

    :Parameters:

        - `applicationInfo`
            (optional) This appends applicationInfo to the content of the sync
            messages. This same info is provided to the receiving application in
            the SyncState state object provided to the onReceivedSyncState
            callback.

ChronoSync2013.shutdown Method
------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Unregister callbacks so that this does not respond to interests anymore.
    If you will dispose this ChronoSync2013 object while your application is
    still running, you should call shutdown() first.  After calling this, you
    should not call publishNextSequenceNo() again since the behavior will be
    undefined.

    .. note::

        [except JavaScript] Because this modifies internal ChronoSync data
        structures, your application should make sure that it calls
        processEvents in the same thread as shutdown() (which also modifies the
        data structures).

    :[C++]:

        .. code-block:: c++

            void shutdown();

    :[Python]:

        .. code-block:: python

            def shutdown(self)

    :[JavaScript]:

        .. code-block:: javascript

            ChronoSync2013.prototype.shutdown = function()

    :[Java]:

        .. code-block:: java

            public final void shutdown()

.. _ChronoSync2013.SyncState:

ChronoSync2013.SyncState Class
==============================

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    A SyncState holds the values of a sync state message which is passed to the
    onReceivedSyncState callback which was given to the ChronoSyn2013 constructor.

    :[C++]:
        | ``#include <ndn-cpp/sync/chrono-sync2013.hpp>``
        | Namespace: ``ndn``

    :[Python]:
        Module: ``pyndn.sync``

    :[Java]:
        Package: ``net.named_data.jndn.sync``

ChronoSync2013.SyncState.getApplicationInfo Method
--------------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the application info which was included when the sender called
    :ref:`publishNextSequenceNo <ChronoSync2013.publishNextSequenceNo>` .

    :[C++]:

        .. code-block:: c++

            const Blob& getApplicationInfo() const;

    :[Python]:

        .. code-block:: python

            # Returns Blob
            def getApplicationInfo(self)

    :[JavaScript]:

        .. code-block:: javascript

            // Returns Blob
            ChronoSync2013.prototype.getApplicationInfo = function()

    :[Java]:

        .. code-block:: java

            public final Blob getApplicationInfo()

    :Returns:

        The applicationInfo Blob. If the sender did not provide any, return an
        isNull Blob.

ChronoSync2013.SyncState.getDataPrefix Method
---------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the application data prefix for this sync state message.

    :[C++]:

        .. code-block:: c++

            const std::string& getDataPrefix() const;

    :[Python]:

        .. code-block:: python

            # Returns str
            def getDataPrefix(self)

    :[JavaScript]:

        .. code-block:: javascript

            // Returns string
            ChronoSync2013.prototype.getDataPrefix = function()

    :[Java]:

        .. code-block:: java

            public final String getDataPrefix()

    :Returns:

        The application data prefix as a Name URI string.

ChronoSync2013.SyncState.getSequenceNo Method
---------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the sequence number for this sync state message.

    :[C++]:

        .. code-block:: c++

            int getSequenceNo() const;

    :[Python]:

        .. code-block:: python

            # Returns int
            def getSequenceNo(self)

    :[JavaScript]:

        .. code-block:: javascript

            // Returns number
            ChronoSync2013.prototype.getSequenceNo = function()

    :[Java]:

        .. code-block:: java

            public final long getSequenceNo()

    :Returns:

        The sequence number.

ChronoSync2013.SyncState.getSessionNo Method
--------------------------------------------

.. container:: experimental

    .. admonition:: Experimental

       The support for ChronoSync is experimental and the API is not finalized.

    Get the session number associated with the application data prefix for this
    sync state message.

    :[C++]:

        .. code-block:: c++

            int getSessionNo() const;

    :[Python]:

        .. code-block:: python

            # Returns int
            def getSequenceNo(self)

    :[JavaScript]:

        .. code-block:: javascript

            // Returns number
            ChronoSync2013.prototype.getSessionNo = function()

    :[Java]:

        .. code-block:: java

            public final long getSessionNo()

    :Returns:

        The session number.
