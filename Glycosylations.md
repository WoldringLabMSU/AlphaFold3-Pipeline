# Guide for modeling glycosylations on MSU HPCC OnDemand
### By Theodore Belecciu (@Theodor-ator) and Alex Aljets (@alexaljets)
##### Last updated 4/14/2025

*Note*: The easiest way to model glycosylated protein structures (glycoproteins) is with a web server made by DeepMind called AlphaFold Server. This server should be good for most applications (glycosylated monomers, glycosylated multimers, monomers or multimers with multiple glycans, etc.), but it can’t handle glycans with more than 8 residues (i.e., sugar units like mannose, fucose, GlcNAc). This guide is for modeling glycans with more than 8 residues.

To use AlphaFold3 on the MSU HPCC, you will need to have an MSU HPCC account, and you will also need access to the Woldring Lab HPCC account. You will not need anything else, since AlphaFold 3 and all of its dependencies are there. You will need PyMOL or another molecular visualization tool to see your results at the end. 

1. Go to [HPCC OnDemand](https://ondemand.hpcc.msu.edu) to access your HPCC account.

2. Log in as you would with any other MSU application, and you should see a screen like the one in the image below: 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g1.png)

3. If you click on `Files` you should see a list of directories appear. If you’ve been added to the Woldring Lab HPCC account, you should see that directory appear (see below). Click to access it. Ignore the `scratch/groups/woldring_lab` directory, that’s not the lab research space. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g2.png)

4. In the Woldring Lab research directory, you should see a directory called `AlphaFold3`. Click to access it. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g3.png)

5. In this directory, you will see the directories in the image below, along with many other files. Ignore all of the files for now, the only important thing is the `inputs` directory.  

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g4.png)

6. The inputs directory contains JSON files. These contain all of the information needed for AlphaFold 3 to function. The JSON file you will use as a template is called `AF3_glycosylation_2.json`. You can see what it looks like below. Ignore the other JSON files, they’re from other runs lab members performed.

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g5.png)

7. The JSON file contains the protein amino acid sequence, the CCDs of the glycan residues that make up the ligand, and information on how they are connected 

8. To download this file and edit it on your computer, you can simply select it and then select the ‘download’ button. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g6.png)

9. You should have the amino acid sequence of your protein of interest, and you can replace the amino acid sequence in the template file with it. If your protein has more than one chain (i.e., it’s a multimer) you can add other bracketed “protein” elements after your first one like this (this is a different example from that in Step 6, but it shows how you would add another protein chain to the JSON file):

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g7.png)

10. You can add as many protein chains as you need. Pay very close attention to the formatting and the brackets! It will need to be exact to work. 

11. The ligand fields come after the protein. You need to know what amino acids will be glycosylated (their numbers), and what exact glycosylations you will need. You have to know how many glycan residues in total you have (sugar bases like mannose, N-acetyl glucosamine, glucose, etc.), as well as how many of each type you will have (i.e., how many mannose residues, glucose residues, etc.). For example, if you only have two amino acids that are glycosylated, and both of these glycans consist of N-acetyl glucosamine bound to glucose, you will have two N-acetyl glucosamine residues and two glucose residues, for a total of 4 glycan residues. 

12. Let’s analyze the glycan part of the `AF3_glycosylation_2` file: 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g8.png)

13. Notice how there are lists of IDs for each “ligand” field. You will need an ID for **each glycan residue you have.** The total number of IDs should be the same as the number of glycan residues. Even if all your glycan residues are the same (e.g., all are N-acetyl glucosamine), they will each need a different ID. Notice how  the “id” fields are followed by the CCD Codes of the glycan residues (these codes are the same as those mentioned in the instructions for using AlphaFold Server earlier in this document). In the example in the image above, there are three NAG residues (N-acetyl glucosamine) and seven mannose residues. **Remember to give each entity (protein sequence or glycan residue) a different ID!**

14. The most important part of the JSON file comes after the “bondedAtomPairs” field. This will tell AlphaFold exactly how the protein’s amino acids covalently bind the glycans and how the glycan residues are bound to each other. Let’s break this down further. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g9.png)

15. The syntax may be a bit difficult to follow, but it can be understood as the following: 

