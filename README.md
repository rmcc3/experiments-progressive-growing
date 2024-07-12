# Progressive Growing for Stable Diffusion Web UI

These changes introduce a highly experimental progressive growing technique to the txt2img process in [AUTOMATIC1111's Stable Diffusion Web UI](https://github.com/AUTOMATIC1111/stable-diffusion-webui). It allows you to generate images by starting at a lower resolution and gradually increasing it, potentially improving image quality and speeding up the initial generation steps.

## Compatibility

- This script is compatible with both Stable Diffusion 1.5 and SDXL models.
- While it does work with SDXL, it is better suited and optimized for SD 1.5 models.

## Installation

1. **Important:** The scripts need to be installed in the **modules** directory of your Stable Diffusion Web UI, **not the models directory**.

2. Download the `txt2img.py`, `ui.py`, and `processing.py` files from this repository.

3. Place the downloaded files into the following directories within your Stable Diffusion Web UI installation:
   - `txt2img.py` →  `stable-diffusion-webui/modules/`
   - `ui.py` →  `stable-diffusion-webui/modules/`
   - `processing.py` →  `stable-diffusion-webui/modules/`

4. Restart your Stable Diffusion Web UI.

## Usage

1. After installing and restarting the Web UI, go to the "txt2img" tab.

2. You'll find a new section labeled "Progressive Growing".

3. **Enable Progressive Growing:** Check the "Enable" checkbox to activate progressive growing.

4. **Settings:**
   - **Min Scale:** The initial resolution as a fraction of the final resolution (e.g., 0.25 for starting at 25% of the final size).
   - **Max Scale:** The final resolution as a fraction of the final resolution (should be 1.0).
   - **Steps:** The number of resolution steps in the progressive growing process.
   - **Enable Refinement:** This uses img2img processing at each step to refine the image. This can improve quality but will increase generation time. Disabling this option will generate images faster but may result in lower quality. **It is recommended to keep this enabled for best results.**

5. **Generate:** Enter your prompt, adjust other settings as desired, and click "Generate."

*Note: If automatic1111 updates the Web UI, you may need to reinstall the script, which may or may not be compatible with the new version. Feel free to make a pull request to convert this to an extension.*

## How it Works

Progressive Growing in image generation works by gradually increasing the resolution of the generated image. Here's a step-by-step explanation:

1. **Initial Generation:** The script starts by generating an image at the lower "Min Scale" resolution specified in the settings. For example, if the final image size is set to 1024x1024 and the "Min Scale" is set to 0.25, the initial image will be generated at 256x256.

2. **Upscaling:** After generating the initial image, the script progressively upscales the latent space representation of the image in the defined number of "Steps." Each step increases the resolution by a factor calculated based on the "Min Scale," "Max Scale," and the number of "Steps."

3. **Refinement (Optional):** If the "Enable Refinement" option is checked, an img2img process is applied at each upscaling step to refine the image. This helps to preserve and enhance the details as the resolution increases. The refinement process uses the upscaled image from the previous step as the input and generates a new image at the current resolution.

4. **Final Output:** Once all the upscaling and refinement steps are completed, the script generates the final output image at the full resolution specified in the "Width" and "Height" settings, unless you chose to generate at a lower max scale.

By starting at a lower resolution and gradually increasing it, Progressive Growing allows the model to focus on the overall composition and structure of the image first, and then progressively add finer details as the resolution increases. This approach can potentially improve the quality of the generated images and speed up the initial generation steps.

## Tips

- Experiment with the settings to find the best balance between speed and quality for your use case.
- Progressive growing can be particularly beneficial for generating larger images or images with complex details.
- The "Enable Refinement" option provides better quality but increases generation time.

## Examples (Zero Post-Processing)
![sample_1](https://github.com/user-attachments/assets/582ec814-fd06-46cb-8edc-a0350a6e163d)
---
**Prompt:** A high-resolution portrait of a woman with sharp facial features, long dark hair, and piercing blue eyes, wearing a black leather jacket, photographed by Annie Leibovitz, 8k resolution

**Negative Prompt:** low quality, blurry, disfigured, deformed

**Settings:**
- Width: 2048
- Height: 2048
- Min Scale: 0.25
- Max Scale: 1.0
- Steps: 5
- Enable Refinement: Checked
- Sampling Steps: 100
- Sampling Method: DPM++ 2M Karras
- Generated on SD 1.5

In this example, the initial image is generated at a resolution of 512x512 (25% of the final 2048x2048 resolution) and then progressively grown to 2048x2048 over 5 steps, with refinement enabled at each step. The high sampling steps and the use of the DPM++ 2M Karras sampling method will contribute to generating a crisp and detailed portrait.

---
![sample_2](https://github.com/user-attachments/assets/82f12efb-c07a-4d99-870c-00c21b82f085)
---
**Prompt:** An ultra-realistic still life of a wooden table with a vase of colorful flowers, a bowl of fresh fruit, and a bottle of red wine, captured in stunning detail, 4k resolution

**Negative Prompt:** low quality, blurry, unclear, abstract

**Settings:**
- Width: 2048
- Height: 1024
- Min Scale: 0.5
- Max Scale: 1.0
- Steps: 4
- Enable Refinement: Checked
- Sampling Steps: 80
- Sampling Method: DPM++ SDE Karras
- Generated on SD 1.5

For this example, the initial image is generated at a resolution of 1024x512 (50% of the final 2048x1024 resolution) and then progressively grown to 2048x1024 over 4 steps, with refinement enabled at each step. The combination of the detailed prompt, high sampling steps, and the DPM++ SDE Karras sampling method will help generate a crystal-clear still life image.

---
![00019-2929855477](https://github.com/user-attachments/assets/eaeb7d4c-763d-42e7-9ee4-82df0e3ea466)
--

**Prompt:** A realistic high-resolution portrait of a 25yo man with sharp facial features and facial hair, in a park on a cloudy day, serious face, detailed face, skin pores, from a distance, outdoors, detailed background

**Negative Prompt:** low quality, blurry, disfigured, deformed, cgi, anime

**Settings:**
- Width: 2048
- Height: 2048
- Min Scale: 0.25
- Max Scale: 0.7
- Steps: 4
- Enable Refinement: Checked
- Sampling Steps: 100
- Sampling Method: DPM++ SDE Karras
- Generated on SDXL

For this example, the initial image is generated at a resolution of 512x512 (25% of the final 2048x2048 resolution) and then progressively grown to 1433x1433 (70% of the final resolution) over 5 steps, with refinement enabled at each step. The high sampling steps and the use of the DPM++ SDE sampling method will contribute to generating a realistic and detailed portrait.

## The Importance of High Sampling Steps

When using the Progressive Growing script, it's crucial to set a high number of sampling steps to ensure the best possible image quality. Sampling steps refer to the number of times the model iteratively refines the generated image during the creation process.

A higher number of sampling steps allows the model to make more precise adjustments to the image, resulting in finer details, smoother transitions, and overall better coherence. This is particularly important when generating high-resolution images, as the increased level of detail requires more precise refinements.

In the example prompts provided in this repository, the sampling steps are set to values ranging from 60 to 100, depending on the complexity of the image and the desired quality. These high values ensure that the generated images are of excellent quality and showcase the full potential of the Progressive Growing technique.

It's important to note that increasing the number of sampling steps also increases the generation time. Therefore, you should find a balance between image quality and generation speed that suits your needs. Experimenting with different values and comparing the results will help you determine the optimal number of sampling steps for your specific use case.

Keep in mind that using a high number of sampling steps is especially beneficial when generating images with high details, complex textures, or multiple objects. In simpler compositions, you may be able to achieve satisfactory results with fewer sampling steps, thereby reducing the generation time.

## Known Issues
- Enabling Highres. fix will output a blurry or broken image. This is because the highres. fix is not compatible at this moment. It is recommended to disable this option when using progressive growing.
- After enabling Highres. fix with progressive growing, and then disabling highres. fix, future images generated with progressive growing will be blurry or broken. To fix this, restart the Web UI.
- "Euler a" sampler is not compatible with progressive growing at the moment, "Euler" is, however, compatible.

## Credits

These changes are based on an idea to improve the txt2img process. It is highly inspired by the Progressive Growing GAN technique and aims to provide similar benefits in the context of txt2img generation.
