# Fine-tuning Stable Diffusion using Textual Inversion
## Training my own Textual Inversion model for Stable Diffusion and ControlNet for Architectural Renderings

Date: May 2023

Disclaimer: I'm not trained in ML (word pun totally intended) so the following document is simply a layperson's understanding, and used as an avenue to share my findings.

### Programs/tools concerned:

•	Stable Diffusion 1.5 – Automatic1111

•	ControlNet

•	Textual Inversion (also called embedding)

Stable Diffusion and ControlNet are powerful tools with good potential to accelerate the workflow from a basic preliminary Sketchup model view to a visual rendering (albeit draft) of the model in the quickest time possible, with various iterations possible. While this already allows for visual renderings to be generated, the objective of this experiment is to further finetune and enhance the visual rendering through the training and use of a Textual Inversion model.

Thus, this document outlines my attempts at training my own Textual Inversion model in Stable Diffusion (SD) 1.5. 

### A Brief Introduction

•	ControlNet (https://github.com/lllyasviel/ControlNet)

ControlNet is a means of controlling diffusion models by adding extra conditions. It allows for the copying of compositions from a reference image. For example:

•	Textual Inversion (https://arxiv.org/abs/2208.01618)

Textual Inversion is a way to build upon existing large models with a user-provided concept. With a few sample images of a user-provided concept, be it an object or style, this can be represented through new ‘words’ of an existing model. These ‘words’ can then be used to guide outputs in a more intuitive and personalised manner.

### Pre-training Preparation

I made a quick villa in Sketchup and exported 2 linework views at 720px by 512px each.

Sample views as inputs to SD 1.5 and ControlNet

![A720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/A720x512.jpg)

Elevation view taken from Sketchup (own model)

![B720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/B720x512.jpg)

Perspective view taken from Sketchup (own model)

### Training of Textual Inversion (TI)

| Setting | Option |
| :----- | :------ |
| Stable Diffusion checkpoint  | v1-5-pruned-emaonly.ckpt |
| Embedding name | lettherebelight |
| Number of vectors per token  | 10  |

(4) images with the most common features were selected from an initial total of (30) images found online. It is important that the images share similar features in order for the Textual Inversion to be effective. If the images were totally disparate, the model may not train well because the images are too different. Also, the images had to be of high quality. As the saying goes, ‘garbage in, garbage out’.

The (4) images were then pre-processed using BLIP for captions, and also cropped to 512px by 512px.

| Setting | Option |
| :----- | :------ |
| Embedding Learning rate  | 0.005 |
| Max steps | 300 |
| Embedding saved after every N steps  | 30  |
| Latent sampling method | Deterministic |
