# app-N4BiasFieldCorrection
**N4 Bias Field Correction (ANTs)**

A portable, containerized wrapper around **ANTs’ `N4BiasFieldCorrection`** (Tustison, N. J. et al, 2010)(Avants, B. B. et al, 2011) to correct intensity inhomogeneity in **T1w or T2w** anatomical MRI. Optional brain masking is supported to improve bias estimation and reduce runtime.

---

## Author

**Gabriele Amorosino**  
(email: [gabriele.amorosino@utexas.edu](mailto:gabriele.amorosino@utexas.edu))

---

## Description

This app runs `N4BiasFieldCorrection` (ANTs) on a single anatomical input and produces a bias-corrected image.  
- **Inputs:** one of `t1` *or* `t2` (NIfTI `.nii.gz`); optional `mask` (binary `.nii.gz`)  
- **Dimensionality:** fixed to 3D (`-d 3`)  
- **Multi-threading:** honors ITK/OMP thread env vars set by the script

Under the hood, the app pulls the ANTs container and executes:

```

N4BiasFieldCorrection -d 3 -i <input.nii.gz> [-x <mask.nii.gz>] -o <output.nii.gz>

````

Execution is via **Singularity**, with PBS headers included for HPC submission.

---

## Requirements

- [Singularity](https://sylabs.io/guides/latest/user-guide/)
- Sufficient RAM for your volumes (typ. ≥ 4–8 GB for standard T1w/T2w)
- (Optional) A brain mask to improve robustness/speed on noisy data

---

## Inputs

Provide **exactly one** anatomical input in `config.json`, plus an optional mask:

- `t1` — path to T1w image (`.nii.gz`) **or**
- `t2` — path to T2w image (`.nii.gz`)
- `mask` *(optional)* — path to a binary brain mask (`.nii.gz`), passed as `-x`

**Config schema**

```json
{
  "t1": null,
  "t2": null,
  "mask": null
}
````

> Supply **either** `t1` **or** `t2` (not both). Leave the unused key as `null`.

---

## Usage

### Running on Brainlife.io

1. Open the app on Brainlife and click **Execute**.
2. Provide one anatomical input (`t1` *or* `t2`) as `.nii.gz`.
3. (Optional) Provide a `mask` (`.nii.gz`) to be passed as `-x`.
4. Submit. Outputs will include a bias-corrected image.

#### Via CLI (example)

```bash
bl login
bl app run --id <app_id> --project <project_id> \
  --input t1:<t1_dataset_id> \
  --config config.json
```

Replace `t1:` with `t2:` if you’re providing a T2w image.

---

### Running Locally / On HPC

1. **Prepare `config.json`:**

   **Example (T1w, with mask):**

   ```json
   { "t1": "sub-01_T1w.nii.gz", "t2": null, "mask": "sub-01_mask.nii.gz" }
   ```

   **Example (T2w, no mask):**

   ```json
   { "t1": null, "t2": "sub-01_T2w.nii.gz", "mask": null }
   ```

2. **Run the script:**

   ```bash
   bash ./main
   ```

   On PBS/Torque clusters, you can also submit:

   ```bash
   qsub main
   ```

---

## Outputs

* `bias_corrected/t1.nii.gz` **or** `bias_corrected/t2.nii.gz` — N4 bias–corrected anatomical volume

---

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

## Citation

If you use this app for your research, please cite:

* Avants, B. B., Tustison, N. J., Song, G., et al. (2011). **A reproducible evaluation of ANTs similarity metric performance in brain image registration.** *NeuroImage*, 54(3), 2033–2044.
* Tustison, N. J., Avants, B. B., Cook, P. A., et al. (2010). **N4ITK: Improved N3 bias correction.** *IEEE Transactions on Medical Imaging*, 29(6), 1310–1320.
* Hayashi, S., Caron, B.A., Heinsfeld, A.S. et al. **brainlife.io: a decentralized and open-source cloud platform to support neuroscience research.** Nat Methods 21, 809–813 (2024).

---

## License

See [LICENSE](LICENSE).

```
