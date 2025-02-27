Download link :https://programming.engineering/product/laboratory-exercise-7-memory-and-the-vga-display-2/

# Laboratory-Exercise-7-Memory-and-the-VGA
Laboratory Exercise 7 Memory and the VGA
The purpose of this exercise is to learn how to create and use on-chip block random access memories (BRAMS) as well as use the video graphics adapter (VGA).

Work ow

For each part of the lab, you should begin by writing and testing Verilog code, using Model-Sim. Once your design works in simulation, you should compile it with Quartus. You must simulate your circuit with ModelSim using reasonable test vectors written in the format used in Lab 2 for the simulation les. You should be prepared to show schematics, Verilog, and simulations to your TA, if requested. You will not be helped with debugging your code if you do not have schematic and ModelSim simulations. Issues identi ed with the tester, without doing your own ModelSim simulations will not get help.

Warning: This lab document is quite detailed and covers a lot of new information. Please read through it carefully. Do not rush to just get the labs done as you will spend much more time debugging your code than making progress. Also, it is highly recommended that you watch the tutorial videos on Quercus in the Lab 7 page.

Part I

In addition to lookup tables (LUTs) and ip ops, the FPGA provides exible embedded memory blocks that can be con gured into various bit widths and depths along with many other parameters. To access these blocks you will use another feature of Quartus that can build modules of various functions. In this part of the lab exercise you will create a small RAM block and interact with it to understand how it works. Using the Quartus IP catalog you will rst create a module for the desired memory. This will create a module that you can then instantiate in your designs when you need such a memory block. You can test the memory module using ModelSim and, if you have a board, using the switches and HEX displays for inputs and outputs.

The memory module we would like to create is shown in Figure 1. It consists of a memory block, address register, data register and a control register. You can see that the address and input data are stored in a register as well as the Write Enable control signal. Using

Write

(a) RAM organization



Clock

Address

A0

A1

A2

A3

Clock

DataOut

A0

D0

D1

D2

D3

Address

A1

A2

A3

WriteEn

D3

DataOut

D0

D1

D2

WriteEn

Figure 2: Timing diagram for read operations.

Clock

Address

A0

A1

A2

A3

Clock

DataIn

D0

D1

D2

D3

Address

A0

A1

A2

A3

WriteEn

DataIn

D0

D1

D2

D3

WriteEn

Figure 3: Timing diagram for write operations. Note that only addresses A1 and A2 are written.

3


Click Finish and Finish again to generate the new Verilog le, part1.v.

Examine the newly created Verilog le. Observe that it declares a module with the required ports as shown in Figure 1. You can now instantiate the module into any design.

Simulate your part1 module with ModelSim to satisfy yourself that your circuit is working. Be prepared to justify that your test cases are enough to give con dence that your circuit is working.

Since you are adding the BRAM IP to your project, you will need to add an additional parameter to simulate this. The following shows how to do this in your Modelsim scripts:

vsim -L altera_mf_ver <module-name>

For some more information on what this does, read Section 4 below. When you are satis ed with your simulations, you can submit to the Automarker.

Create a new Quartus project for your circuit. You will need a top-level module to make connections from the instantiation of your part1 module to the switches, pushbutton and HEX displays of the DE1-SoC board. The connections used are shown in Table 1.

part1 Port Name

Direction

DE1-SoC Pin Name

Clock

Input

KEY[0]

DataIn[3:0]

Input

SW[3:0]

Address[4:0]

Input

SW[8:4]

WriteEn

Input

SW[9]

DataOut[3:0]

Output

HEX0

DataIn[3:0]

HEX2

Address[7:4]

HEX4

Address[8]

HEX5

Table 1: Module part1 mapping to DE1-SoC pin names

Simulate your new circuit with the DE1-SoC connections with ModelSim to ensure that you have done the connections correctly.

Compile the project to generate a bitstream to make sure your code can at least be synthesized.

Download the compiled circuit into the FPGA chip. Test the functionality of the circuit.


Figure 4: VGA adapter module schematic. Resetn is not shown.

Part II

For this part you will learn how to display simple images on a VGA display. Your task is to design a circuit to draw a lled square on the screen at any location in any colour.

