.. title: Writing non Schema Conform BCF Files
.. slug: writing-non-schema-conform-bcf-files
.. date: 2019-06-07 16:02:56 UTC
.. tags: bcf schema 
.. category: 
.. link: 
.. description: 
.. type: text

.. _`Handling Non-Conform BCF Files`: link://slug/handling-non-conform-bcf-files

Wait would you write files with the intention that they don't conform to the
provided XSD files? ... you might ask by just reading the title. No I wouldn't.
As I introduced in my last blog post (`Handling Non-Conform BCF Files`_), that
was not an update of the dev-logs post, I introduced the need of supporting
files from vendors that might do some things differently. I ended this last post
with a pros and cons list about two approaches that I thought of. In the
meantime I decided for latter one. 

Recap
======
The second approach, let's call it update approach, extracts the BCF file to a
temporary folder, reads the contents into memory (that are conform to the
corresponding XSD file) and every time the state in
memory is changed the updates get written to the dicrectory structure in the
temporary folder. At the end, when the plugin is closed and its lifetime ends,
this directory is compressed again and stored in its original location. The
update approach thereby does not alter any data that the plugin didn't load into
memory, it works "around" it.

I think this is the better appraoch, since no exceptions to the rule have to be
made for every speciality that a vendor might incorporate in its files. However,
the writer module has to have an understanding of XML baked in, in order for it
to be able to update these files in place. 

In the next section I want to elaborate a bit more on how I envision this writer
module. 

Writing actual stuff
======================
At the beginning of every writing operation it has to be known whether an
existing element shall be modified, a new element shall be added or an existing
one shall be deleted. Following you will find my thoughts, uncompiled, as I have
written them down onto paper. First will be the handling of additions. 

-----------------------

How can additions be handled?
''''''''''''''''''''''''''''''
Every element class (a class that directly represents a XML-Element like
`Comment`) shall have a function that returns the XML-Text. This text is then
going to be inserted at the right place by the writer module. 
The writer module has to crawl through the complete data model to check/compile
a list of objects that were added, changed or deleted. Then for each added
object it calls the XML-Text function to get the corresponding XML-text for each
element. A second function shall give the following information about the
position at which it should be inserted (could also be done with a dictionary,
that way the data model stays clean):

- the containing element: the containing element of `Comment` is `Markup`
- the relatie order: a list of elements that preceed the current one in 
  reverse order. So the immediate predecessor is first in the list. The last
  element of the list is the containing element. 
- if the new element shall be added inside an element that might occur more than
  once (e.g.: `ModifiedDate` in `Comment`) then also a unique id of that list
  element has to be retrieved. 

The writer then searches for the containing element in the file and then goes
through the contents of the containing element, searching for the one with the
least index in the relative order list. The text is inserted right after the
closing tag of the chosen element. 

Any references should not be handled/checked in the writer module (e.g.: that
the viewpoint guid in the to be written comment object belongs to an actual
existing viewpoint). Such consistency checks are assumed to be handled in the
GUI or the programmatic interface respectively.

-----------------------

How can updates be handled?
'''''''''''''''''''''''''''
An element either has to occur only once in the corresponding file, or be
identified uniquely by some attribute. Without one of these two constraints the
identification of the right element in the XML-file becomes extremely hard or
cannot be done deterministically. 
An object shall be flagged `MODIFIED` iff (=if and only if) a non-list child
element was changed. A change of a list element translates either to an addition
or deletion, or a change of an element that can be identified
deterministically, as described above. In which case the object, containing the
list with one or more items changed, shall not be flagged `MODIFIED`. Each
object shall then contain a list of changed objects, oblivious of whether it
translates to an attribute or element. for each of these objects the writer
module has to gather following information: 

- element hierarchy: `ModifiedDate` in `Comment` would have
  `ModifiedDate` -> `Comment` -> `Markup` as hierarchy list. 
- unique id of every list element that is in the hierarchy (like with additions)
- whether it translates to an attribute or an element

The writing module shall collect a list of all elements that are flagged
`MODIFIED`. For each element the writer module shall collect the amount
information listed above. It then follows the element hierarchy of the object in
reverse order till it reaches the changed object itself. At this point the
distinction between attribute and element has to be made. For both cases the
following two steps happen: 

1. the respective substring of the element has to be found and
2. be replaced entirely by the XML-text generated from the object

-----------------------

How can deletions be handled?
'''''''''''''''''''''''''''''
The writer module compiles a list of all elements to delete. For each element
the following information shall be gathered: 

- which file is the element contained in
- unique id of the element (if available)
- name of the element

The writer module then searches for the unique id in the specified file that
also matches the element name and deletes it from the start to the end tag. 

-----------------------

How shall the interface to the writer look?
===========================================
The above mentioned crawler has the advantage of alos supporting writing the
file only once. Therefore also being somewhat resource saving. 
The writer shall have a function `writeChanges()`.
