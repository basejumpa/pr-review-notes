Review of `PR-SPL/13`_
**********************

.. _PR-SPL/13: https://github.com/avengineers/SPL/pull/13

Log of what I do:

Sync local environment to the content of the PR
===============================================

In vs-code Open my vscode-workspace **SPLDemo_and_SPL** which comprises my local repositories

- **avengineers/SPL** and also
- **avengineers/SPLDemo**

Pull feature branch of PR SPL-13 **add_kconfig**  from upstream repository **github.com/avengineers/SPL.git**

Pull branch **develop**  from upstream repository **github.com/avengineers/SPLDemo.git**

.. _link-target:

Remove the **build** folder in **SPLDemo** to force a clean build later on. 

In statusline set

- Active Folder **SPLDemo**
- Active Kit **test**
- Default build target **all**
- Current build variant **spl/alpha**

In statusline click **Run CTest**.

Watch **Output - CMake/Build**.

Investigate that output.

.. code-block::

    [main] Building folder: SPLDemo 
    [...]
    [cmake] Clone/Update SPL version: v1.1.5 from https://github.com/avengineers/SPL.git
    [cmake] Cloning into './build/spl-core'...
    [cmake] Note: switching to '58c1b8dc81fa6b461c00a460bb66b96da29a5070'.
    [...]
    [ctest] 1/3 Test #1: component__var_a.test_dummyInterface ..........   Passed    0.07 sec
    [...]
    [ctest] CTest finished with return code 0

It seems that the build has not used the **SPL** of PR-13.

Remember that Alexandru has shown us that environment variable which enables us to use a local **SPL** instead of an automatic retrieved one.

Search the two codebases for that environment variable and the functionality around it.

Begin with codebase of **SPL**.

Use GitGraph to first focus on the latest changes.

Can not find that environment variable.

Dive into codebase of **SPLDemo**.

Use again GitGraph to focus on git history.

Notice that in **SPLDemo** there is a feature branch **origin/feature/integrate-kconfig**.

Diff between **develop** and **origin/feature/integrate-kconfig** reveals the relevant change in file **CMakeLists.txt**:abbr:

:: code-block::

    if(DEFINED ENV{SPLCORE_PATH})
    message(WARNING "SPLCORE_PATH defined! Use fixed SPL-CORE version from: $ENV{SPLCORE_PATH}")
    include($ENV{SPLCORE_PATH}/cmake/spl.cmake)
    [...]

Conclusion: Switch to feature branch **feature/integrate-kconfig** in **SPLDemo** is necessary in order to run the code of `PR-SPL/13`_.

Switch to branch **feature/integrate-kconfig** in **SPLDemo**

In order to set environment variable ``SPLCORE_PATH`` do

- close vscode
- in powershell do ``PS C:\d\repos\spl> $env:SPLCORE_PATH = 'c:\d\repos\spl'``
- in the same terminal re-open vscode via ``PS C:\d\repos> code .\SPLDemo_and_SPL.code-workspace``

Re-do steps beginning with removing the build folder:

Remove the build folder in **SPLDemo**.

In statusline click **Run CTest**.

Watch **Output - CMake/Build**.

:: code-block::

    [...]
    [cmake] CMake Warning at CMakeLists.txt:12 (message):
    [cmake]   SPLCORE_PATH defined! Use fixed SPL-CORE version from: c:\d\repos\spl
    [...]
    [ctest] 1/3 Test #1: component__var_a.test_dummyInterface ..........   Passed    0.07 sec
    [...]
    [ctest] CTest finished with return code 0

Conclusions: 

- Build of **SPLDemo** uses now local **SPL** instead downloading to build-folder the version of it which is defined among the dependencies in file ``SPLDemo/dependencies.json``.
- Build and test succeeds.


Dive into new content
=====================

Investigate new content of **SPLDemo**:

- Root contains new file ``Kconfig``. This represents the featuremodel (and the datamodel for variant "default").
- It contains two features (boolean) and a variable of type string.
- File ``build.bat`` also takes new environment variable ``SPLCORE_PATH`` into account.
- Variant **alpha** has a file ``config.txt``. This represents the delta-datamodel deltaalpha. Datamodel "alpha" is the join of "default + deltaalpha"
- The header file ``component.h`` of component with name **component/var_a** now includes a header file ``autoconf.h``.
- The legacy-component with name **platform_a** contains changes. These changes are not related to ``Kconfig``, ``config.txt`` nor ``autoconf.h``.

Repository **SPL** contains the architectural element **SPLCore**.

Investigate new content of **SPL**:

- 