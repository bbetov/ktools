# Introduction
The purpose of this project is to provide a specification and reference model of the POSIX in-memory variant of the Oasis Kernel.  

The code can be compiled in Linux, POSIX-compliant Windows and native Windows.

### Background

The in-memory solution for the Oasis Monte-Carlo sampling of damage calculations (including financial module and simple outputs) is called the kernel tools or “ktools”.
The origin of ktools is that a secure compilable version of the kernel calculation in Oasis was needed for the Oasis Solutions Project so this gave the team the opportunity to bring forward the always planned C-based calculation option.  This is timely as the existing SQL-based Oasis provides a comparison calculation and also has full audit trails, so that it can be used to check that the compute engine of ktools is giving correct answers.
ktools is an independent “specification” of a set of processes which means that it defines the processing architecture and data structures and is then implemented as “reference model” which can then be adapted for particular model or business needs.  For example, we have a related project for the implementation into Oasis called "oatools" (see 'Related projects' below).

Release 1.3 (and later releases) includes an “in-memory” version of the Oasis central calculations (termed the “Kernel”) written in C++ and C to provide streamed calculation at high computational performance.  Prior releases of Oasis used SQL-based back-ends with SQL code generated by the mid-tier libraries.  The new variant was delivered as a stand-alone toolkit with R1.4 and in future releases, the compiled code will be distributed by the mid-tier.

### Architecture

The Kernel is provided as a toolkit of components (“ktools”) which can be invoked at the command line.  Each component is a separately compiled executable with a binary data stream of inputs and outputs.  
The principle is to stream data through by event end-to-end, with multiple processes being used either sequentially or concurrently, at the control of the user using a script file appropriate to the operating system.

### Language

The components can be written in any language as long as the data structures of the binary streams are adhered to.  The current set of components have been written in POSIX-compliant C++ and C.  This means that they can be compiled in Linux and Windows using the GNU compiler toolchain.

### Components

The set of components in the Reference Model provided in this release is as follows;
* **eve** is the process distributing utility. Based on the number of events in the input and the number of processes specified as a parameter, eve distributes the events to the processes. The output streams into getmodel.
* **getmodel** is a CDF plug-in to generate the CDFs for a specified list of events. It is the reference example of a plug-in using Oasis kernel format data in binary format. getmodel streams into gulcalc or can be output to a binary file.
* **gulcalc** is the core component which performs the GUL sampling calculations and numerical integration. The output is the Oasis format gul results table. This can be output to a binary file or streamed into another plug-in component, such as a Financial Module or output calculation.
* **fmcalc** is the core component which performs the insured loss calculations from the input ground up loss samples. It has the same functionality as the Oasis Financial Module.  The output is the Oasis format FM results table. This can be output to a binary file or streamed into an output calculation.
* **outputcalc** performs results analysis on the ground up loss or insured loss samples, and exports the results table to a csv file. The example given produces an event loss table.
* **cdftocsv** is a utility to output binary format CDFs to a csv.
* **gultocsv** is a utility to output binary format GULs to a csv.
* **fmtocsv** is a utility to output binary format losses to a csv. 

### Usage

Standard piping syntax can be used to invoke the components at the command line. For example the following command invokes eve, getmodel, gulcalc, fmcalc, and outputcalc, and exports the results to a csv file.
``` sh
$ eve 1 1 2 | getmodel 1 | gulcalc –S100 –C1 | fmcalc | outputcalc > output.csv
```

Example bash shell, python and vbs scripts are provided along with a binary data package in the /examples folder to demonstrate usage of the toolkit.

### Related projects
[oatools](https://github.com/OasisLMF/oatools) is a related (private) github project containing additional components which are specific to an implementation of the in-memory kernel in Oasis. 

Specifically, it contains a component **gendata** that generates the input data required for the in-memory calculations as binary files, reading from an Oasis SQL Server back-end database. In order to generate the package of executables for the Oasis implementation it is necessary to first build ktools in Windows, and then build oatools in Windows. 

gendata is an example of another class of components for extracting data to create the binary input files required by ktools, which is principally just the calculation componentware. It is planned to provide utilities which convert input data in csv format to binary format as part of the reference model, to make it easier for developers to import their data into ktools. 

[Go to Data streaming architecture overview](Overview.md)

[Back to Contents](Contents.md)