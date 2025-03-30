# AlphaFold3-Pipeline
### By Theodore Belecciu (@Theodor-ator) and Alex Aljets (@alexaljets)
##### Last updated 3/23/2025

This page provides a quick overview of command line usage and requirements for running AlphaFold3 on the MSU HPCC. For comprehensive documentation, please see the full guide, named "comprehensive_guide.md" (in this repository).

This basic guide will explain how you can run structure predictions with AlphaFold3 on HPCC. AlphaFold3 has been installed in the Woldring Lab research directory (/mnt/research/woldring_lab), and it is now available for everyone added to the lab research account. 

This guide will cover monomer, monomer-ligand complex, and multimer prediction.

## Preliminaries 

1. Ensure you have HPCC access and that you can navigate to the Woldring Lab research directory (/mnt/research/woldring_lab). 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss1.png)

Figure 1. Research directory in main HPCC OnDemand menu.  

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss2.png)

Figure 2. AlphaFold3 directory in the Woldring Lab research space. 

## Running the jobs 

1. Navigate inside the AlphaFold3 directory using HPCC OnDemand. 

2. Navigate inside the ‘inputs’ directory.  

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss3.png)

Figure 3. Inside of AlphaFold3 directory, which contains the ‘inputs’ directory. 

3. Inside of the ‘inputs’ directory there are three JSON files. These are named for the three different types of predictions that are discussed in this tutorial. They are also formatted differently based on the type of prediction they are meant for.    


3a. For monomers, open the JSON file called **alphafold_input_monomer.json**. 
This is the exact format you will need if you want to predict the structure of a monomeric (i.e., single-chain) protein. You will simply need to change the amino acid sequence in the “sequence” field to that of the protein you desire to predict. 
<span style="color: red;">-NOTE: AlphaFold3 is VERY picky about the formatting of this JSON file. It does not tolerate spaces, line indents, line breaks, or any other deviations. When you paste your sequence, make sure it is all on ONE line. For large sequences, use your Backspace/Delete key at the beginning of each line that your pasted sequence is in to check for spaces (don’t assume that the sequence will paste on one line just because the source you copied it from had it on one line). </span>

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss4.png)

Figure 4. Example JSON file used for monomeric protein structure prediction (called alphafold_input_monomer.json) 

3b. For protein-ligand complexes, open the JSON file called **alphafold_input_protein-ligand_complex.json**. 
Change the field after “sequence” to the sequence of your protein of interest. Properly format your sequence. 
Change the field after “smiles” to the SMILES string of your ligand of interest. If you don’t have this for your molecule, you can search the name of your molecule on PubChem, locate it, and scroll all the way down to the SMILES field to copy it. If your molecule doesn’t have a name that PubChem can identify it by, but you have its 3D or 2D structural file (PDB, SDF, MOL2, etc), consider installing OpenBabel (https://openbabel.org/docs/Installation/install.html). You can use this chemical toolkit for a variety of file conversions, like obtaining SMILES strings from structural files. When pasting the SMILES string into the JSON file, heed the advice given in the note after Step 4 in the Monomer section.

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss8.png)

Figure 5. Example JSON file for protein-ligand complex prediction (alphafold_input_protein-ligand_complex.json) 

3c. For multimers, open the JSON file called **alphafold_input_multimer.json**. 
Determine what type of multimer you want to predict. If your desired protein has two chains, (i.e., a dimer) you can use the JSON file exactly as it is, simply replacing the amino acid sequences with those of your chains.
If you want to predict a complex with more than two protein chains or a complex with multiple protein chains and ligands, you need to add new elements to the JSON file. Each element starts with a bracket “{“, is followed by a label (e.g., “protein”), which is followed by an ID (e.g., [“A”], [“B”], etc.), a sequence or SMILES string, and it ends with a closing bracket “}”. If there are other elements that follow, the last bracket is followed by a comma. Figure 10 below shows an example of a JSON file for a trimeric protein in complex with a small molecule. Pay very close attention to the number, type, and formatting of the brackets for each element. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss9.png)

Figure 6. Example JSON file for multimer submission. Note the different protein chains. 

7. You should also change the name of the protein in the “name” field. Name it something identifiable since this name is used to create an output directory for your protein in woldring_lab/AlphaFold3/outputs 

8. Once you made the necessary changes to the JSON file, save it and upload it back into the ‘inputs’ directory.  

9. Go into the main AlphaFold3 directory and open the file called ‘fold_alphafold3.sb’ 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss5.png)

Figure 7. Job file that you will run within the main AlphaFold3 directory. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss6.png)

Figure 8. Job file you will run called ‘fold_alphafold3.sb’. Note that by default, the name of the JSON file it refers to is alphafold_input.json. Change this to the name of the JSON file you want to use as an input. This should be the only thing you need to change. 

 
10. Change the name of the JSON file as indicated in Figure 6 if needed. Save the change and upload the file again to your main AlphaFold3 directory. Now you can run the job! Simply type ‘sbatch fold_alphafold3.sb’ into your command prompt to submit the job, and ‘qs’ to check its status.  

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss7.png)

Figure 9. Example of the job submission command being entered. 

11. The job will probably take anywhere from 15 minutes to 3 hours to finish depending on the size of the protein and the availability of similar proteins in the PDB. As a benchmark, a 900 amino-acid protein with many rare domains took just 1 hour and 15 minutes to finish. The job will automatically stop after 3 hours and 59 minutes, even if it isn’t finished (but it probably will be). This time limit enables access to buy-in GPU nodes, so your job won’t have to spend much time in the queue.  

12. Once your job finishes, refer to the Prediction Results section of this document.


## Prediction results 

1. Once your job finishes running (which you can check with the ‘qs’ command), navigate to the ‘outputs’ directory within the main AlphaFold3 folder. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss12.png)

Figure 12. Location of ‘outputs’ directory within the main AlphaFold3 folder. 

2. Inside this ‘outputs’ directory, you will find directories named after what you entered in the “name” field of the JSON file(s) that your job(s) referenced. 

3. If you enter these directories and list the contents ('ls'), you will see the following: 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/ss13.png)

Figure 13. Example results directory. 

4. The structure files produced by AlphaFold3 are the ones ending in ‘_model.cif’. You can open these structure in PyMOL to visualize them. AlphaFold3 produces a total of 5 output structures for each prediction, and they can each be found in the directories starting with ‘seed’. The structures are ranked by overall prediction confidence (score of 0 to 1, with 1 being the highest), and the ‘seed’ directories are in order of decreasing confidence (i.e., seed-1_sample-0 contains the highest confidence structure, and seed-1_sample-4 contains the lowest confidence structure). The ‘model.cif’ structure in the main ‘outputs’ directory is the same as the one in the ‘seed-1_sample-0’ directory. The rest of the files summarize different aspects of the confidence scores.