The DE1 SoC board has a VGA interface that enables you to connect the board to a VGA monitor. You are provided with a VGA adapter module in Verilog. It has an input interface of a pixel coordinate and a colour for the pixel. A pixel is one of the dots drawn on your screen. A VGA display is made up of 640 480 pixels. The VGA adapter logic will draw the colour on the screen at the speci ed pixel coordinate. The VGA adapter that you will use is shown in Figure 4.

The inputs to the adapter module are similar to the memory interface in Part I. The (X,Y) inputs specify a pixel location on the screen while Colour speci es the pixel colour. Note that (X,Y) = (0,0) is the top left corner of the screen. The Plot input is a write enable signal that tells the controller to update the pixel speci ed by (X,Y) with the value Colour at the next clock edge. The outputs of the VGA adapter drive the o -chip video digital-to-analogue converters (DACs) that subsequently drive the monitor.

3.1 (OPTIONAL) If you want to learn more about VGA and the VGA adapter

You are given enough information in this lab sheet to use the VGA adapter module, but if you are curious to learn more about the details you can get more information at http: //www.eecg.toronto.edu/~jayar/ece241_08F/vga/. If you do not want to, you can skip this part and move onto Section 3.2.

In terms of other changes you can make to the VGA, you may want to change the resolution


and colour quality, in which case you should look at \Changing Adapter Parameters”. If you stick to using the names of the ports used at that site, you will get the correct pin mapping through the DE1 SoC.qsf le that you have been provided, except for two port names that must be changed, as described in the next paragraph.

A word of caution if you want to try the example code at that web site. You will see mention of the DE1 and DE2 boards. You are using the DE1-SoC, so the examples may not work directly. In particular, watch for the names of two signals in the old examples. The signals were changed from VGA BLANK and VGA SYNC to VGA BLANK N and VGA SYNC N, respectively, for the DE1-SoC so you will have to change any of the example code accordingly. The les you are provided for this lab are for the DE1-SoC and will not need the above modi cation.

3.2 Constraints and Colour Coding

To make things a bit easier, you will work with a screen that is 160 pixels wide by 120 pixels high. Compare this with standard VGA, which is 640 480 pixels and your HD TV at 1920

1080 pixels or 4K UHD at 3,840 2,160 pixels. We are also using only three bits for the colour of a pixel. The three bits correspond to red, green, and blue, which is called an RGB colour coding. There is one bit for each colour so if you want to draw red, then input (1,0,0) as the colour bits. You can combine colours as well, such as (0,1,1), which will draw green and blue resulting in cyan. A very fancy display may have 8 bits per colour, for a total of 24 bits of colour, to de ne the colour of a pixel.

An important consideration is the amount of memory required to store the pixels. The reason why we are using a very small screen with only three bits per pixel is so that we only need a small memory. How many bits of memory are required to store an image size of 160 120 pixels where each pixel is three bits? How many bits of memory are required to store a UHD image size of 3,840 2160 pixels where each pixel is 24 bits?

In Figure 4, the memory block shown is called the frame bu er. Through the user interface, a user can update the contents of the frame bu er. The VGA Controller in Figure 4 con-tinuously reads the frame bu er and displays the image in the frame bu er by driving the signals that are sent to the monitor.

3.3 Your Circuit

Your circuit will have the following inputs:

iClock The clock input to your circuit.

iResetn Active low synchronous reset.


iXY Coord, iLoadX, iColour The circuit should draw a 4 4 pixel square whose upper-left corner is at the (X,Y) coordinate speci ed. The square should be lled with the colour speci ed by the iColour input. Recall that coordinate (0,0) is the top left corner of the screen for our VGA adapter.

The input iXY Coord is used to input the X and Y coordinates. This is done in two steps. First the X coordinate is input at iXY Coord and X is loaded into a register when iLoadX is set high and then low. This is similar to how inputs were handled in lab 6 i.e. as long as iLoadX is held high, the iXY Coord value should get stored into an X register after every positive clock edge. After iLoadX is pulsed, then the Y coordinate should be input at iXY Coord. It will be loaded when the input iPlotBox is pulsed to start the drawing. The iColour value should also be loaded into a register when the input iPlotBox is pulsed. This is similar to how inputs were handled in lab 6 i.e. as long as iPlotBox is held high, the iXY Coord value should get stored into a “Y register” and iColour should get stored into a “Colour register” after every positive clock edge. The assumption is that iXY Coord will be connected to some switches. Using one input port for both X and Y reduces the number of switches required. Also, assume iLoadX will be connected to a pushbutton.

