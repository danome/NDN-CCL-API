.. _Data:

Data Class
==========

The Data class represents an NDN Data packet.

:[C++]:
    | ``#include <ndn-cpp/data.hpp>``
    | Namespace: ``ndn``

:[Python]:
    Module: ``pyndn``

:[Java]:
    Package: ``net.named_data.jndn``

Data Constructor
----------------

Create a new Data object with the optional name.

:[C++]:

    .. code-block:: c++

        Data(
            [const Name& name]
        );

:[Python]:

    .. code-block:: python

        def __init__(self
            [, name  # Name]
        )

:[JavaScript]:

    .. code-block:: javascript

        var Data = function Data(
            [name  // Name]
        )

:[Java]:

    .. code-block:: java
    
        public Data(
            [Name name]
        )

:Parameters:

    - `name`
        (optional) The name for the data packet. If omitted, use a blank name.

Data Get Methods
----------------

Data.getCongestionMark Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the congestion mark according to the incoming packet header.

:[C++]:

    .. code-block:: c++

        uint64_t getCongestionMark() const;

:[Python]:

    .. code-block:: python

        # Returns int
        def getCongestionMark(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns number
        Data.prototype.getCongestionMark = function()

:[Java]:

    .. code-block:: java

        public final long getCongestionMark()

:Returns:

    The congestion mark. If not specified, return 0.

Data.getContent Method
^^^^^^^^^^^^^^^^^^^^^^

Get content of the Data packet.

:[C++]:

    .. code-block:: c++

        const Blob& getContent() const;

:[Python]:

    .. code-block:: python

        # Returns Blob
        def getContent(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns Blob
        Data.prototype.getContent = function()

:[Java]:

    .. code-block:: java
    
        public final Blob getContent()
    
:Returns:

    The data packet content as a Blob.

Data.getFullName Method
^^^^^^^^^^^^^^^^^^^^^^^

Get the data packet's full :ref:`Name <Name>`, which includes the final
ImplicitSha256Digest.

:[C++]:

    .. code-block:: c++

        ptr_lib::shared_ptr<Name> getFullName() const;

:[Python]:

    .. code-block:: python

        # Returns Name
        def getFullName(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns Name
        Data.prototype.getFullName = function()

:[Java]:

    .. code-block:: java

        public final Name getFullName()

:Returns:

    The full name. You must not change the Name object - if you need to change
    it then make a copy.

Data.getIncomingFaceId Method
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Get the incoming face ID according to the incoming packet header (if the
forwarder is configured to include it in the header).

:[C++]:

    .. code-block:: c++

        uint64_t getIncomingFaceId() const;

:[Python]:

    .. code-block:: python

        # Returns int
        def getIncomingFaceId(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns number
        Data.prototype.getIncomingFaceId = function()

:[Java]:

    .. code-block:: java

        public final long getIncomingFaceId()

:Returns:

    The incoming face ID. If not specified, return ``(uint64_t)-1`` (C++)
    or ``None`` (Python) or ``undefined`` (JavaScript) or ``-1`` (Java).

Data.getMetaInfo Method
^^^^^^^^^^^^^^^^^^^^^^^

Get the data packet's :ref:`MetaInfo <MetaInfo>` object.

:[C++]:

    .. code-block:: c++

        MetaInfo& getMetaInfo();

        const MetaInfo& getMetaInfo() const;

:[Python]:

    .. code-block:: python
    
        # Returns MetaInfo
        def getMetaInfo(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns MetaInfo
        Data.prototype.getMetaInfo = function()

:[Java]:

    .. code-block:: java
    
        public final MetaInfo getMetaInfo()

:Returns:

    The meta info object.

.. _Data.getName:

Data.getName Method
^^^^^^^^^^^^^^^^^^^

Get the data packet's :ref:`Name <Name>`.

:[C++]:

    .. code-block:: c++

        Name& getName();

        const Name& getName() const;

:[Python]:

    .. code-block:: python
    
        # Returns Name
        def getName(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns Name
        Data.prototype.getName = function()

:[Java]:

    .. code-block:: java
    
        public final Name getName()

:Returns:

    The name. If not specified, the name size() is 0.

Data.getSignature Method
^^^^^^^^^^^^^^^^^^^^^^^^

Get the data packet's :ref:`Signature <Signature>` object. If not null, the object is a subclass of 
Signature such as :ref:`Sha256WithRsaSignature <Sha256WithRsaSignature>`

:[C++]:

    .. code-block:: c++

        Signature* getSignature();

        const Signature* getSignature() const;

:[Python]:

    .. code-block:: python
    
        # Returns a subclass of Signature such as Sha256WithRsaSignature
        def getSignature(self)

:[JavaScript]:

    .. code-block:: javascript

        // Returns a subclass of Signature such as Sha256WithRsaSignature
        Data.prototype.getSignature = function()

:[Java]:

    .. code-block:: java
    
        public final Signature getSignature()

:Returns:

    The signature object.  To read the fields of the object, you must check for 
    the type of subclass of Signature (such as Sha256WithRsaSignature), and in 
    C++ and Java you must cast to the subclass. If the signature is not 
    specified, return null (or None in Python).

Data Set Methods
----------------

Data.setContent Method
^^^^^^^^^^^^^^^^^^^^^^

Set the content to the given value.

:[C++]:

    .. code-block:: c++

        Data& setContent(
            const Blob& content
        );

:[Python]:

    .. code-block:: python

        # Returns Data
        def setContent(self,
            content  # Blob
        )

:[JavaScript]:

    .. code-block:: javascript

        // Returns Data
        Data.prototype.setContent = function(
            content  // Blob
        )

:[Java]:

    .. code-block:: java
    
        public final Data setContent(
            Blob content
        )

:Parameters:

    - `content`
        A Blob with the content.

:Returns:

    This Data so that you can chain calls to update values.

Data.setMetaInfo Method
^^^^^^^^^^^^^^^^^^^^^^^

Set the meta info to a copy of the given :ref:`MetaInfo <MetaInfo>` object.

.. note::

    You can also call getMetaInfo and change the fields directly.

:[C++]:

    .. code-block:: c++

        Data& setMetaInfo(
            const MetaInfo& metaInfo
        );

:[Python]:

    .. code-block:: python
    
        # Returns Data
        def setMetaInfo(self,
            metaInfo  # MetaInfo
        )

:[JavaScript]:

    .. code-block:: javascript

        // Returns Data
        Data.prototype.setMetaInfo = function(
            metaInfo  // MetaInfo
        )

:[Java]:

    .. code-block:: java
    
        public final Data setMetaInfo(
            MetaInfo metaInfo
        )

:Parameters:

    - `metaInfo`
        The MetaInfo object which is copied.

:Returns:

    This Data so that you can chain calls to update values.

Data.setName Method
^^^^^^^^^^^^^^^^^^^

Set the data packet's :ref:`Name <Name>`.

.. note::

    You can also call getName and change the name values directly.

:[C++]:

    .. code-block:: c++

        Data& setName(
            const Name& name
        );

:[Python]:

    .. code-block:: python
    
        # Returns Data
        def setName(self,
            name  # Name
        )

:[JavaScript]:

    .. code-block:: javascript

        // Returns Data
        Data.prototype.setName = function(
            name  // Name
        )

:[Java]:

    .. code-block:: java
    
        public final Data setName(
            Name name
        )

:Parameters:

    - `name`
        The data packet's name. This makes a copy of the name.

:Returns:

    This Data so that you can chain calls to update values.

Data.setSignature Method
^^^^^^^^^^^^^^^^^^^^^^^^

Set the signature to a copy of the given :ref:`Signature <Signature>` object.

.. note::

    You can also call getSignature and change the fields directly.

:[C++]:

    .. code-block:: c++

        Data& setSignature(
            const Signature& signature
        );

:[Python]:

    .. code-block:: python
    
        # Returns Data
        def setSignature(self,
            signature  # a subclass of Signature such as Sha256WithRsaSignature
        )

:[JavaScript]:

    .. code-block:: javascript

        // Returns Data
        Data.prototype.setSignature = function(
            signature  // a subclass of Signature such as Sha256WithRsaSignature
        )

:[Java]:

    .. code-block:: java
    
        public final Data setSignature(
            Signature signature
        )

:Parameters:

    - `signature`
        An object of a subclass of Signature such as Sha256WithRsaSignature.
        This calls signature.clone() to make a copy.

:Returns:

    This Data so that you can chain calls to update values.

Data.wireDecode Methods
-----------------------

Data.wireDecode Method (from Blob)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Decode the input from wire format and update this Data.  Also keep a pointer to the immutable input Blob for later use. 

:[C++]:

    .. code-block:: c++

        void wireDecode(
            const Blob& input
        );

:[Python]:

    .. code-block:: python

        def wireDecode(self,
            input  # Blob
        )

:[JavaScript]:

    .. code-block:: javascript

        Data.prototype.wireDecode = function(
            input  // Blob
        )

:[Java]:

    .. code-block:: java
    
        public final void wireDecode(
            Blob content
        )

:Parameters:

    - `input`
        The immutable input byte array to be decoded.

Data.wireDecode Method (copy from byte array)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Decode the input from wire format and update this Data.  Also save a copy of the input for later use. 
(To not copy the input, see wireDecode(Blob).)

:[C++]:

    .. code-block:: c++

        void wireDecode(
            const std::vector<uint8_t>& input
        );

    .. code-block:: c++

        void wireDecode(
            const uint8_t *input,
            size_t inputLength
        );

:[Python]:

    .. code-block:: python

        def wireDecode(self,
            input  # an array type with int elements
        )

:[JavaScript]:

    .. code-block:: javascript

        Data.prototype.wireDecode = function(
            input  // Buffer
        )

:[Java]:

    .. code-block:: java
    
        public final void wireDecode(
            ByteBuffer input
        )

:Parameters:

    - `input`
        The input byte array to be decoded.

Data.wireEncode Method
----------------------

Encode this Data to wire format.

:[C++]:

    .. code-block:: c++

        SignedBlob wireEncode() const;

:[Python]:

    .. code-block:: python

        # Returns SignedBlob
        def wireEncode()

:[JavaScript]:

    .. code-block:: javascript

        // Returns SignedBlob
        Data.prototype.wireEncode = function()

:[Java]:

    .. code-block:: java
    
        public final SignedBlob wireEncode()

:Returns:

    The encoded byte array as a SignedBlob.
