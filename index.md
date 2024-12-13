---
layout: home
title: FPGA VGA Driver Project
tags: fpga vga verilog
categories: demo
---

My name is Tremain and this is my FPGA VGA Driver Project. The aim of this project was to use to the VGA connector of the Basys 3 board to create a custom image on a monitor. Through this process, I developed my understanding of the vivado tool and my understanding of the verilog code.  

## **Template VGA Design**
### *Project Set-Up*
I began the project by creating a new Vivado project and defined the target FPGA board. We were given a constraints file that had clock configurations and pin assignments for the reset switch on the board. The design flow began by writing the verilog code, synthesizing it to check for any syntax or logic errors, running implementaion, and generating a bitstream to programme the FPGA board. Below is a screenshot of the project summary window.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ProjectSummary.png">

### *Template Code*
Outline the structure and design of the Verilog code templates you were given. What do they do? Include reference to how a VGA interface works. Guideline: 2/3 short paragraphs, consider including screenshot(s).

A VGA interface works by generating analog signals for red, green and blue colour channels. Each channel is 4 bits so the overall colour system will be 12 bit. Horizontal and vertical synchronization signals are generated to drive a VGA-compatible display. We use a monitor in the lab as our display.

We were given two Verilog code templates by Michelle, VGAColourCycle and VGAColourStripes.

### *VGAColourStripes*

VGAColourStripes created a static image that broke the 640 x 480 display area into 8 columns that displayed different colours. At the beginning of the code, we set the parameters and the inputs and outputs needed along with the registers used to store the RGB colours. The parameters set the counter width for timings and we define the counter range and the value that the counter should reset at.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ParametersStripesOrig.png">

Within the template, we used 'if' and 'else if' statements to check that the column width was within certain parameters before we set the colour of each column.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ColourStripesOrig.png">

We assign our output colours to internal registers. This is to separate internal logic from external sources. We use 'assign' to make sure changes in the registers are reflected on the output ports. This structure allows us to manipulate internal registers without affecting the outputs.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/AssignReg.png">

### *VGAColourCycle*

VGAColourCycle used a state machine to increment through eight different colour states every time a counter reaches the 'COUNT_TO' parameter. The outputs are the same as VGAColourStripes but we only have inputs for the clock and reset as we are not splitting up the screen so we do not need to use 'if' statements to check for row or col conditions. We define our state names and registers for current state and next state, colour and count. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/CycleParam.png">

The state machine determines the colour and logic. We begin by setting our default colour and setting the next state to the current state. We set the colour within the first state and then check if count has reached 'COUNT_TO'. If the condition is correct, we set the next state to the name of the next state we want to enter. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/CycleLogic.png">

We have two blocks for implementing the state machine and for the counter. The state register block allows us to reset to the default condition if the reset switch is high. If the reset is low, the state will update on the rising edge of the clock. The counter block resets the count to 'COUNT_FROM' if the reset switch is high. If the reset switch is low, it will reset the counter to 0 if it reaches 'COUNT_TO'. Otherwise, it will increment the counter by 1 unless it has reached 'COUNT_TO'.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/CycleRegState&Counter.png">

## **My VGA Design Edit**
For my VGA Design, I wanted to break the display up into smaller pixels so I could create a custom image. I also wanted to make use of the counter from the VGAColourCycle template to iterate through different images. My aim was to customise the templates that Michelle gave me so I could better understand the verilog code and how it worked. This allowed me to avoid using chatGPT also. I found that others were having issues with the code that chatGPT was recommending them to use so I decided not to waste time with that. 

Once I had split up my display into smaller pixels, I used an online pixel art creator to create my image. This also let me map out the rows and columns I would use depending on where they were in the pixel art image.
https://www.pixilart.com/draw

Below is the original pixel image I created. I elaborated on this to create multiple images that displayed in different states.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ProjectSnip.png">

### **Code Adaptation**
I used a combination of the 2 templates I received from Michelle for my project. I used the state machine from the VGAColourCycle template to implement the counter and switching between states. I used the column and row separation from the VGAColourStripes template to split my display into boxes. I divided my 640 x 480 display 10. This gave me 64 horizontal boxes and 48 vertical boxes. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/MainCode.png">

I used 'if' statements to check if the rows and columns were correct before changing the colour of the individual boxes to create my image. At the beginning of each state, I set the entire display black. This avoids any issues with colours being overwritten. The counter and register state is identical to the VGAColourCycle template. 

### **Simulation**
I used the testbench to simulate the design. This is helpful for detecting any timing or synchronization issues. It also allows us to visualise the signal beahviour and detect any logic errors before deploying to hardware. In the testbench, we reduce the size of our parameters to allow for quicker testing. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/TestBenchParam.png">

Below, we can see the signal generation from our testbench. We can see the clock frequency and we can see that is synchronous as the other signals change on the rising edge of the clock. The row and col signals are numbered in decimal so it is easier to understand how they work. We can see that the programme begins at row[0] and col[0]. It then implements through each column in row[0]. Once that has been completed, it moves on to row[1], col[0] and implements through each column in row[1]. It does this for each row.  

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/Simulation.png">

### **Synthesis**
Once we synthesize, an elaborate design is created. This shows us a top down view of our different modules and multiplexers (Muxes). 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ElaborateDesign.png">

If we select the ColourStripes module, we can see different comparators, AND gates, registers and multiplexers. The comparators act as logic blocks for comparing inputs and generating a true (1) or false (0) output. The 'RTL_GE' comparator checks if I0 is greater than or equal to V and outputs a 1 if this is true. This is the defined range for checking the row and column 'if' statements. The 'RTL_LT' checks if I0 is less than V. The 'RTL_AND' is a normal AND gate anf outputs a 1 if all inputs are 1.

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ProjectSnipComparators&AND.png">

The muxes are used to select an input signal based on the select signal. This allows for different ROM signals based on the select signal. The 'RTL_ROM' is Read-Only Memory and it stores look-up tables (LUTs). The input determines the stored values address. The output data is sent to the mux. The 'RTL_REG_ASYNC' is an asynchronous register that stores data that can be reset asynchronously using the clr signal. It acts as a final storage space for 'blue_reg' or 'green_reg' or 'red_reg'. This ensures the output is synchronized with the clock. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ProjectSnipReg&Mux.png">

### **Implementation**
In the implementatin design, we can see the hardware placement on the board that is being used for our project. The blue boxes in the bottom left of the board is the hardware that is being used on the board. If we zoom in on this, we can see different routes for Hsync, Vsync, Blue_reg etc. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ImplentationDesignBoardPlacement.png">

The below image shows the 'hsync_reg' flip-flop within a slice. It represents the physical location of the flip-flop after synthesis, placement and routing. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ImplentationDesignHsync&Flip&Latch.png">

In verilog, we can see a breakdown of the resources used. We can see the overall amount of look-up tables used, slices used and registers used. It gives us a breakdown of what modules used these resources. 

<img src="https://raw.githubusercontent.com/Tremainm/SOC_Project/main/docs/assets/images/ImplentationDesignSlices-LUT-etc.png">

### **Demonstration**
If you get your own design working on the Basys3 board, take a picture! Guideline: 1-2 sentences.


Hyperlinks look like this: [GitHub Help](https://help.github.com/).

A bullet list can be rendered as follows:
- vectors
- algorithms
- iterators

Images can be added by uploading them to the repository in a /docs/assets/images folder, and then rendering using HTML via githubusercontent.com as shown in the example below.

<img src="https://raw.githubusercontent.com/melgineer/fpga-vga-verilog/main/docs/assets/images/VGAPrjSrcs.png">
