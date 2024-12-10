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

### **Simulation**
Explain the simulation process. Reference any important details, include a well-selected screenshot of the simulation. Guideline: 1/2 short paragraphs.
### **Synthesis**
Describe the synthesis and implementation processes. Consider including 1/2 useful screenshot(s). Guideline: 1/2 short paragraphs.
### **Demonstration**
Perhaps add a picture of your demo. Guideline: 1/2 sentences.

## **My VGA Design Edit**
Introduce your own design idea. Consider how complex/achievabble this might be or otherwise. Reference any research you do online (use hyperlinks).
### **Code Adaptation**
Briefly show how you changed the template code to display a different image. Demonstrate your understanding. Guideline: 1-2 short paragraphs.
### **Simulation**
Show how you simulated your own design. Are there any things to note? Demonstrate your understanding. Add a screenshot. Guideline: 1-2 short paragraphs.
### **Synthesis**
Describe the synthesis & implementation outputs for your design, are there any differences to that of the original design? Guideline 1-2 short paragraphs.
### **Demonstration**
If you get your own design working on the Basys3 board, take a picture! Guideline: 1-2 sentences.

## **More Markdown Basics**
This is a paragraph. Add an empty line to start a new paragraph.

Font can be emphasised as *Italic* or **Bold**.

Code can be highlighted by using `backticks`.

Hyperlinks look like this: [GitHub Help](https://help.github.com/).

A bullet list can be rendered as follows:
- vectors
- algorithms
- iterators

Images can be added by uploading them to the repository in a /docs/assets/images folder, and then rendering using HTML via githubusercontent.com as shown in the example below.

<img src="https://raw.githubusercontent.com/melgineer/fpga-vga-verilog/main/docs/assets/images/VGAPrjSrcs.png">
