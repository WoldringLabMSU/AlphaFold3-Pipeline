# AlphaFold3-Pipeline
### By Theodore Belecciu (@Theodor-ator) and Alex Aljets (@alexaljets)
##### Last updated 4/13/2025

This page provides a quick overview of command line usage and requirements for running AlphaFold3 on the MSU HPCC. For comprehensive documentation, please see the full guide, named ["comprehensive_guide.md"](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/comprehensive_guide.md).

This basic guide will explain how you can run structure predictions with AlphaFold3 on HPCC. AlphaFold3 has been installed in the Woldring Lab research directory (`/mnt/research/woldring_lab`), and it is now available for everyone added to the lab research account. 

This guide will cover monomer, monomer-ligand complex, and multimer prediction.

## Once you have HPCC access

1. Navigate to the Woldring Lab AlphaFold3 research directory (`/mnt/research/woldring_lab/AlphaFold3`).  

2. Inside of the ‘inputs’ directory upload your JSON file. They are also formatted differently based on the type of prediction they are meant for. Change the name of the protein in the “name” field and other relevant information as shown below.

3. Go into the main AlphaFold3 directory and open `fold_alphafold3.sb`.

4. Within `fold_alphafold3.sb`, change the name of the JSON file it refers to to the name of the JSON file you want to use as an input. Save the change and upload the file again to your main AlphaFold3 directory.

5. Type `sbatch fold_alphafold3.sb` into your command prompt to submit the job, and `qs` to check its status.

6. View the results in the ‘outputs’ directory.

**For monomers,** open the JSON file called `alphafold_input_monomer.json`. 
This is the exact format you will need if you want to predict the structure of a monomeric (i.e., single-chain) protein. You will simply need to change the amino acid sequence in the “sequence” field to that of the protein you desire to predict. 
<span style="color: red;">-NOTE: AlphaFold3 is VERY picky about the formatting of this JSON file. It does not tolerate spaces, line indents, line breaks, or any other deviations. When you paste your sequence, make sure it is all on ONE line. For large sequences, use your Backspace/Delete key at the beginning of each line that your pasted sequence is in to check for spaces (don’t assume that the sequence will paste on one line just because the source you copied it from had it on one line). </span>

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss4.png)

Figure 1. Example JSON file used for monomeric protein structure prediction (called alphafold_input_monomer.json) 

**For protein-ligand complexes,** open the JSON file called `alphafold_input_protein-ligand_complex.json`. 
Change the field after “sequence” to the sequence of your protein of interest. Properly format your sequence. 
Change the field after “smiles” to the SMILES string of your ligand of interest.

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss8.png)

Figure 2. Example JSON file for protein-ligand complex prediction (alphafold_input_protein-ligand_complex.json) 

**For multimers,** open the JSON file called `alphafold_input_multimer.json`. 
If your desired protein has two chains, (i.e., a dimer) you can use the JSON file exactly as it is, simply replacing the amino acid sequences with those of your chains.
If you want to predict a complex with more than two protein chains or a complex with multiple protein chains and ligands, you need to add new elements to the JSON file. Each element starts with a bracket “{“, is followed by a label (e.g., “protein”), which is followed by an ID (e.g., [“A”], [“B”], etc.), a sequence or SMILES string, and it ends with a closing bracket “}”. If there are other elements that follow, the last bracket is followed by a comma. Figure 10 below shows an example of a JSON file for a trimeric protein in complex with a small molecule. Pay very close attention to the number, type, and formatting of the brackets for each element. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss9.png)

Figure 3. Example JSON file for multimer submission. Note the different protein chains. 


The job will probably take anywhere from 15 minutes to 3 hours to finish depending on the size of the protein and the availability of similar proteins in the PDB. As a benchmark, a 900 amino-acid protein with many rare domains took just 1 hour and 15 minutes to finish. The job will automatically stop after 3 hours and 59 minutes, even if it isn’t finished (but it probably will be). This time limit enables access to buy-in GPU nodes, so your job won’t have to spend much time in the queue.  
