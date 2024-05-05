

# Title

## Introduce

<p style="text-align:justify; text-justify:inter-ideograph">In traditional HDL teaching, students often must focus on many details beyond the core learning objectives, such as spending excessive time on how to use EDA tools and delving into minute syntactical details of HDL. HDLGen_ChatGPT is a tool designed by Fearghal Morgan (HDLGen-ChatGPT, 2023) that streamlines this process; with the aid of HDLGen_ChatGPT and a large language model, students need only concentrate on designing digital circuits and writing pseudo-code. They would then proceed to synthesis and simulation within the AMD Vivado EDA environment.
<p style="text-align:justify; text-justify:inter-ideograph">Students initially design Data Flow Diagrams, create Signal Dictionaries, and develop Finite State Machines. Subsequently, using the GUI program in HDLGen_ChatGPT, they design modules comprising input and output signals, internal signals, and their architecture. For each Process, they define sensitive signals and output signals, along with writing corresponding pseudo-code. Similarly, the Testbench is also written in pseudo-code within the HDLGen_ChatGPT interface.

<p style="text-align:justify; text-justify:inter-ideograph">HDLGen_ChatGPT then generates prompts based on the student's design, which are submitted to the large language model. The model subsequently produces executable HDL code. Finally, HDLGen_ChatGPT automatically sets up the EDA project and launches the EDA tool (AMD Vivado). The student then completes the synthesis and simulation stages within the EDA tool. Another advantage brought by such a workflow is that students can conveniently switch between different HDLs (VHDL, Verilog, SystemVerilog, Chisel).</p>

<p style="text-align:justify; text-justify:inter-ideograph">However, this design flow is currently incomplete; it has not been adapted to work with Intel's EDA tools, and the process does not encompass verification on actual FPGA hardware. Those are precisely the issue that this project aims to address.

<p style="text-align:justify; text-justify:inter-ideograph">This project aims to refine the workflow for digital circuit design, verification, and execution on a development board using HDLGen_ChatGPT. It involves designing Data Flow Diagrams (DFDs) and Finite State Machines (FSMs) within the HDLGen_ChatGPT platform, as well as drafting pseudocode. Based on these DFDs, FSMs, and pseudocode, prompts are generated for submission to a large language model that subsequently produces High-Level Description Language (HDL) code. The process is furthered by executing pre-written TCL scripts to initiate both AMD and Intel's Electronic Design Automation (EDA) tools (Vivado and Quartus), which generate necessary C language files and Linux Drivers for a System-on-Chip (SoC) application running on the DE10-Nano board. Finally, through a user interface written in either C or Python, the performance of the SoC can be monitored on the DE10-Nano board.

![image-20240430172701496](C:\Users\chenyang\AppData\Roaming\Typora\typora-user-images\image-20240430172701496.png)

<center>Figure 1, Workflow Overview</center>

<p style="text-align:justify; text-justify:inter-ideograph">This project is merely a part of a more comprehensive project, as illustrated in Figure 1, which encompasses the development of GUIs, front-end drawing functionalities, as well as the adaptation of AMD EDA tools and the PYNQ platform for AMD development boards. However, within the scope of this particular project, it solely focuses on the adaptation of Intel EDA tools and the corresponding Development Kit. The DE10-Nano from Intel has been selected as the Development Kit for this endeavor. The choice of DE10-Nano is due to its widespread popularity and ample references in the educational market, where it serves as an effective teaching tool. Notably, the DE10-Nano consists of two parts: a traditional FPGA and an HPS (Hard Processor System) that incorporates dual Cortex-A9 cores. This means the DE10-Nano can run Linux, thereby allowing it to align with the PYNQ aspect of the broader project as illustrated in Figure 1. Furthermore, the presence of Linux facilitates the verification process for the FPGA. (Intel Corporation, n.d.)

