.. title: BCF-Plugin GSoC 2019
.. slug: bcf-plugin-gsoc-2019
.. date: 2019-08-03 10:09:52 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

.. _`GSoC 2019 BCF-Plugin`: https://summerofcode.withgoogle.com/#Link
.. _FreeCAD: https://freecadweb.org/
.. _`FreeCAD BCF-Plugin`: https://github.com/podestplat/BCF-Plugin-FreeCAD
.. _`BCF supporting CAD tools`: https://www.bimcollab.com/en/BCF-Manager/BCF-Manager
.. _pluginwikiprimer: https://github.com/podestplatz/BCF-Plugin-FreeCAD/wiki/Home
.. _modelviewqt: https://doc.qt.io/qt-5/model-view-programming.html

This blog post shall give detailed information about the project
`GSoC 2019 BCF-Plugin`_. The following sections will elaborate on a few key
aspects of the project including: 

- the initial vision and with it the reason why it was done
- some information about the BCF standard
- what exactly has been accomplished in this project
- main hurdles that cost a considerable amout of time
- ideas for improvment.

Vision
-------
BCF stands for BIM (Building Information Modelling) Collaboration Format. It is
used in the industry by architects to collaborate effectively on one building
model. Collaboration in BCF terms means that, apart from the building model a
separate BCF file is handed over to the other architect(s). Using a tool that
supports the standard, these parties can then add issues/topics, supply these
with context in from of comments, snapshots, addtional documents and more. 

The intent behind the `FreeCAD BCF-Plugin`_ was to also integrate support for
BCF into FreeCAD, and thus adding FreeCAD to the list of `BCF supporting CAD
tools`_.

BCF-Standard
------------
During the course of the project I have written quite a lot of documentation.
This includes already a primer on the BCF standard that is more expansive than
the one above. So for more information on BCF please refer to the project's `wiki
page`__. 

__ pluginwikiprimer_

Outcome
--------
FreeCAD can be used in two modes: 

1. GUI-mode: in this mode FreeCAD can be used as Qt application. 
2. nonGUI-mode: is composed of a python console that gives access to all aspects
   of the opened file.

Apart from these two modes, a plugin framework was implemented which is able to
run standalone python applications within the FreeCAD context. Through the
suggestions of my mentors (@yorik and @hardeeprai) I developed the BCF
integration as python application that accesses FreeCAD's state via the its
python interface. 
The plugin, like its host application, can be controlled either via a graphical
user interface or via the python console, proviced also by FreeCAD.

---------------

Complete integration of the BCF standard in the timeframe of just about three
months would have required more than one person, presumably. Thus, here the
aspects that are accessible through the GUI are listed (practically everything
can be done in nonGUI-mode, since complete access to the parsed BCF file is
given to the user):

- BCF files are read into an internal data model 
- a topic can be chosen from the list of specified ones. 
- most metrics of a topic can be edited in a separate window. 
- the list of additional document references of a topic can be displayed
- relations between topics can be explored
- comments can be edited/added/removed (this is the part which was most
  emphasized during development)
- the snapshots of a topic are shown,
- as well as a list of all available viewpoints. 
- a snapshot can be opened in full resolution
- a viewpoint can be applied to the object viewer of FreeCAD. 
- the current state of the bcf file can also be saved to file again.


Hurdles along the way
---------------------

In this section I want to list some of the most significant hurdles I
experienced during the last three months. 

The biggest of all probably was supporting BCF files that do not comply with the
standard. Unfortunately it is intended by the BIM collaboration that vendors of
BCF tools can add their flavor to the BCF files created by their tools. This posed
quite the obstacle in two ways: 

1. What to do with additional and malformed information, that is not defined by
   the standard? 
2. How can BCF files be opened and written again without losing potential
   information that did not comply to the standard? 

The approach taken for (1) was to just omit data that did not comply to the
standard during reading it in. However, this had significant consequences for
(2) as it ruled out the easiest strategy for writing a BCF file: just
serializing the whole data model from memory to disk. Thus, (2) was solved by using an
update write strategy. Thereby the BCF file is opened in a temporary directory,
and every modification, done by the user will be commited to it as an update. 
This update approach was designed to also support batch processing (i.e.: write
multiple updates at once instead of immediatly when they arise).

--------

The second major hurdle was the comment list in Qt. My background in programming
does not include user interface programming and thus I had no experience with
Qt. Items in the comment list show the three major aspects of a comment at once: 

- the comment text itself
- the E-Mail address of the person that either created or, if the comment already was
  modified, modified it at last, and
- the date of creation or last modification respectively. 

These three aspects are split up into two rows, separated by a thin line, where
the comment fills the first row and comment and date share the second row. 
The difficulty here was to properly implement custom list elements in the
`Model/View Paradigm`__ of Qt. The current implementation uses, apart from a
custom model and view, a delegate. It is responsible for drawing the list
elements as described above and for handling modifications to the list elements. 

__ modelviewqt_

-----

The last, in comparison, rather small hurdle was the correct handling of
temporary directories. In theory it is quite easy: 

1. a `TemporaryDirectory` object is created like in the code example below. 
   This object is itself a context manager and automatically deletes the
   temporary directory again when it is garbage collected.

  .. code:: python
    
    import tempfile
    tmpDir = tempfile.TemporaryDirectory()

2. when you are finished run `del tmpDir` and the whole directory will be
   deleted again. 
3. during runtime of the application store the object in the application global
   space. 

That is the theory. However in combination with Qt's event handling mechanism
the assumption that the application global variable will have the same state
when reading it in an event handler and in normal operation does not hold
anymore. 

In my case I had a global function `getSystemTmp()` that creates a temporary
directory only on first invocation, and on each subsequent call return the
already created directorie's path. However, it twice created a new temporary
directory. The second one was created when asking the user for his/her E-Mail
address before a change is commited. 

In the end this context problem was solved by a separate file in the system's
temporary directory. It holds only the path to the created temporary directory,
which serves as working directory. Thus, now `getSystemTmp()` creates the file
on the first invocation and on each subsequent call reads the one line from the
file and returns it. 

Outlook
---------

Some things are still missing at this point in time (2019-08-19)
Following a list of things that the plugin still needs to serve the
user best. 

- Apart from opening and saving an already existing BCF file, the creation of a
  completely new one is probably integral to a BCF tool. 
- Adding new topics to a project
- Link comments to viewpoints
- Add new viewpoints
- Create snapshots
- A list showing referenced documents with their respective paths is already
  implemented, but it would be nice if they also could be opened right from the
  plugin with a double left click or something like that. 
- Create relations between topics in form of `<RelatedTopic>` nodes in the
  `markup.bcf` file.

=================

At this point I want to thank my mentors Yorik van Havre and Hardeeprai
(unfortunatel I don't know if his real name is written this way, because I only
knew him by his user name on the freecad forum), for giving me the possibility
to contribute to FreeCAD in a meaningful manner!
