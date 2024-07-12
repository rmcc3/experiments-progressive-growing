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

> ⚠️ When setting an image size, the "Width" and "Height" settings should be set higher than the final resolution you want to generate. For example, if you want a 1024x1024 image, set the "Width" and "Height" to 2048, and set max scale to 0.5 (or 1.0 if you want to generate at full resolution). This will generate a high quality 1024x1024 image.

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

## Examples

- **Fast Generation:**  Set "Min Scale" to 0.5, "Steps" to 2, and disable "Enable Refinement" for a quicker generation process. However, expect lower quality results.
- **High Quality:**  Set "Min Scale" to 0.25, "Steps" to 4, and enable "Enable Refinement" for higher quality results, but expect longer generation times.

## Known Issues
- Enabling Highres. fix will output a blurry or broken image. This is because the highres. fix is not compatible at this moment. It is recommended to disable this option when using progressive growing.
- After enabling Highres. fix with progressive growing, and then disabling highres. fix, future images generated with progressive growing will be blurry or broken. To fix this, restart the Web UI.

## Credits

These changes are based on an idea to improve the txt2img process. It is highly inspired by the Progressive Growing GAN technique and aims to provide similar benefits in the context of txt2img generation.