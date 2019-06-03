.. title: Handling Non-Conform BCF Files
.. slug: handling-non-conform-bcf-files
.. date: 2019-06-01 07:50:16 UTC
.. tags: bcf, schema, xml
.. category: 
.. link: 
.. description: 
.. type: text

.. _`XSDs`: https://github.com/buildingSMART/BCF-XML/tree/master/Schemas
.. _`link to the first one`: https://www.bimcollab.com/Files/Example-Projects/BIMcollab_Example_project_Issues-bcfzip.aspx
.. _`xmlschema`: https://pypi.org/project/xmlschema/
.. _`markup.xsd`: https://github.com/buildingSMART/BCF-XML/blob/master/Schemas/markup.xsd
.. _`validation modes`: https://xmlschema.readthedocs.io/en/latest/usage.html#xsd-validation-modes
.. _`hardeeprai`: https://forum.freecadweb.org/memberlist.php?mode=viewprofile&u=255
.. _`freecad forum`: https://forum.freecadweb.org/

This post shall just collect some ideas I had about handling BCF files whose
contents may not be (in part) conform to the `XSDs`_ supplied by buildingSmart on
github.

_________________________________
Why is there a need for that?
_________________________________

Well, during testing with one of the two BCF files I currently have (here the
`link to the first one`_ to download) I wanted to validate each file I read in,
before I read it in, to be (mostly) sure that my code does not throw unexpected
exceptions. But already in `markup.bcf` of the first topic the library I use,
`xmlschema`_, threw an `XMLSchemaValidatorError`. The reason was that the node
`<Header />` was defined, but it was defined empty, which is prohibited by
`markup.xsd`_. So as quick fix I deleted the `<Header />` node altogether to be
able to test my code somewhat. But now on to the next section.

__________________________________________________________________
How does `xmlschema` report parsing errors?
__________________________________________________________________

Before we can go into the problem solution, a little investigation of the
library I am using has to be done. We need to know how it handles the nodes and
attributes that could not be parsed/decoded against the corresponding XSD
file.
For parsing/decoding `xmlschema`_ defines the following three `validation
modes`_:

  **strict**: Schemas are validated against the meta-schema. The processor stops
  when an error is found in a schema or during the validation/decode of XML
  data.

  **lax**: Schemas are validated against the meta-schema. The processor collects
  the errors and continues, eventually replacing missing parts with wildcards.
  Undecodable XML data are replaced with None.

  **skip**: Schemas are not validated against the meta-schema. The processor
  doesn't collect any error. Undecodable XML data are replaced with the original
  text.

For the ones, like me, that got confused by the first sentence of the first two modes:
every schema that is read in is itself validated against a schema, the
meta-schema, before it can be used. These modes can be used separately for
reading the schema file and for parsing/decoding a XML file. So for example it
is possible to read the `markup.xsd` in **strict** mode, thereby validating it
against a meta-schema, but then parsing/decoding the `markup.bcf` file in
**lax** or **skip** mode. 


__________________________________
In what way can errors be handled?
__________________________________

Given the modes in which XML files can be read in by `xmlschema`_ I could only
think about the following two possibilities on how to handle possible errors:

1. Just ignore the node that could not be parsed/decoded against the
   corresponding schema file. Easy to accomplish with **lax** mode.
2. Read in the plain XML text for that node, store it somewhere in the data
   structure and when writing it all out again just insert the plain XML text into
   the right place. Only possible in **skip** mode. 

The main drawback of number 1 is clearly that the data, that couldn't be read
in, would be lost, or only exist in an earlier version of the BCF file, after
the datastructure got written. It would certainly be easy to implement but does
not offer the user great value if his or her data get lost. This is, however, on
the premise of reading in the file once and writing it once after all changes
have been made in memory. 

The second option offers a possibility to circumvent the lost data problem. The
not-parsable elements just get read in plain.

But there may be another option that could use **lax** mode without the data
loss. This option is another approach to writing the contents of the
datastructure to the file. Originally I planned to do it in one sweep when the
user presses the enter key or the save button for that matter.  But then
`hardeeprai`_ from the `freecad forum`_ suggested, to another topic, that I
write changes right after the were made. So effectively implementing an update
driven approach to making changes permanent.

So now there are two possible approaches to handling errors that each use a
different validation mode. In the next section I want to list some thoughts I
came up with for each one of these approaches. 


__________________________________
Approaches: Pros & Cons
__________________________________

Reading once and writing once
.............................

As already established, **skip** mode must be used here with `xmlschema`. For
every member in the data structure it has to be noted somewhere if it could be
read in or not. That would indicate whether the node holds the acutal value of
the intended datatype or if it holds the plain XML text as string. 

**Advantage:** writing the datastructure is fairly easy

**Disadvantage #1:** every class, that represents part of the XML file, has to be
changed in order to house the additional information about every member or a
whole new datastructure has to be created for that. 

**Disadvantage #2:** the type annotations become invalid, if this approach is
used, which leads to confusion by the ones reading the code. 

**Disadvantage #3:** the module that reads in the XML file into the data model
becomes much more complex, thereby not aiding the understandability of the code.


Updating the file on the fly
.............................

Here **lax** mode can be used with `xmlschema`. Still the whole file has to be
read in once, but the parts that could not be parsed/decoded stay in the file and are
not read in. To really be able to update the original file, the datamodel has to
accommodate information for every element if it is new or updated, and therefore
shall be written or not. The overhead here is comparable to the first approach
but every class just needs one additional member to signify a change or that
this object was newly created. 

**Advantage #1:** (as already mentioned) not so much overhead in the data model
compared to the first approach. 

**Advantage #2:** The user is not exposed to the danger of losing unsaved
changes, since it is updated every time a new change is committed to the data
representation.

**Disadvantage #1:** the module that writes the data to the BCF file has to have
light parsing capabilities or a XML awareness respectively, in order to be able
to edit XML files directly. This does not have to be the case with the first
approach, since we could utilize the `xmlschema` library.

**Disadvantage #2:** Objects that get deleted must not be deleted right away
from the data model. Rather a flag has to be set that it is "deleted" and the
corresponding part in the file has to be removed as well. After that it can be
removed from the data model.

______________________________________

I am gravitating more to the latter option, since it does not interfere with the
type annotations, and therefore does not complicate the whole type situation
in the data model. What I still have to face, however, is the XML aware writer
module. This is going to be fun!
