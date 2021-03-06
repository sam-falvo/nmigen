Introduction
############

nMigen is a Python-based tool that aims at automating further the VLSI design process.

nMigen makes it possible to apply modern software concepts such as object-oriented programming and metaprogramming to design hardware. This results in more elegant and easily maintained designs and reduces the incidence of human errors.

.. _background:

Background
**********

Even though the |mm|_ had many successes, it suffers from several limitations stemming from its implementation in manually written Verilog HDL:

.. |mm| replace:: Milkymist System-On-a-Chip
.. _mm: http://m-labs.hk/m1.html

#. The "event-driven" paradigm of today's dominant hardware descriptions languages (Verilog and VHDL, collectively referred to as "V*HDL" in the rest of this document) is often too general. Today's FPGA architectures are optimized for the implementation of fully synchronous circuits. This means that the bulk of the code for an efficient FPGA design falls into three categories:

   #. Combinatorial statements
   #. Synchronous statements
   #. Initialization of registers at reset

   Because V*HDL do not follow this organization, a lot of repetitive manual coding is needed.  This introduces greater opportunity for human error, petty issues, and especially confusion for beginners:
   
   #. `wire` vs. `reg` in Verilog
   #. forgetting to initialize a register at reset
   #. deciding whether a combinatorial statement must go into a process/always block or not
   #. simulation mismatches with combinatorial processes/always blocks
   #. different design strategies for ASICs versus FPGAs
   #. and more...
   
#. V*HDL support for composite types is very limited. Signals having a record type in VHDL are unidirectional, which makes them clumsy to use e.g. in bus interfaces. There is no record type support in Verilog, which means that a lot of copy-and-paste has to be done when forwarding grouped signals.

#. V*HDL support for procedurally generated logic is extremely limited. The most advanced forms of procedural generation of synthesizable logic that V*HDL offers are CPP-style directives in Verilog, combinatorial functions, and ``generate`` statements. Nothing really fancy, and it shows. To give a few examples:

   #. Building highly flexible bus interconnect is not possible. Even arbitrating any given number of bus masters for commonplace protocols such as Wishbone is difficult with the tools that V*HDL puts at our disposal.
   #. Building a memory infrastructure (including bus interconnect, bridges and caches) that can automatically adapt itself at compile-time to any word size of the SDRAM is clumsy and tedious.
   #. Building register banks for control, status, and interrupt management for cores can also largely benefit from automation.
   #. Many hardware acceleration problems can fit into the dataflow programming model. Manual dataflow implementation in V*HDL has, again, a lot of redundancy and potential for human errors. See the Milkymist texture mapping unit [mthesis]_ [mxcell]_ for an example of this. The amount of detail to deal with manually also makes the design space exploration difficult, and therefore hinders the design of efficient architectures.
   #. Pre-computation of values, such as filter coefficients for DSP or even simply trigonometric tables, must often be done using external tools whose results are copy-and-pasted (in the best case, automatically) into the V*HDL source.

.. [mthesis] http://m-labs.hk/thesis/thesis.pdf
.. [mxcell] http://www.xilinx.com/publications/archives/xcell/Xcell77.pdf p30-35
   
Enter nMigen, a Python toolbox for building complex digital hardware. We could have designed a brand new programming language; but, that would have been reinventing the wheel instead of being able to benefit from Python's rich features and immense library. The price to pay is a slightly cluttered syntax at times when writing descriptions in HDL. However, we believe this is totally acceptable, particularly when compared to VHDL. ;-)

nMigen is made up of several related components:

#. the base language, HDL
#. a library of small generic cores
#. a simulator
#. a build system

Installing nMigen
*******************

There are several different methods you can use to install nMigen.  Which one works for you will depend on your project's configuration and your development team's needs.

The examples below assume your nMigen checkout path is assigned to the ``$NMIGEN_CHECKOUT`` variable.  Your hardware project's path would then be assigned to ``$PROJECT_HOME``.

Using Virualenv
===============

Inside your hardware project's directory, you'll want to create a virtual environment in which to place `nMigen` and its dependencies.

::

    $ cd $PROJECT_HOME
    $ virtualenv3 env3

You'll then want to activate it.

::

    $ . env3/bin/activate

Once there, you can then install nMigen like so:

::

    $ pushd $NMIGEN_CHECKOUT
    $ python3 setup.py install
    $ popd

Once this step is complete, every time you (re-)activate your virtualenv, nMigen will be available for use.

Using ``setup.py`` Globally
===========================

Either run the ``setup.py`` installation script or simply set ``PYTHONPATH`` to the root of the nMigen source directory.

Explicitly in a ``requirements.txt`` file
=========================================

nMigen can be referenced in a ``requirements.txt`` file (used for ``pip3 install -r requirements.txt``) via ``-e git+http://github.com/m-labs/nmigen.git#egg=nmigen``. See the pip3 documentation for more information.

.. note::
   You **must** use an appropriate version of ``pip`` for your distribution's Python 3 environment.  nMigen **will not** run with Python 2.  Some distributions (e.g., Debian or Debian-derived distributions) use ``pip3`` while others (e.g., Void Linux) support the use of either ``pip`` or ``pip3`` depending on your local configuration.  If possible, use a virtual environment, as shown above, to guarantee a consistent Python user experience without sacrificing convenience.  Otherwise, make sure you always opt to use the Python 3 tools.

Feedback
********
Feedback concerning nMigen or this manual should be sent to the M-Labs developers' mailing list ``devel`` on lists.m-labs.hk.