<p style="text-align:justify; text-justify:inter-ideograph">In the AMD segment of the boarder entire project, PYNQ has been chosen as part of the development kit. It aims to simplify the development process for ZYNQ's All Programmable System-on-Chip (SoC) and enables developers to program and control FPGA hardware directly using Python language. PYNQ combines traditional hardware description languages (such as VHDL or Verilog) design workflows with the high-level programming language Python, making it easier for software engineers, data scientists, and non-traditional FPGA designers to leverage the powerful parallel processing capabilities and real-time performance of FPGAs.

## Chapter 1, Design Process

### Data Communication

<p style="text-align:justify; text-justify:inter-ideograph">Using DE10-Nano as a simple FPGA</p>

### Project Architecture

![hdlgen_chatgpt_architecture.drawio](C:\Users\chenyang\Downloads\hdlgen_chatgpt_architecture.drawio.png)

<center>Pict 1, The Architecture of Whole Project</center>

<p style="text-align:justify; text-justify:inter-ideograph">For a project which can be compiled into a prog</p>

### Program Design

<p style="text-align:justify; text-justify:inter-ideograph">This document provides a Python script that automatically generates a Quartus project. The script compiles the project into a programmable file, which is then used to reflash the FPGA on the DE10-Nano. Additionally, it enables communication between the FPGA and the Linux running on the DE10-Nano's HPS.</p>

<p style="text-align:justify; text-justify:inter-ideograph">Based on the process discussed in chapter 1, this Python program should have the following functions.</p>

<ol>
    <li style="text-align:justify; text-justify:inter-ideograph">Retrieve necessery information for the HDLGen-ChatGPT project, including the user's design name, paths to the top-module and sub-module HDL files, IO ports and the testbench.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Connect the variety-width ports (ranging from 1 to 64 bits) to 64-bit PIOs, recording the connections details.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Create a project folder and subfolders. Including an IP folder containing the HDL for PIOs.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Generate the '_hw.tcl' TCL script for the PIOs within the project folder.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Create the whole Quartus project TCL, which will include the HDL files into the Quartus project.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Generate a TCL script for the Platform Designer based on the connection details.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Create a top-level HDL file that act as a bridge between the user's design and the components generated by the Platform Designer.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Generate a BAT file to automate the generation and compilation of the Quartus project.</li>
    <li style="text-align:justify; text-justify:inter-ideograph">Create an XML file documenting the testbench and connections between ports and PIOs.</li>
</ol>

The functions above was placed in the different module.

<ul>
    <li style="text-align:justify; text-justify:inter-ideograph"><b>HDLGenDataType.py: </b>this module contains several classes as the abstract of data type including port and PIO.</li>
    <ul>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>Port: </b>abstract of the Port.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>PIO: </b>abstract of PIO.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>PortPIOAdapter: </b>an adapter charge for connecting port and PIO .</li>
    </ul>
    <li style="text-align:justify; text-justify:inter-ideograph"><b>HDLGenProjectParser.py: </b>this module aims to parse the HDLGen project to get the necessery information. It has the following classes.</li>
    <ul>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>PathBuilder: </b>a class aims manage the path which converts relative path into the absolute path and save path as a string in posix format.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>HDLGenProjectParser: </b>a class aims to parse the HDLgen project from an xml file.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>HDLGenEnvironmentParser: </b>a class aims to parse the HDLgen project environment from an xml file.</li>
    </ul>
    <li style="text-align:justify; text-justify:inter-ideograph"><b>QuartusProjectGenerator.py: </b>this module ...</li>
    <ul>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>Connection: </b>This class abstracts port-PIO connecitons, recording which port is connected to which PIO and identifying the start bit of the connection.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>Quartus: </b>This class records the paths of Quartus' executable file.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>FolderStructureBuilder: </b>This class will construct the folder for the quartus project. It will create quartus project and ip folder and write hdl and hw.tcl files into it.</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>QuartusTCLScriptGenerator: </b>...</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>QsysTCLScriptGenerator: </b>...</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>TopLevelHdlGenerator: </b>...</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>BatchFileGenerator: </b>...</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>XMLDocumentation: </b>...</li>
        <li style="text-align:justify; text-justify:inter-ideograph"><b>QuartusProjectManager: </b>...</li>
    </ul>
