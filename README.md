# Fine-tuning Stable Diffusion using Textual Inversion
## Training my own Textual Inversion model for Stable Diffusion and ControlNet for Architectural Renderings

| ![B720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/B720x512.jpg)| ![](https://github.com/ngchloe/Lettherebelight/blob/main/images/sample%202.png)| ![sample](https://github.com/ngchloe/Lettherebelight/blob/main/images/sample%20-%20LTBL.png) | 
| :------: | :------: | :------: |
| Input (view from Sketchup) | SD (no Textual Inversion) | Refined with my Textual Inversion |

Date: May 2023

*Disclaimer: I'm not trained in ML (word pun totally intended) so the following document is simply a layperson's understanding, and used as an avenue to share my findings.*

## 01. Programs/tools involved

•	Stable Diffusion 1.5 – Automatic1111

•	ControlNet

•	Textual Inversion (also known as embedding)

Stable Diffusion and ControlNet are powerful tools with good potential to accelerate the workflow from a basic preliminary Sketchup model view to a visual rendering (albeit draft) of the model in the quickest time possible, with various iterations possible. While this already allows for visual renderings to be generated, the objective of this experiment is to further finetune and enhance the visual rendering through the training and use of a Textual Inversion model.

Thus, this document outlines my attempts at training my own Textual Inversion model in Stable Diffusion (SD) 1.5. I trained the Textual Inversion on my RTX3060 (Laptop GPU) with 6GB of VRAM.

## 02. A Brief Introduction: ControlNet and Textual Inversion

•	ControlNet (https://github.com/lllyasviel/ControlNet)

ControlNet is a means of controlling diffusion models by adding extra conditions. It allows for the copying of compositions from a reference image. For example:

| Input image | Output using Stable Diffusion and ControlNet|
| :------: | :------: |
| ![B720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/B720x512.jpg)| ![sample](https://github.com/ngchloe/Lettherebelight/blob/main/images/sample.png) |
| Exported Sketchup view of model (own model) as the input image to SD 1.5 | SD 1.5 and ControlNet with a text prompt automatically renders the image, while still retaining the original composition and reference linework from the input image. |

•	Textual Inversion (https://arxiv.org/abs/2208.01618)

Textual Inversion is a way to build upon existing large models with a user-provided concept. With a few sample images of a user-provided concept, be it an object or style, this can be represented through new ‘words’ of an existing model. These ‘words’ can then be used to guide outputs in a more intuitive and personalised manner.

## 03. Pre-training Preparation

I made a quick villa in Sketchup and exported 2 black and white linework-only views at 720px by 512px each. Sample views as inputs to SD 1.5 and ControlNet:

| Elevation View | Perspective View |
| :-----: | :------: |
| ![A720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/A720x512.jpg)| ![B720x512](https://github.com/ngchloe/Lettherebelight/blob/main/images/B720x512.jpg) |

## 04. Training of Textual Inversion (TI)

### Create Embedding

| Settings | Configuration |
| :----- | :------ |
| Stable Diffusion checkpoint  | v1-5-pruned-emaonly.ckpt |
| Embedding name | lettherebelight |
| Number of vectors per token  | 10  |

(4) images with the most common features were selected from an initial total of (30) images found online. It is important that the images share similar features in order for the Textual Inversion to be effective. If the images are totally disparate, the model may not train well because the images are too different. Also, the images had to be of high quality. As the saying goes, ‘garbage in, garbage out’.

The (4) images were then pre-processed using BLIP for captions, and also cropped to 512px by 512px.

### Training Settings

| Settings | Configuration |
| :----- | :------ |
| Embedding Learning rate  | 0.005 |
| Max steps | 300 |
| Embedding saved after every N steps  | 30  |
| Latent sampling method | Deterministic |

## 05. Finding the best Textual Inversion checkpoint

Once the training was complete, the next step was to determine which of the embedding checkpoints was the most appropriate. For max steps of 300 and with an embedding saved every 30 steps, that means that there are 10 TI checkpoints to choose from.

My prior studies have shown that the realisticVision2.0 checkpoint (https://civitai.com/models/4201/realistic-vision-v20) was the most suitable for architectural visual renderings. The other checkpoints (the base SD checkpoint, deliberate_v2, dvArchMultiPrompt) did not feel as suitable for this testing. Hence, the next task was to test the 10 TI checkpoints against the realisticVision2.0 checkpoint to identify the most suitable. 

### txt2img

| Settings | Configuration |
| :----- | :------ |
| Prompt  | Lettherebelight, (((architectural rendering)), (((dramatic lighting))), landscape photo of a modern villa surrounded by tropical trees, night, (((modern architecture villa))), modern, (((tropical trees))), (((lush vegetation))), (((villa on the beach))), white sandy beach, ((tropical)), (((realistic))), 85mm, f1.8, portrait, photo realistic, hyperrealistic, super detailed |
| Negative Prompt | signature, soft, blurry, drawing, sketch, poor quality, ugly, text, type, word, logo, pixelated, low resolution, saturated, high contrast, oversharpened |
| Sampling method  | Euler a  |
| Sampling steps | 20 |
| Width | 720px (to correspond to input image size) |
| Height | 512px (to correspond to input image size) |

### ControlNet

| Settings | Configuration |
| :----- | :------ |
| Enable | Yes |
| Preprocessor | Canny |
| Model | Control_v11p_sd15_canny |

### Script

| Settings | Configuration |
| :----- | :------ |
| Script | X/Y/Z plot |
| X values | Lettherebelight, Lettherebelight-30, Lettherebelight-60, Lettherebelight-90, Lettherebelight-120, Lettherebelight-150, Lettherebelight-180, Lettherebelight-210, Lettherebelight-240, Lettherebelight-270, Lettherebelight-300 |

The use of this script is to automate and swap out ‘Lettherebelight’ in the original prompt with the other TI checkpoints.

The most suitable turned out to be Lettherebelight-150. That will be the checkpoint used. I archived the 9 rejected checkpoints in a separate folder, and renamed Lettherebelight-150 to Lettherebelight.

## 06. Observations of selected Textual Inversion checkpoint

With a checkpoint selected, it was now time to test it.

### txt2img

| Settings | Configuration |
| :----- | :------ |
| Prompt  | Lettherebelight, (((architectural rendering)), (((dramatic lighting))), landscape photo of a modern villa surrounded by tropical trees, night, (((modern architecture villa))), modern, (((tropical trees))), (((lush vegetation))), (((villa on the beach))), white sandy beach, ((tropical)), (((realistic))), 85mm, f1.8, portrait, photo realistic, hyperrealistic, super detailed |
| Negative Prompt | signature, soft, blurry, drawing, sketch, poor quality, ugly, text, type, word, logo, pixelated, low resolution, saturated, high contrast, oversharpened |
| Sampling method  | Euler a  |
| Sampling steps | 20 |
| Width | 720px (to correspond to input image size) |
| Height | 512px (to correspond to input image size) |
| Batch count | 1 |
| Batch size | 4 |

### ControlNet

| Settings | Configuration |
| :----- | :------ |
| Enable | Yes |
| Preprocessor | Canny |
| Model | Control_v11p_sd15_canny |

The following images are direct exports from SD 1.5 and have not been retouched in any way. 

### Sunset View (elevation)

| realisticVision v2.0 (SD 1.5) | realisticVision v2.0 (SD 1.5) with ‘Let there be Light’ |
| :-----: | :------: |
| ![no TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/sunset%20view%20-%20elevation.png) | ![with TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/sunset%20view%20-%20elevation%20-%20LTBL.png) |

### Sunset View (perspective)

| realisticVision v2.0 (SD 1.5) | realisticVision v2.0 (SD 1.5) with ‘Let there be Light’ |
| :-----: | :------: |
| ![no TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/sunset%20view%20-%20perspective.png) | ![with TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/sunset%20view%20-%20perspective%20-%20LTBL.png) |

I also tested a noon/daytime view with the following. Settings are the same with slight differences in the text prompt for time of day.

### txt2img

| Settings | Configuration |
| :----- | :------ |
| Prompt  | Lettherebelight, ((architectural rendering)), landscape photo of a modern villa surrounded by tropical trees, noon, day time, (((modern architecture villa))), modern, (((tropical trees))), (((lush vegetation))), (((villa on the beach))), white sandy beach, ((tropical)), (((realistic))), 85mm, f1.8, portrait, photo realistic, hyperrealistic, super detailed |
| Negative Prompt | signature, soft, blurry, drawing, sketch, poor quality, ugly, text, type, word, logo, pixelated, low resolution, saturated, high contrast, oversharpened |
| Sampling method  | Euler a  |
| Sampling steps | 20 |
| Width | 720px (to correspond to input image size) |
| Height | 512px (to correspond to input image size) |
| Batch count | 1 |
| Batch size | 4 |

### ControlNet

| ControlNet Settings | Configuration |
| :----- | :------ |
| Enable | Yes |
| Preprocessor | Canny |
| Model | Control_v11p_sd15_canny |

### Daytime view (elevation)

| realisticVision v2.0 (SD 1.5) | realisticVision v2.0 (SD 1.5) with ‘Let there be Light’ |
| :-----: | :------: |
| ![no TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/daytime%20-%20elevation.png) | ![with TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/daytime%20-%20elevation%20-%20LTBL.png) |

The use of Lettherebelight TI model provides dramatic and warm building lighting for sunset/night view and daytime views.

However, the daytime views are skewed towards dusk and hence this TI model may not be suitable for a bright noon time view.

## 07. Further testing

To further test the usefulness of this TI model, I also wondered: could the same effect be achieved via text prompt without Lettherebelight? (i.e. is this TI model even necessary?)

### Sunset view (elevation)

| realisticVision v2.0 (SD 1.5) | 
| :-----: | 
| ![no TI](https://github.com/ngchloe/Lettherebelight/blob/main/images/sunset%20-%20elevation%20-%20prompt.png) |

The above collage of images uses the same text prompt, but with additional prompting to achieve a warm, orange-hued glow effect. The prompt has the unintentional effect of toning the building material to be more orange in colour and the images look more flat and less dramatic. 

**Hence, since text alone is insufficient in achieving the intended effect, Lettherebelight is a useful Textual Inversion model in further refining a given text prompt.**

## 08. Conclusion

### The ‘Lettherebelight’ Textual Inversion model is most suitable if you are aiming for a visual rendering that is: more dramatic, has a warmer hue (where the building appears to glow), and in the hours of sunset and night time. 
