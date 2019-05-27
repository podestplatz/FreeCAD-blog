.. title: Dev Logs
.. slug: dev-logs
.. date: 2019-05-25 07:15:45 UTC
.. tags: daily
.. category: DevLog
.. link: 
.. description: This is a daily updated log of the work I do on the BCF-plugin for FreeCAD
.. type: text
.. **May 27th:** Write the python representation of the `class diagram`_.

.. _`class diagram`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/tree/master/doc
.. _`BCF-plugin`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/
.. _`commit 1c34ad9`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/1c34ad907b7fc56cd96aa2fc5aa133e3f445a24b
.. _`commit 31ef931`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/31ef931b3637c90ca0c8252f71dd635e66a843fa 
.. _`commit 0a1081b`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/0a1081bb1fe26dc729d3a2b708fde491b3a31505
.. _`commit bae270f`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/bae270f1127039ae78876bf6f3785c48ec0e30b9
.. _`commit 3c0b9d0`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/3c0b9d0a1beed02816cd15b0a5186368d7361f7d
.. _`commit f62ed23`: https://github.com/podestplatz/BCF-Plugin-FreeCAD/commit/f62ed23a73e209fc69995fccedf4e20beddf7632
.. _`mockup of the plugin interface`: https://forum.freecadweb.org/viewtopic.php?p=310515#p310515

This is a daily updated log of the work I do on the `BCF-plugin`_ for FreeCAD

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
