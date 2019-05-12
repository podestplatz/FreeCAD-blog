.. title: How to represent extension.xsd constraints
.. slug: how-to-represent-extensionxsd-constraints
.. date: 2019-05-12 15:54:21 UTC
.. tags: bcf, bim, GSoC, class-diagram
.. category: GSoC 2019 - BCF Integration
.. link: 
.. description: 
.. type: text

.. _repository: https://github.com/podestplatz/BCF-Plugin-FreeCAD
.. _yorik: https://forum.freecadweb.org/memberlist.php?mode=viewprofile&u=68
.. _Dia: http://dia-installer.de/download/linux.html.de
.. _here: https://github.com/buildingSMART/BCF-XML/tree/master/Extension%20Schemas
.. _`python reference`: https://docs.python.org/3/library/enum.html

So by now this repository_ exists, which already houses two example BCF files in
``examples/``,
supplied by yorik_. This repository is intended to be the main development repository.
I started a little UML class diagram, which you can find in
the folder ``doc/`` in the root directory of the repository. 

Note however, the class "Template" is just used for copy and paste. I used it to
set the font to Sourcecode Pro, which was kind of tedious, and since I didn't
know if these font changes translate to newly created classes too I have gone
with the copy&paste approach. The classes presented in this diagram are all for
representing the data from the markup.bcf file (except for the class "Project").
For the creation of these class diagrams I use Dia_.

The current problem I am facing is: how to represent the constraints given in
the ``extension.xsd`` file (a link to it here_). The most constraints listed in
this file are just listings of valid values for some nodes or attributes. What I
want to do is to force the user of this classes to use one of the valid values
and not have to implement extra code that checks each time the value is set or
written to a file for that matter. 

**So what possibilities do I have to choose from?**

Probably most intuitively would be to create one enumeration class (`python
reference`_) for each attibute or node listed in ``extensions.xsd``. The main
disadvantage her ist that these enumerations have to be maintained by hand, in
the case of a change to ``extensions.xsd``. That makes this first intuitive
option quite unattractive. 

The second option that comes to mind would be to parse ``extensions.xsd`` and
then programmatically define the enumerations. This is possible due to the
dynamically typed nature of python. However this also implies that the classes,
containing a representation of an affected attribute or node, also have to be
modified every time the plugin starts and ``extensions.xsd`` is parsed. This is
because the classes, of which type the class variables are, do not exist before
runtime.

A third approach would also be possible where one enumeration class is written,
before runtime, with no values. Then during startup ``extensions.xsd`` gets
parsed and the valid values are added to the enumerations. That however has one
drawback: if the standard gets updated, and a new constraint is put upon another
field then an according class would have to be created manually. 

In the end I still have to think about how it is done best, and what is the most
maintainable version. Already I tend to latter option, because when the standard
changes, most probably also the implementation of this plugin has to change. 
