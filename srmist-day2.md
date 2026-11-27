---
layout: guide
title: SRMIST Day 2 | Three protein design projects
permalink: /srmist-day2/
---

# Day 2 · Three protein design projects

Yesterday you ran a supplied example. Today your team will start with a reference structure and a blank YAML file. Choose one project. By the end of the workshop, another team should be able to read your config, predict what the model was asked to build, and find evidence for or against that prediction in your output.

Bring your working Day 1 checkout and a structure viewer. Work in three parallel teams so the class can compare all three projects at the end. [Day 1 setup](/srmist-day1/)

## Update your Day 1 checkout

Open PowerShell in the `RFD4-Proteina-windows` folder you cloned on Day 1. Pull the workshop updates before writing your YAML.

```powershell
git switch experimental/windows-cu132
git pull --ff-only
```

The three reference structures are in `docs/examples/inputs/`: use `5o45_cropped.cif.zst` for PD-L1, `1BRZ.cif` for brazzein, or `5CPA.cif` for the hydrolase. The update also includes the PyMOL script used at the end of this guide.

## Pick a project

### 1. Design a binder for PD-L1

**Question.** Where on PD-L1 do you want your proposed binder to land, and how will you tell whether it did? For this exercise, your *candidate epitope* is the patch on the **target** that you predict the new binder will contact.

#### Choose an epitope before writing YAML · 15 minutes

1. **Orient yourself.** Open the deposited 5O45 complex in a structure viewer. Display PD-L1 (chain A) as a molecular surface and the bound peptide (chain B) in a contrasting color. Toggle the peptide off and on to see its footprint. Then open the cropped classroom input beside it. Identify which parts of the deposited structure are present in the file you will actually use. [Explore the 5O45 complex in 3D](https://www.rcsb.org/3d-view/5O45)
2. **Nominate two patches.** Color two candidate regions on the cropped target and rotate the structure a quarter turn. For each, check whether the highlighted residues still form one exposed neighborhood in 3D. Residues far apart in sequence can meet on the same face. Check each patch against the deposited structure as well. If it looks exposed only where the classroom file was cut, mark that as a reason to reconsider.
3. **Make a choice another team can reproduce.** Pick the patch for which you can show the clearest structural evidence. Hand a teammate the chain and residue labels from your input, hide your coloring, and ask them to recover the same patch. Resolve any numbering mismatch before translating your choice into YAML.

**Before inference**, save a labeled surface view and finish this sentence together: “We chose patch ___ because ___. We will call the placement a miss if ___.” Write your own YAML to describe the target, the new chain, and the intended contact region.

**After inference**, align the input and output on PD-L1. Restore your original view and patch coloring, then show the generated chain. Which labeled residues face the new chain? Did it reach the middle of your patch, graze an edge, or land elsewhere? Use that paired view as your evidence. Binding affinity remains an experimental question.

### 2. Scaffold a sweetness-associated surface from brazzein

**Question.** Can a new scaffold present a recognizable patch of brazzein on its surface? Several separated regions contribute to sweetness. Pick one literature-supported patch and mark it on one member of the NMR ensemble. Explain why you chose those residues together in three dimensions. [Brazzein structure, 1BRZ](https://www.rcsb.org/structure/1BRZ) · [Experimental mapping of sweetness-associated sites](https://pubmed.ncbi.nlm.nih.gov/20302879/)

**Decision for your YAML.** Which residue identities and coordinates define your motif? Where may the surrounding scaffold change? Draw any segments that are distant in sequence but adjacent in space.

**Evidence to bring back.** Superpose the motif atoms you designated in advance. Show the overlay, then show a surface view of the whole design. A close overlay and a buried patch can occur in the same structure. Explain what each view tells you and what would still be needed to establish sweetness.

### 3. Build a candidate metallohydrolase scaffold

**Question.** Can a new scaffold accommodate a metal site and leave room for a substrate to approach it? Use bovine carboxypeptidase A as the reference. It is a zinc-containing hydrolase that cleaves a peptide bond at a protein or peptide’s C-terminus. Locate the zinc in the structure viewer before choosing which protein atoms to retain. [Carboxypeptidase A structure, 5CPA](https://www.rcsb.org/structure/5CPA)

**Decision for your YAML.** How will you represent the zinc and its surrounding protein motif? Which parts of the scaffold are free to change? Trace a plausible approach to the pocket and identify one way the new scaffold could obstruct it.

**Evidence to bring back.** Label the same metal-to-protein atom pair in the reference and design. Report the two distances in ångströms, show the pocket opening, and discuss what those observations can tell you about geometry. Catalytic activity would require a separate measurement.

## From sketch to YAML

Start with a blank file. Use this worksheet to decide what to write. Keep your answers next to the config for peer review. [RFD4 YAML field reference](https://github.com/r-krishna/RFD4-Proteina/blob/experimental/windows-cu132/docs/reference/yaml-json.md)

| Before writing | Write down |
| --- | --- |
| What is in the coordinate file? | Its filename and the chain, residue, or metal identifiers you actually see in the file. Input paths in the YAML are relative to the YAML file. |
| What will the model receive? | Under `input`, identify the structure. For `contig`, sketch the retained pieces and the new segments in order, including any chain breaks. |
| What must carry through? | Under `conditions`, distinguish residue identity from atom coordinates. Name one choice you deliberately leave open. |
| How can someone catch a mistake? | Give your reviewer the YAML alone. Ask them to mark the fixed and generated parts, then compare their sketch with yours. |
| What will you measure? | Name the reference atoms or surface region, the matching output feature, and one result that would make you reconsider your design. |

## Run and report

Save your team’s YAML as `day2.yaml` in the Day 1 repository root on your workstation. In PowerShell, from that root, run one replicate. If several teams share a checkout, give each YAML and output folder a distinct name.

```powershell
.\scripts\windows-gpu.cmd inputs=day2.yaml num_replicates=1 out_dir=runs/day2-v1
```

Compare the program’s printed input summary with your sketch. Once the run finishes, open the generated structure under `runs/day2-v1/inference_samples`. For a revision, save a second YAML and choose a fresh output folder. If a run stops, keep the first error and the input summary so a peer can help locate the mismatch.

**Hand in** your YAML, one annotated figure, and a three-sentence interpretation. What did you ask the model to preserve? What do you observe in the output? Which conclusion still needs independent evidence?

## Visualize the motif in PyMOL

**In PowerShell**, from the same checkout, find the generated structure for your run. If you chose a different output folder, substitute it below.

```powershell
Get-ChildItem .\runs\day2-v1\inference_samples -Recurse -Filter '*_generated.cif' |
    Select-Object -ExpandProperty FullName
```

**In a fresh PyMOL session**, use the command line at the bottom of the viewer. Replace the first path with your checkout folder and the second with the file printed by PowerShell. Use forward slashes in Windows paths. Enable `cif_keepinmemory` **before** loading the file so PyMOL keeps the motif annotations.

```pymol
cd C:/path/to/RFD4-Proteina-windows
set cif_keepinmemory, on
load C:/path/to/your_generated.cif, design
run scripts/show_rfd4_motif.py
show_rfd4_motif design
zoom motif_atoms, 8
```

**Magenta marks the coordinate-conditioned atoms.** The script names this selection `motif_atoms`, shows their protein residues as sticks, and displays any marked zinc as a sphere. To identify the selected atoms in your output, enter:

```pymol
iterate motif_atoms, print(chain, resn, resi, name)
```

A hotspot or sequence constraint alone does not mark an atom as coordinate-conditioned. If the script reports zero selected atoms, check which conditions your team specified.