</ul>


Details of classes:

<center>Classes in HDLGenDataType.py</center>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>Port</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td rowspan="4"><b>Member variable</b></td>
        <td>name: str</td>
        <td>The name of this IO port.</td>
    </tr>
    <tr>
    	<td>direction: str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">An input port or an output port. The values are restricted to 'in' and 'out'.</td>
    </tr>
    <tr>
    	<td>type: str</td>
        <td>A single bit or a bus.</td>
    </tr>
    <tr>
    	<td>width: int</td>
		<td>The width of the signal.</td>
    </tr>
    <tr>
        <td rowspan="3"><b>Member method</b></td>
        <td>init()</td>
        <td>Initial the member variable by calling Getter/Setter method.</td>
    </tr>
    <tr>
        <td>Getter/Setter</td>
        <td>Get/set method of the memver variables.</td>
    </tr>
    <tr>
    	<td>__type_parser(value:str) -> bool</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Get width of the port with regular expression from the type. This function will be called by Setter of type. It will return false if the parsing is fail.</td>
    </tr>
</table>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>PIO</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td rowspan="4"><b>Member variable</b></td>
        <td>name: str</td>
        <td>The name of this PIO.</td>
    </tr>
    <tr>
    	<td>direction: str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">An input PIO or an output PIO. The values are restricted to 'in' and 'out'.</td>
    </tr>
    <tr>
    	<td>address: int</td>
        <td>The bias of address.</td>
    </tr>
    <tr>
    	<td>available_bit: int</td>
		<td style="text-align:justify; text-justify:inter-ideograph">The highest available bit of this PIO. When a port is connected to a PIO, it will connect from higher bit to lower bit. This variable is initialed with 63.</td>
    </tr>
    <tr>
        <td rowspan="5"><b>Member method</b></td>
        <td>init()</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Initial the member variable by calling Getter/Setter method.</td>
    </tr>
    <tr>
        <td>Getter/Setter</td>
        <td>Get/set method of the member variables.</td>
    </tr>
    <tr>
    	<td>connect_port(port: Port) -> int</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will receive an instance of Port. After the port was connected on PIO, this function will return the start bit of connect.</td>
    </tr>
    <tr>
    	<td>has_spare_space_for(self, port_width: int) -> bool</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function is to judge whether this PIO has enough bits to connect with the port.</td>
    </tr>
    <tr>
    	<td>_connect_to_available_bits(self, port: Port) -> int</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will be called by connect_port(). It will modity the available_bit after the connection and return the start bit of connect.</td>
    </tr>
</table>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>PortPIOAdapter</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td><b>Member variable</b></td>
        <td>pios: List[PIO]</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This list contains PIOs which were already created. After a PIO has been created, the PIO will be appended in this list.</td>
    </tr>
        <td rowspan="4"><b>Member method</b></td>
        <td>_direction_convert(mode: str) -> str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will receive a string 'in' or 'out', and upon receiving one of them, it will return another. Because the direction of user's design is opposite with PIO's direciton.</td>
    </tr>
    <tr>
        <td>_find_or_create_pio_for_port(self, port: Port) -> PIO</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will return an instance of a PIO. This function first will check out the last PIO in the list pios for whether it has enought bits to connect with the port. If it is true, it will return that PIO. If not, it will create a new instance of PIO and appends it in the List pios.</td>
    </tr>
    <tr>
    	<td>connect_port(self, port: Port) -> Tuple[PIO, int]</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will receive an instance of Port, It will connect it with a PIO. First it will get an instance of PIO by calling the function _find_or_create_pio_for_port(). Then it will call the member function of pio, connect_port(), to connect the port and the pio.</td>
    </tr>
</table>


