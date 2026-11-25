<h1 align="center">N4 Bias Field Correction (T2w)</h1>

<p align="center">
  Corrects intensity inhomogeneity (bias field) in a T2-weighted image with ANTs N4BiasFieldCorrection, optionally restricted to a brain mask.
</p>

<p align="center">
  <a href="https://github.com/brainlife/abcd-spec"><img src="https://img.shields.io/badge/ABCD_Spec-v1.1-green.svg" alt="ABCD Spec v1.1"></a>
  <a href="https://doi.org/10.25663/brainlife.app.876"><img src="https://img.shields.io/badge/Brainlife-brainlife.app.876-blue.svg" alt="Run on brainlife.io"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/License-see_LICENSE-lightgrey.svg" alt="License"></a>
</p>

## At a glance

<!-- brainlife-app:begin (generated from the App manifest; edit the manifest, not this block) -->

> **Corrects intensity inhomogeneity (bias field) in a T2-weighted image with ANTs N4BiasFieldCorrection, optionally restricted to a brain mask.**

Runs N4BiasFieldCorrection -d 3 on the T2w image, passing the mask with -x, and writes the bias-corrected image. Use it before segmentation or registration when the image shows smooth intensity non-uniformity. The mask must be aligned with the T2w image.

| | |
|---|---|
| **Inputs** | `neuro/anat/t2w` · `neuro/mask` (optional) |
| **Outputs** | `neuro/anat/t2w` |
| **Container** | `brainlife/ants:2.2.0-1bc` |
| **Resources** | 1 CPUs · 8 GB memory · 2 h walltime |
| **Run it** | [doi.org/10.25663/brainlife.app.876](https://doi.org/10.25663/brainlife.app.876) |

#### Inputs

| Input | Datatype | Required | Description |
|---|---|---|---|
| `t2` | `neuro/anat/t2w` | yes | T2-weighted anatomical image (NIfTI). |
| `mask` | `neuro/mask` | no | Binary brain mask passed to N4 with -x. Must be aligned with the T2w image. |

#### Outputs

| Output | Datatype | Description |
|---|---|---|
| `bias_corrected` | `neuro/anat/t2w` | N4 bias-corrected T2-weighted image (t2.nii.gz). |

<!-- brainlife-app:end -->

## Running the App

### On brainlife.io

Open the App at [doi.org/10.25663/brainlife.app.876](https://doi.org/10.25663/brainlife.app.876), choose `neuro/anat/t2w`, `neuro/mask` from your project and press **Execute**. The results are saved to your project as `neuro/anat/t2w`.

### Locally

You need [Singularity/Apptainer](https://apptainer.org/) (the App runs in `brainlife/ants:2.2.0-1bc`), [`jq`](https://jqlang.github.io/jq/).

1. Clone this repository.
2. Create `config.json` with the paths to your inputs and the parameters:

   ```json
   {
       "t2": "/path/to/t2/t2",
       "mask": "/path/to/mask/mask"
   }
   ```

3. Run `./main`.

## Container & Implementation Notes

* **Container:** `docker://brainlife/ants:2.2.0-1bc`
* **Command:** `N4BiasFieldCorrection -d 3 -i <input> [-x <mask>] -o <output>`
* **Threading:** The script sets

  * `ITK_GLOBAL_DEFAULT_NUMBER_OF_THREADS`
  * `OMP_NUM_THREADS`
    via `setITKthreads $nthreads`. Make sure `nthreads` is exported in your environment (e.g., `export nthreads=8`) or adapt the script to compute it from available CPUs.

* **Input exclusivity:** If both `t1` and `t2` are set, the current script will pick the **last** branch executed (i.e., `t2`). 
* **Mask optionality:** Mask is optional; when provided, it’s passed as `-x`. Ensure the mask aligns with the input image.

---

## Citing

If you use this App, please cite it, the software it builds on and brainlife.io:

- N4 Bias Field Correction (T2w). brainlife.io App. [doi:10.25663/brainlife.app.876](https://doi.org/10.25663/brainlife.app.876)
- Avants BB, Tustison NJ, Song G, et al. A reproducible evaluation of ANTs similarity metric performance in brain image registration. NeuroImage 54(3):2033-2044 (2011).
- Tustison NJ, Avants BB, Cook PA, et al. N4ITK: Improved N3 bias correction. IEEE Trans Med Imaging 29(6):1310-1320 (2010).
- Avants, B. B., Tustison, N. J., Song, G., et al. (2011). **A reproducible evaluation of ANTs similarity metric performance in brain image registration.** *NeuroImage*, 54(3), 2033–2044.
- Tustison, N. J., Avants, B. B., Cook, P. A., et al. (2010). **N4ITK: Improved N3 bias correction.** *IEEE Transactions on Medical Imaging*, 29(6), 1310–1320.
- Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. **brainlife.io: a decentralized and open-source cloud platform to support neuroscience research.** Nat Methods 21, 809–813 (2024). ---
- Avants et al. A reproducible evaluation of ANTs similarity metric performance in brain image registration. *NeuroImage* 54:2033-2044 (2011). [doi:10.1016/j.neuroimage.2010.09.025](https://doi.org/10.1016/j.neuroimage.2010.09.025)
- Hayashi S, Caron BA, Heinsfeld AS, et al. brainlife.io: a decentralized and open-source cloud platform to support neuroscience research. *Nature Methods* 21:809–813 (2024). [doi:10.1038/s41592-024-02237-2](https://doi.org/10.1038/s41592-024-02237-2)
- Avesani P, McPherson B, Hayashi S, et al. The open diffusion data derivatives, brain data upcycling via integrated publishing of derivatives and reproducible open cloud services. *Scientific Data* 6:69 (2019). [doi:10.1038/s41597-019-0073-y](https://doi.org/10.1038/s41597-019-0073-y)

## Authors

**Gabriele Amorosino**  
(email:

---

## License

See [LICENSE](LICENSE).