[“ID of first bond entity”, Position, Atom involved in bond], [“ID of second bond entity”, Position, “Atom involved in bond”]. In the example above, [“A”, 76, “ND2”], [“B”, 1, “C1”] indicates that ID A (the protein sequence in this case) forms a bond with its 76th amino acid (in this case that’s an asparagine) using its ND2 atom (nitrogen on side chain). The recipient of this bond is the glycan residue with ID B (a NAG in this case) at its 1st position (glycans residues only have one possible position, unlike proteins which have many amino acids) with its C1 atom (carbon 1, of six carbons). In the case of all glycan residues, C1 will be the atom receiving the bond from another entity. After [“A”, 76, “ND2”], [“B”, 1, “C1”], we have [“B”, 1, “O4”], [“C”, 1, “C1”]. Here, entity B (the NAG that is bound to the amino acid from protein entity A) uses O4 (oxygen 4) to bind entity C (another NAG, see image before the one above) at its C1. Notice that each bonded pair is surrounded by square brackets (e.g., [[“A”, 76, “ND2”], [“B”, 1, “C1”]], [[“B”, 1, “O4”], [“C”, 1, “C1”]]) and the whole set of bonded atom pairs is as well (for a total of three sets of square brackets). Make sure you keep this formatting! 

NOTE: When choosing an amino acid position to glycosylate, it may be difficult to determine its exact position in the protein sequence. To quickly get this, you can use an [online character counter](https://wordcounter.net/character-count). Here, you can paste the part of your protein sequence until your amino acid of interest (including it) to find its position. 

NOTE: The amino acid atoms that bond to glycans (e.g., ND2) have very specific names that cannot be determined intuitively, so I will record them here. Asparagine always uses its ND2 atom, Serine always uses its OG atom, and Threonine always uses its OG1 atom. Only ND2 is used in the above example. 

16. When you identify the glycans that you want to bond to your protein, they will likely have a diagram like the one below. These diagrams indicate which glycan oxygens participate in bonding, and you’ll need this information for the JSON file (the oxygen numbers are right next to the bonds and glycan residue icons). 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g10.png)

17. As an example, this is the bondedAtomPairs field that will result from the above glycan, assuming the protein sequence has ID “A”, amino acid number 145 (threonine) is bound to the glycan, the blue squares represent NAG, the green circles represent mannose, and the glycan residues have the IDs that they’re labeled with: 

“bondedAtomPairs”: [[[“A”, 145, “OG1”], [“B”, 1, “C1”]], [[“B”, 1, “O4”], [“C”, 1, “C1”]], [[“C”, 1, “O4”], [“D”, 1, “C1”]], [[“D”, 1, “O2”], [“E”, 1, “C1”]], [[“E”, 1, “O2”], [“F”, 1, “C1”]], [[“E”, 1, “O6”], [“G”, 1, “C1”]], [[“G”, 1, “O6”], [“I”, 1, “C1”]], [[“G”, 1, “O2”], [“H”, 1, “C1”]]] 

18. Once you finish editing your JSON file, you should change the “name” field to something identifiable and different from “Glycan_second_success”. This will be the name your results directory will appear under in the “outputs” directory: 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g11.png)

19. You now should be finished with the JSON file. Save it, name it whatever you want, and upload it into the inputs directory within the AlphaFold3 directory (see image below) 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g12.png)

20. Now you are almost ready to submit the job! Navigate into the main AlphaFold3 directory and locate the docking job file called `fold_alphafold3_glycan.sb`

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g13.png)

21. Download this file onto your computer and open it in a text editor. It should look like the following. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g14.png)

22. In this file, locate the string ‘AF3_throwaway.json’, and replace it with the exact name of the JSON file you just created and uploaded into inputs. Save the edited file and upload it into the main AlphaFold3 directory. Feel free to name it whatever you want, but make sure it has the .sb extension. 

23. Now, you’re ready to submit the AlphaFold3 job! Open a terminal window while you’re in the main AlphaFold3 directory. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g15.png)

24. After the terminal window loads, simply type `sbatch [exact name of the docking job file you just uploaded into the AlphaFold3 directory]`. For example, if you named the file `AF3_job.sb`, you would simply type `sbatch AF3_job.sb` (see below). Hit ENTER, and you’re good to go! 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g16.png)

25. You can check the status of your job with the `qs` command. If you get something like the following, it means the job is either running or preparing to run: 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g17.png)

26. The job should take around 30 minutes. The results will appear in the 'outputs' directory in a directory named after what you specified in the “name” field of the JSON file you uploaded. 

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g17.png)

27. Inside the results folder, you’ll see something like in the image below. The most important file by far is the `.cif` model. You can download this structure and visualize it with tools like PyMOL. Thus you will be able to see your glycosylated protein structure.  

![Alt text](https://github.com/WoldringLabMSU/AlphaFold3-Pipeline/blob/main/pictures/g17.png)
