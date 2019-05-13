.. title: Representing markup.bcf in UML
.. slug: representing-markupbcf-in-uml
.. date: 2019-05-13 17:43:03 UTC
.. tags: bcf, bim, GSoC, class-diagram
.. category: 
.. link: 
.. description: 
.. type: text

.. _`BCF-XML-Documentation`: https://github.com/BuildingSMART/BCF-XML/tree/master/Documentation

Today I mostly finished the UML class diagram in regard to representing the data
of the ``markup.bcf`` file. One think I found kind of interesing while creating
the diagram is that in the `BCF-XML-Documentation`_ There is no link between a
topic and a comment, so no id of one is stored in the other. That didn't make
sense at first because I thought that one ``markup`` could have multiple topics,
which is **not** the case. 

However one think what is not that obvious to me is the case with the ``BIMSnippet``. 
On the documentation page it says that: "BimSnippet is an additional file containing
information related to one or multiple topics.". This would imply that there
would be more topics inside one ``markup.bcf`` file, because ``BimSnippet`` is,
as I understand, also just a node inside ``markup.bcf``.

Regarding the open question from the previous post: How to represent constraints
defined in ``extensions.xsd``; I thought about it a little today and came to the
conclusion that it would be the best approach to go with the last option I
listed. That means that for every node, for which constraints are listed in
``extensions.xsd``, an empty enumeration/class is created which then, during
runtime, gets populated with the valid values.
