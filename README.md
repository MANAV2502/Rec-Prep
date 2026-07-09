## Rec-Prep: Receptor Preparation & Grid Map Generation

🧬 A Colab notebook (`Rec_Prep.ipynb`) automating the full rigid (+ optional flexible) receptor preparation pipeline for AutoDock-GPU, from a raw PDB file to a complete AutoGrid4 map set.

## Pipeline Steps

Environment setup (condacolab, ADFRsuite, OpenBabel, PDB2PQR, py3Dmol) → target-pH configuration → PDB upload with chain/HETATM detection → optional non-metallic cofactor retention (dual-tool protonation: PDB2PQR + PROPKA3 for protein, OpenBabel for cofactors) → receptor-to-PDBQT conversion via `prepare_receptor4.py` with charge-conservation QC → optional flexible-residue definition and rigid/flex split via `prepare_flexreceptor4.py` → interactive 3D visualization → AutoSite pocket detection → six grid-box definition modes → GPF writing and `autogrid4` map generation → packaged download.

**⚠️ Required step:** Always run the "Select Non-Metallic Cofactors to Retain" cell before Step 3, even with the field left blank, it performs the mandatory protein protonation that will be used further. Skipping it causes Step 3 to silently fall back to the raw, unprotonated PDB and crash with `KeyError: None`.

## ⚠️ Pre-Upload Cleanup (Required)

The downloaded archive contains several **intermediate/secondary files** generated as scratch output during cofactor and protein protonation. These must be **deleted before zipping or uploading the receptor package to the Dock-GPU docking pipeline** — only the final canonical PDBQT(s) are valid AutoDock-GPU input.

**Delete:**
- `_cof_<name>.pdb`, `_cof_<name>_prot.pdb`, `_cof_<name>.pdbqt` — per-cofactor scratch fragments (raw, protonated, and typed intermediates)
- `receptor_raw.pdb` — original uploaded structure, pre-processing
- `receptor_protein_only.pdb`, `receptor_protein_only.pqr`, `receptor_protein_only.log` — protein-only PDB2PQR/PROPKA3 intermediates
- `*.propka` — PROPKA pKa diagnostic output, if present

**Keep:**
- `<receptor_name>.pdbqt` — canonical rigid receptor (merged protein + retained cofactors)
- `<receptor_name>_flex.pdbqt` — flexible-residue part, only if Step 3.5 was used
- `<receptor_name>.gpf`, `<receptor_name>.glg` — grid parameter file and AutoGrid4 log
- `<receptor_name>.maps.fld`, `<receptor_name>.maps.xyz`, all `*.map` files — AutoGrid4 affinity maps
- `AD4.1_bound.dat` — AutoGrid parameter file

Only `<receptor_name>.pdbqt` and, if applicable, `<receptor_name>_flex.pdbqt` are the receptor PDBQTs Dock-GPU expects at docking time — every other `.pdbqt` in the archive is a discarded scratch artifact of cofactor preparation.

## Prerequisites

Colab runtime (GPU not required for this notebook); a receptor `.pdb` file; internet access for the Step 1 dependency install (~2–4 min).

## Citations

- **AutoDock4 / AutoGrid4 (scoring function and grid engine):** Morris, G. M. et al. (2009). AutoDock4 and AutoDockTools4: Automated docking with selective receptor flexibility. *J. Comput. Chem.*, 30(16), 2785–2791. https://doi.org/10.1002/jcc.21256
- **AutoDockFR / ADFRsuite (`prepare_receptor4.py`):** Ravindranath, P. A. et al. (2015). AutoDockFR: Advances in protein-ligand docking with explicitly specified binding site flexibility. *PLOS Comput. Biol.*, 11(12), e1004586. https://doi.org/10.1371/journal.pcbi.1004586
- **AutoDockTools_py3 (Python 3 port supplying `prepare_receptor4.py`, `prepare_ligand4.py`, `prepare_flexreceptor4.py`):** Valdés-Tresanco, M. S. GitHub repository: https://github.com/Valdes-Tresanco-MS/AutoDockTools_py3
- **AutoSite (binding pocket detection):** Ravindranath, P. A. & Sanner, M. F. (2016). AutoSite: an automated approach for pseudo-ligands prediction — from ligand-binding sites identification to predicting key ligand atoms. *Bioinformatics*, 32(20), 3142–3149. https://doi.org/10.1093/bioinformatics/btw367
- **PDB2PQR (protein protonation/charge assignment):** Dolinsky, T. J. et al. (2007). PDB2PQR: expanding and upgrading automated preparation of biomolecular structures for molecular simulations. *Nucleic Acids Res.*, 35, W522–W525. https://doi.org/10.1093/nar/gkm276; Jurrus, E. et al. (2018). Improvements to the APBS biomolecular solvation software suite. *Protein Sci.*, 27(1), 112–128. https://doi.org/10.1002/pro.3280
- **PROPKA3 (empirical pKa prediction):** Søndergaard, C. R., Olsson, M. H. M., Rostkowski, M., & Jensen, J. H. (2011). Improved treatment of ligands and coupling effects in empirical calculation and rationalization of pKa values. *J. Chem. Theory Comput.*, 7(7), 2284–2295. https://doi.org/10.1021/ct200133y
- **Open Babel (cofactor protonation, format conversion):** O'Boyle, N. M. et al. (2011). Open Babel: An open chemical toolbox. *J. Cheminform.*, 3, 33. https://doi.org/10.1186/1758-2946-3-33
- **3Dmol.js / py3Dmol (interactive visualization):** Rego, N. & Koes, D. (2015). 3Dmol.js: molecular visualization with WebGL. *Bioinformatics*, 31(8), 1322–1324. https://doi.org/10.1093/bioinformatics/btu829

## Citing This Pipeline

If Rec-Prep is used to generate receptor structures reported in a publication, thesis, or preprint, cite the repository directly, in addition to the underlying tool citations above:

**Repository citation:**
Rec-Prep: Receptor Preparation & Grid Map Generation Pipeline for AutoDock-GPU. GitHub repository: https://github.com/MANAV2502/Rec-Prep

**BibTeX:**
```bibtex
@software{recprep2026,
  title  = {Rec-Prep: Receptor Preparation & Grid Map Generation Pipeline for AutoDock-GPU},
  author = {{Manav Patel}},
  year   = {2026},
  url    = {https://github.com/MANAV2502/Rec-Prep}
}
```

Include the specific commit hash of the repository used at the time of receptor preparation (e.g., via `git rev-parse HEAD` or the commit visible in the GitHub UI), since the notebook may be revised over time and downstream reproducibility depends on pinning the exact version consulted.