iPlotBox The lled square should be drawn when iPlotBox is pulsed high and then low.

The assumption is that iPlotBox will be connected to a pushbutton.

iBlack When pulsed high and low, this input will cause the entire screen to be cleared to black (Black). Note that black corresponds to colour (0,0,0). The assumption is that iBlack will be connected to a pushbutton.

Your circuit will have the following outputs: The outputs are intended to connect directly to the corresponding inputs of the VGA adapter.

oX, oY The X and Y pixel coordinates of the display.

oColour The colour to draw at the speci ed (oX,oY) coordinate.

oPlot The write enable to tell the VGA Adapter to draw the oColour at the speci ed coordinate.

oDone Must be set high after your circuit has completed plotting a box or clearing the screen. After your circuit is done plotting a box or clearing the screen, must remain high until iPlotBox or iBlack is pulsed high and then low.

After a square has been drawn, your circuit should allow additional squares to continue to be drawn (say, at di erent locations in possibly di erent colours). The high-level design of the circuit for the system is given in Figure 5. It contains three major blocks:


Inputs

ResetN

Colour

ResetN

X position

ResetN

X

Y position

Y

Datapath

VGA

Adapter

Black Plot ResetN

Colour

ControlA

Plot

ControlB

ControlC

FSM


Figure 5: Design Overview – Finite State Machine, Datapath and VGA Adapter. Although not shown, the Resetn signal should be connected to all the registers in the circuit (including the FSM state registers).

The VGA adapter is responsible for the drawing of pixels on the screen.

The datapath that contains arithmetic circuitry and registers, controlled by the FSM, that compute the (X,Y) values that are fed into the VGA adapter to draw the 4 4 lled square.

A nite state machine (FSM) that serves as a controller for the datapath. Some output control lines are shown in Figure 5 as examples. You may put in whatever other signals you require.

Build your FSM as a Moore machine. It will make it easier to think about how to respond to the various inputs, which are happening at human time scales.

3.4 How to think about this design

An important part of doing design is to rst think through the steps you can take to get to the nal design. As you start to deal with greater and greater complexity, you must realize that you cannot build everything at once and then hope that it will all work the rst time you try to test it. You should take incremental steps.

To make the design of this Part II more incremental, what can you start with that demon-strates some basic functionality and helps you understand how the VGA adapter works? The ultimate goal is to draw a 4 4 lled square. How do you do that? You have to draw one pixel, move to the next pixel coordinate, draw that pixel, etc. Your FSM will need to be changing the pixel coordinate to reach the appropriate pixels and writing the colour value at each pixel. However, what can you do rst that is simpler, but achieves important functionality?

Try just drawing one pixel on the screen. This will use a much simpler FSM, so it will be easier to get working. In simulation, you just want to see that the correct inputs make it to the VGA adapter interface. Make the rst pixel red. This requires you to set X, set Y, and iColour. Then set iPlotBox to plot the pixel. Draw several pixels at di erent coordinates. Change the colour to green, set iPlotBox, then blue, set iPlotBox and see if the pixel colour changes accordingly each time. This will test that you can input X and Y correctly and input the correct colour. If you can do this, then you know that you are using the X, Y and iColour inputs correctly.

Now you can try testing the circuit with a display. You can do this with a real board and VGA monitor.

Note that one pixel will be quite small, so you may need to look very carefully to nd it. When testing on the board this will also show that you are using the VGA adapter correctly.

Once you are con dent that you are correctly drawing and displaying one pixel at any desired coordinate, you can try plotting the 4 4 square knowing that you already have the ability to draw one pixel. If you have problems with this step, then the problem is most likely in the new code that you have added, so you can focus your debugging on that code.

You also need to clear the screen, i.e., make the screen all black. Do this next. It will require a slightly di erent function that writes (0,0,0) into all pixels. If you think a bit about this, you can create a module that can draw any size box at a speci ed coordinate with a speci ed colour. It will take a coordinate, colour and X and Y dimensions as an input. You can use it to draw both the 4 4 box and to ll the screen, which is just a very large box!

At this point, you will have achieved all the functionality required for this part.

3.5 The Top-Level Module

The skeleton code for the top-level module of your design, called part2, is provided to you in the le part2 template.v. Copy this le and rename it to part2.v. This is the le you will submit to the Automarker after you add your code.

