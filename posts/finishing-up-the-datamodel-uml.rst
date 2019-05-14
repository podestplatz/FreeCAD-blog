.. title: Advancing to the Viewpoint.bcf part
.. slug: finishing-up-the-datamodel-uml
.. date: 2019-05-14 03:42:30 UTC
.. tags: bcf, bim, GSoC, class-diagram
.. category: 
.. link: 
.. description: 
.. type: text

.. _`forum post`: https://forum.freecadweb.org/viewtopic.php?p=308095#p308095
.. _`viewpoint.bcf`: https://github.com/buildingSMART/BCF-XML/blob/master/Schemas/visinfo.xsd
.. _`W3CSchools: XML Schema Tutorial`: https://www.w3schools.com/xml/schema_intro.asp

In the `forum post`_ yesterday, Yorik pointed out that, although part of the
specification, the BimSnippet does not have to be treated with high priority. He
also referred me to Bernd and Duncan in case of questions arising. 
As advised, I will consider the BimSnippet in the data model but probably won't
implement it right away. 

The progress I made today was mostly in understanding the BCF specification in
regards to the viewpoint.bcfv file. Multiple viewpoint files can be contained
within the folder of one topic. At the beginning of the day I thought that the
data model would be finished rather quick but as it turned out the documentation
on it is not as concise as one might wish it to be. To be more precise: the
whole section about the "Components" element is a bit confusing. It lists
multiple "properties", like `Selection` and `Visibility`. My intuition says that
`Selection` contains a list of components that are selected, and `Visibility`
specifies whether these components shall be visible or not. But in reality each
contains a list of components onto which the values of the attributes of the
respective property shall be applied. 

This is a bit confusing at first, and still is for me. Because of this ambiguity
I decided, for the further data model design, that refer to the related XML
schemas rather than the written documentation. For example here you can find the
schema for the `viewpoint.bcf`_ file.

But since the last time I have worked with XML schemas was in vocational school
(I think 2012) I have to refresh my memory on it. For that I will go through the
relevant parts of the `W3CSchools: XML Schema Tutorial`_. Tomorrow. 
