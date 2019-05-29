.. title: Dev Logs
.. slug: dev-logs
.. date: 2019-05-29 15:00:00 UTC
.. tags: daily
.. category: DevLog
.. link: 
.. description: This is a daily updated log of the work I do on the BCF-plugin for FreeCAD
.. type: text

.. _`class diagram`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/tree/master/doc
.. _`BCF-plugin`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/
.. _`commit 1c34ad9`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/1c34ad907b7fc56cd96aa2fc5aa133e3f445a24b
.. _`commit 31ef931`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/31ef931b3637c90ca0c8252f71dd635e66a843fa 
.. _`commit 0a1081b`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/0a1081bb1fe26dc729d3a2b708fde491b3a31505
.. _`commit bae270f`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/bae270f1127039ae78876bf6f3785c48ec0e30b9
.. _`commit 3c0b9d0`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/3c0b9d0a1beed02816cd15b0a5186368d7361f7d
.. _`commit f62ed23`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/f62ed23a73e209fc69995fccedf4e20beddf7632
.. _`commit cccde6a`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/cccde6ae2bdf52f21f5e7ecfeb68cc89957af29e
.. _`commit 9a79162`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/9a791627b16b09e9c6641975e6fb0a9bf7e72856
.. _`mockup of the plugin interface`: https://forum.freecadweb.org/viewtopic.php?p=310515#p310515
.. _`schema constraints revisited`: link://slug/schema-constraints-revisited
.. _`branch unit_tests ./src/tests`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/tree/unit_tests/src/tests
.. _`reader.buildProject()`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/blob/991d967ab5fc00f8960bbc938c727d11e42c950c/src/bcf/reader.py#L145
.. _`reader.py`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/blob/master/src/bcf/reader.py
.. _`bimcollab website`: https://www.bimcollab.com/en/Support/Support/Downloads/Examples-templates

This is a daily updated log of the work I do on the `BCF-plugin`_ for FreeCAD

**May 29th:** Already started refactoring a bit in `reader.py`_. Went away from
using `ZipFile` objects when operating on the zipFile to the extracted version
of the zip file instead. Written function `reader.readInFile()` that shall read
the complete BCF file into the data structure (see `commit 9a79162`_). During testing 
I noticed that the example BCF file from the `bimcollab website`_ is not valid
becaus it defines an empty node `Header` in `markup.bcf` of topic
`ebb1a8bf-6d1d-4aad-a875-61ad3cc40d30` which is prohibited by `markup.xsd` of
BCF version 2.1.

**May 28th:** Created a new blog post about the way the schema constraints are
handled now (`schema constraints revisited`_). Change configuration file of the
blog to reflect the current project, instead of the FreeCAD development blog.
Started a unit-test suite, see `branch unit_tests ./src/tests`_, and test cases
written for `reader.buildProject()`_. Written function in `reader.py` that
parses `bcf.version` and returns the version number as string, see `commit
cccde6a`_

**May 27th:** Complete the python representation of the class diagram `commit
0a1081b`_. Also advanced to the point where a `.bcf` can be opened, every XML
file be validated (in theory, only tested it with project.bcfp yet) and the
contents of `project.bcfp` are can be written to an object of `Project`. For
more details refer to following commits: 

- `commit bae270f`_: Add util.py and reader.py
- `commit 3c0b9d0`_: util.py: add schemaValidate 
- `commit f62ed23`_: reader.py: add buildProject 

**May 26th:** Update Comment in class diagram. For more info see `commit 1c34ad9`_, and create `mockup of the plugin interface`_.
Write part one of the class model in python see `commit 31ef931`_.