Note that the template includes two parameters, X SCREEN PIXELS and Y SCREEN PIXELS, which de ne the size of screen being used. The default values are set to the 160 120 screen size used by the provided VGA adapter. Use these parameters anywhere you need to specify the size of the screen in your code. The parameters can be changed when the module is instantiated so that the module can work for di erent sizes of screens. The Automarker will expect these parameters and set them to test for an 8 5 screen size, which reduces the time required for the Automarker. You will nd that simulation using a smaller screen size will also be helpful so you should learn to use these parameters.

You should also observe that iXY Coord is declared to only be seven bits even though the default screen size is 160 pixels wide, which would require eight bits to capture the full range. This is because for this part, the module is designed for testing on the DE1-SoC and there are not enough switches. Therefore, for part2.v you will only be able to access 128 columns, instead of the full 160 columns of pixels available, but when you output the oX coordinate, it must be eight bits to match the requirements of the VGA adapter. When loading your X coordinate register from the 7-bit iXY Coord input, the register should be eight bits and you must set the most signi cant bit to 0 to ll out the required eight bits.

3.6 What to Do

Perform the following steps to implement your design. It is strongly recommended that you go through the incremental steps outlined in the previous section to arrive at the nal design, i.e., you will use the steps here to do several simpler designs leading up to your nal design.

Design (draw the schematic and write Verilog) and simulate a datapath that imple-ments the required functionality.

Design (draw a state diagram and write Verilog) and simulate a Moore FSM that controls the implemented datapath.

Build your top-level design and simulate the full system. If you feel you have met all the requirements, then you can submit to the Automarker.

You can now proceed to test your design on the real hardware.

Create a new Quartus project for your circuit. You will need a top-level module to make connections from the instantiation of your part2 module to the switches and


pushbuttons of the DE1-SoC board. The connections used are shown in Table 2.

part2 Port Name

Direction

DE1-SoC Pin Name

iClock

Input

Clock

50

iResetn

Input

KEY[0]

iPlotBox

Input

KEY[1]

iBlack

Input

KEY[2]

iLoadx

Input

KEY[3]

iXY

Coord[6:0]

Input

SW[6:0]

iColour[2:0]

Input

SW[9:7]

Table 2: Module part2 mapping to DE1-SoC pin names

On the DE1-SoC board you must also add the VGA adapter so that you can drive the VGA output on the board. Since the VGA adapter connects to an external circuit, it is not so easy to simulate your entire top-level design. This is because the VGA adapter connects to external circuitry and you cannot simulate the external circuitry. In the real-world, this can often happen, and you have to think hard about how to do a simulation. Sometimes you can acquire a simulation model, such as for an SDRAM chip. Other times, you may need to write a model for the external circuitry that can properly respond to your circuit in the FPGA. Here, we will try to get away with checking that the INPUTs to the VGA adapter look correct and assume that the VGA adapter works. Since we’ve used the VGA adapter for many years, it is a safe bet that you can do this, so do this simulation without the VGA adapter.

Simulate your new circuit with the DE1-SoC connections with ModelSim to ensure that you have done the connections correctly.

Now you can build your top-level design with the VGA adapter. You will nd some skeleton code that incorporates the VGA adapter in the le called fill.v in the lab7-part2.zip le. Use fill.v as the template for your top-level that includes the VGA adapter.

If you examine the code carefully, you will note that it refers to a le called black.mif. This initializes the frame bu er when the FPGA is rst con gured. In this case, it creates an all-black image.

Compile the project to generate a bitstream to make sure your code can at least be synthesized.

Download the compiled circuit into the FPGA chip. Test the functionality of the circuit by drawing boxes at di erent coordinates with di erent colours.

A Note About Simulating with Quartus Libraries and the VGA Adapter

If you are adding IP blocks (functional blocks of Verilog) generated from Quartus, such as RAMs, you will need to include the simulation libraries for those IP blocks. The VGA adapter, and other modules you might use for projects using the DE1-SoC board, like a keyboard controller, may also include Quartus IP blocks. You will need to include some Altera simulation libraries (ex., lpm ver, altera mf ver) to run your simulations.

The following shows how to add a library in your Modelsim scripts:

vsim -L altera_mf_ver <module-name>

If you need to add several libraries, then you will need additional -L library name ags.

Submission

Please submit part1.v and part2.v. For module names and port names, please follow the provided templates.
