.. title: Learning XSD Syntax
.. slug: learning-xsd-syntax
.. date: 2019-05-15 04:24:35 UTC
.. tags: bcf, bim, GSoC, class-diagram
.. category: 
.. link: 
.. description: 
.. type: text

.. _`python library`: https://pypi.org/project/xmlschema/
.. _`here`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/tree/master/doc

The main takeaway from the XML Schema Definition Tutorial I went through today
was the following: 

  So the node `<xs:sequence>...</xs:sequence>` is part of the definition of a
  complex type, it is also called an indicator. What it does is that it tells the
  XML checker that the elements specified inside have to occur in the same order
  in the XML document to be valid. Also it specifies what elements may be
  contained in the complex node.

This greatly improved my understanding of `visinfo.xsd` which describes how a
`viewpoint.bcfv` file is built. As I learned during design, the representation
of a viewpoint is almost as complex as the representation of the remaining
contents of a BCF file. 

I thought about how I coudl read the contents of a BCF into this file structure.
As my brain always does, it thinks through the whole process, from checking if
the given file is valid till the point where all the data resides in an object
in memory. This makes me prone to "reinventing the wheel" as I also design a XSD
parser/checker in my head which I then would use to check the given files before
reading them in. But a quick google search brought up a better option, namely an
offical `python library`_ for checking XML files against a schema. 

Last but not least: I am pleased to announce that the first draft of the class
diagram is ready. You can find it `here`_ if you are interested. 