<center>Classes in HDLGenProjectParser.py</center>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>PathBuilder</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td><b>Member variable</b></td>
        <td>path: str</td>
        <td>A string to store the path.</td>
    </tr>
    <tr>
        <td rowspan="2"><b>Member method</b></td>
        <td>init()</td>
        <td>Initial the member variable by calling Getter/Setter method.</td>
    </tr>
    <tr>
        <td>Getter/Setter</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Get/set method for the path. First, it will convert it to an absolute path. Then it will convert as a string in posix path format.</td>
    </tr>
</table>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>HDLGenProjectParser</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td><b>Member variable</b></td>
        <td>path: str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">A string to store the path of the user's HDLGen project which is an xml file.</td>
    </tr>
    <tr>
        <td rowspan="8"><b>Member method</b></td>
        <td>init()</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Initial the member variable by calling Getter/Setter method.</td>
    </tr>
    <tr>
        <td>Getter/Setter</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Get/set method for the member variable.</td>
    </tr>
    <tr>
        <td>parser(self) -> Tuple[str, str, str, List[Port], str]</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function aims retrieve series necessery information from the HDLGen project. It will return a couple of values: project_name, top_hdl_path, environment_path, ports, testbench. This function will call the following member functions to get those return values.</td>
    </tr>
    <tr>
    	<td>_design_name_parser(root: Element) -> str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">The function retrieves the design name of users design.</td>
    </tr>
    <tr>
    	<td>_top_level_hdl_parser(root: Element) -> str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">
            The function retrieves the path of HDL file of user's design.<br>
            This function will first retrieve the location of user's HDLGen project. Then retrieve the subfolder of the HDL files.<br>
        </td>
    </tr>
    <tr>
    	<td>_environment_path_parser(root: Element) -> str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">
        	Get the environment path. This function will assume that the environment path is an absolute path.
        </td>
    </tr>
    <tr>
    	<td>_signals_parser(root: Element) -> List[Port]</td>
    	<td style="text-align:justify; text-justify:inter-ideograph">This function will retrieve the signals, then create an instance of port, and return the instance.</td>
    </tr>
    <tr>
    	<td>_testbench_parser(root: Element) -> str</td>
        <td style="text-align:justify; text-justify:inter-ideograph">This function will retrieve a testbench and return it as a string.</td>
    </tr>
</table>

<table>
    <tr>
        <td><b>Class name</b></td>
        <td><b>HDLGenEnvironmentParser</b></td>
    </tr>
    <tr>
    	<td></td>
        <td></td>
        <td><b>Description</b></td>
    </tr>
    <tr>
        <td><b>Member variable</b></td>
        <td>path: str</td>
        <td>A string to store the environment path.</td>
    </tr>
    <tr>
        <td rowspan="3"><b>Member method</b></td>
        <td>init()</td>
        <td>Initial the member variable by calling Getter/Setter method.</td>
    </tr>
    <tr>
        <td>Getter/Setter</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Get/set method for the path. First, it will convert it to an absolute path. Then it will convert as a string in posix path format.</td>
    </tr>
    <tr>
        <td>parser(self) -> List[str]</td>
        <td style="text-align:justify; text-justify:inter-ideograph">Get/set method for the path. First, it will convert it to an absolute path. Then it will convert as a string in posix path format.</td>
    </tr>
</table>

## Linux Image

## Flesh FPGA

### Introduce to the Device Tree

The definition of device tree is clearly provided by Wikipedia.

> Devicetree is a datastructure describing the hardware components of a particular computer so that operating system's kernal can use and manage those components, including the CPU or CPUs, the memory, the buses and the integrated peripherals.

> Given a currect device tree, the same compiled kernal can support different hardware configuration within a wider architecture family.

Device tree is mandatory for ARM devices as a remedy for vast number of forks by move the significant part of hardware description out of the kernal binary. The hardware description move into the compiled device tree blob, which is handed to the kernal by boot loader.

## GUI on Jupyter Notebook

`ipywidgets` is a good library that ...
