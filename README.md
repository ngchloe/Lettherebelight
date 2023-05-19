# Fine-tuning Stable Diffusion using Textual Inversion
## Training my own Textual Inversion model for Stable Diffusion and ControlNet for Architectural Renderings

### Date: May 2023

Disclaimer: I'm not trained in ML (word pun totally intended) so the following document is simply a layperson's understanding and sharing of findings.

### Programs/tools concerned:

•	Stable Diffusion 1.5 – Automatic1111
•	ControlNet
•	Textual Inversion

Stable Diffusion and ControlNet are powerful tools with good potential to accelerate the workflow from a basic preliminary Sketchup model view to a visual rendering (albeit draft) of the model in the quickest time possible, with various iterations possible. While this already allows for visual renderings to be generated, the objective of this experiment is to further finetune and enhance the visual rendering through the training and use of a Textual Inversion model.

Thus, this document outlines my attempts at training my own Textual Inversion model in Stable Diffusion (SD) 1.5. 

### Introduction

ControlNet (https://github.com/lllyasviel/ControlNet)

ControlNet is a means of controlling diffusion models by adding extra conditions. It allows for the copying of compositions from a reference image. For example:

