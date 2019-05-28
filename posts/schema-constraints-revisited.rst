.. title: Schema Constraints Revisited
.. slug: schema-constraints-revisited
.. date: 2019-05-28 13:18:31 UTC
.. tags: 
.. category: 
.. link: 
.. description: 
.. type: text

.. _`extensions.xsd`: https://github.com/buildingSMART/BCF-XML/blob/release_2_1/Extension%20Schemas/extensions.xsd
.. _`Enums`: https://docs.python.org/3/library/enum.html
.. _`BCF plugin for FreeCAD`: https://github.com/podestplatz/BCF-Plugin-FreeCAD
.. _`older post`: link://slug/how-to-represent-extensionxsd-constraints

In an `older post`_, one or two weeks back, I've written about a design issue I was
facing when trying to integrate the constraints of `extensions.xsd`_ into my
class model. `extension.xsd` defines possible values for the XML nodes:

- TopicType
- TopicLabel
- TopicStatus
- SnippetType
- Priority
- Stage

I wanted to ensure that the variables, representing let's say `TopicType`, can
only assume valid values, as defined in `extension.xsd`.  My first idea was to
just define `Enums`_ following the schema. Generally this idea is fine but it
doesn't do so well with version changes. A new version of the BCF-XML standard
would then possibly require more maintenance work than necessary. My aim with
this `BCF plugin for FreeCAD`_ is it to create a data model that requires as
little maintenance work as possible, although the code still has to be adapted
to support any new version.

From the maintenance point of view hardcoded enumerations were not really an option.

The next idea was to create something like dynamic enumerations. These would
have the advantage that the constraint is enforced (i.e.: a variable holding an
enumeration value can only hold a valid value as defined in the enumeration
itself). I knew it was possible to change a class object (not the object *of* a
class) during runtime and add or remove members from it.  I thought maybe this
is also possible with classes that derive from `Enum`. In python to create an
enumeration you just have to define a class like this: 

.. code:: python

  from enum import Enum

  class Example(Enum):
    VAL1 = 1
    VAL2 = 2
    ...

The first obstacle was that classes can only derive from a class (that itself
derives from `Enum`) iff they don't introduce new members. Otherwise this would
break some invariants the python interpreter has over enumerations.
The second obstacle was a logical implication of the first: if subclasses
couldn't add new members to an enum-super-class then it is also not possible to
add new members to enumerations during runtime.

So another solution had to be thought of. I maintained the basic structure I
had envisioned for the dynamic enumeration solution. Namely one superclass
called `SchemaConstraint` and one subclass for each of the above mentioned XML
nodes. Every piece of code that wants to represent one of these XML nodes would
store an object of one of the subclasses in a variable of type `SchemaConstraint`. 
The actual value of the object is stored in a class variable, `value`, that
holds just any value of type string. This approach of course has the downside that
basically any value can be assigned to the variable `value`, if not restricted.
In order to change this I made the class variable `value` a property where the
setter checks whether the new value is actually a valid value. 

Now the question arises: How does the setter function know which value is valid?
Every node has different valid values!

To solve this issue I introduced, for one, the list `validValues` into
`SchemaConstraints` (`value` does also reside there) and, for the other, a
static function called `parseConstraints(elementName)`. This static function
reads the valid values for the XML node `elementName` from `extension.xsd` and
returns them as list.  `parseConstraints()` is intended to be called from the
`__init__()` function of each one of the sub classes (e.g.:
`TopicType.__init__()`). To make things clearer I put a small code example below
showing the classes `SchemaConstraints` and `TopicType` as well as all the
members of both classes. 

.. code:: python

  class SchemaConstraint:
    def __init__(self, value, validValues):
      self.value = value
      self.validValues = validValues

    @property
    def value(self):
      ...

    @value.setter
    def value(self, newValue):
      ...

    @property
    def validValues(self):
      ...

    @validValues.setter
    def validValues(self, newValue):
      pass

    @staticmethod
    def parseConstraints(elementName):
      ...

  class TopicType(SchemaConstraint):
    def __init__(self):
      validValues = SchemaConstraint.parseConstraints("TopicType")
      super(TopicType, self).__init__(validValues[0], validValues)

One important thing to note here is that the setter method of `validValues` has
just one line which contains `pass`. This is done in order to ensure that the
set of valid values does not get overwritten, at least not that easily.

This solution has, for one, the advantage of being adaptable, in case new values
get added to the list of valid values and, for the other, through the setter function of `value`
it can be enforced that the state of an object is always valid. 
