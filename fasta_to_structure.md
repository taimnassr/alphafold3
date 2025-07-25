# **Fasta To Structure using AlphaFold3**

## **IMPORTANT NOTE BEFORE ANYTHING**
When you use Alphafold3, you need to cite the people who created it as the following: 

```
@article{Abramson2024,
  author  = {Abramson, Josh and Adler, Jonas and Dunger, Jack and Evans, Richard and Green, Tim and Pritzel, Alexander and Ronneberger, Olaf and Willmore, Lindsay and Ballard, Andrew J. and Bambrick, Joshua and Bodenstein, Sebastian W. and Evans, David A. and Hung, Chia-Chun and O’Neill, Michael and Reiman, David and Tunyasuvunakool, Kathryn and Wu, Zachary and Žemgulytė, Akvilė and Arvaniti, Eirini and Beattie, Charles and Bertolli, Ottavia and Bridgland, Alex and Cherepanov, Alexey and Congreve, Miles and Cowen-Rivers, Alexander I. and Cowie, Andrew and Figurnov, Michael and Fuchs, Fabian B. and Gladman, Hannah and Jain, Rishub and Khan, Yousuf A. and Low, Caroline M. R. and Perlin, Kuba and Potapenko, Anna and Savy, Pascal and Singh, Sukhdeep and Stecula, Adrian and Thillaisundaram, Ashok and Tong, Catherine and Yakneen, Sergei and Zhong, Ellen D. and Zielinski, Michal and Žídek, Augustin and Bapst, Victor and Kohli, Pushmeet and Jaderberg, Max and Hassabis, Demis and Jumper, John M.},
  journal = {Nature},
  title   = {Accurate structure prediction of biomolecular interactions with AlphaFold 3},
  year    = {2024},
  volume  = {630},
  number  = {8016},
  pages   = {493–-500},
  doi     = {10.1038/s41586-024-07487-w}
}
```
## Another important note:
This does prediction of multiple sequences found in one fasta file. However, here you have only simple prediction of protein structure (no binding legends, no PTM, and so on). I will soon make a tutorial for these customizations. 

## **1) Convert *.fasta* file into *.json* files**
Alphafold3 only takes input files in *.json* format, which is not as widely used as *.fasta*. But no worries, I made a python script that converts the fasta file into json file(s). Alphafold3 only takes one sequence per *.json* file, unlike previously where you could include multiple sequences in one file. Therefore, if you have a fasta file with multiple sequences that you want to predict their structure, you need to have a sepearte json file for each sequence. This is what my python script do for you .
Here is how you do it from our server:
 ```bash
#bash
# First create a conda environment because the python version there is different than the one in system
conda create -n <env_name> # you chose your enviornment name and put it instead of <env_name>

#Activate the environment
conda activate <env_name>
#you should see (env_name) instead of (base) on the beginning of the code line

#run the script
#first make sure to know in which directory you have your input .fasta file
#usually it should be in your own folder within the system. This is up to you
#also you need the directory where you will store the .json file outputs
#the function can create the directory for you, you just have to specifiy it
#it should be of course a directory where you have write permissions (probably in your own folder)

#now let us run the script
python3 /opt/alphafold3/fasta_to_json.py \<full_path_to_your_fasta_file.fasta> \<full_path_to_json_files>
#/opt/alphafold3/fasta_to_json.py is where my script is and please write the full path
#<full_path_to_json_files> doesn't need to be vcreated before, the function can make it for you
# Please write the FULL paths always
# make sure that there is no space between "\" and "<full_path...> because this is a common source of error
# \mnt/DATA/user/my_file and NOT \ mnt/DATA/user/my_file so no space
#yout output with multiple .json files will be in <full_path_to_json_files>
```
Now each *.json* file will have the name <sequenceX.json>, and each sequence in the *.fasta* file will have a *.json* file. 

We now need to leave the conda environment for the next step
```bash
#bash
conda deactivate
#you should see (base) at the beginning of the code line
```

## **2) Running AlphaFold3**
Alphafold3 structure prediction consists of two main steps:
1) Data Pipeline: This consists of Multiple Sequence Alignment (MSA) and template search. It searches public databases for homologs of your structure and find templates for structural homologs. This is a computationally intensive step that relies on CPU. Luckily, we have multiple CPU cores (around 112) where we can parallelize this procees. Parallelization means that in case we have multiple input *.json* files, we can split this process on the different cores (max 112) so each core can process one *.json* input.
2) Featurization and Model Inference: This infers the model based on the MSA. This process depends on GPU not CPU. Since we only have one GPU, we can't parallelize this process. Luckily that this one is not as computationally intensive as the one before.

### **Data Pipeline**
For this we need one directory (folder) where our *.json* files exist. Remember that you generated this in the last step, so your *.json* files should be in the <full_path_to_json_files> specified in the previous step. 
In the data pipeline, the function will take the *.json* path as input then modify it to include msa and template search and outputs a modified *.json* file. The modified json file should be stored in a new directory (where you have writing permissions of course) and we would call <full_path_to_json_modified2> 

**NOTE** this is the step that will take the majority of time. It is also dependent on the number of cores you will use and the length of the sequences as well. I ran this step on around 50 protein sequences, each around 150 amino acids, and I ran it on 50 cores (our max is 112). This took me around 40 minutes. Sometimes using more cores might not be helpful and might actually slow you down. If for example, you have 20 sequences, I think 20 cores should be enough and there is no reason to use the 112 cores. On the other hand, if you wanna model the whole proteome, then you may consider more cores. You can specifiy the number of cores as below

```bash
#bash 
#now we want to run the first step of alphafold3 which is computationaly heavy
#again we use full paths ALWAYS
/opt/alphafold3/run_alphafold3_step1_msa.sh <full_path_to_json_files> <full_path_to_json_modified2> <num_cores>
#/opt/alphafold3/run_alphafold3_step1_msa.sh is the script for the first step with parallelization
#<full_path_to_json_files> this is the directory (folder) where your .json files that were outputted in last step
#exists. you only need to specifiy the path of the directory and NOT the .json files themselves
#<full_path_to_json_modified2> this is the directory where the modified .json files that would have the msa and would be #outputted would exist
#<num_cores> up to you . max is 112 but please don't use all cores. let be around the number of your sequences
```

After this runs (will probably take some time), the modified *.json* files would be stored in the <full_path_to_json_modified2> directory. However each modified *.json* files would be stored in a folder with its name inside the <full_path_to_json_modified2> directory. So basically, to access each *.json* file it would be 
<full_path_to_json_modified2/sequenceX/sequenceX.json>
For the next step, you need all *.json* files in one subdirectory and not in multiple subdirectories 
Therefore, you have to run the following lines

```bash
#bash
#again always use full paths
mkdir -p <full_path_to_json_modified3>
# this is to create the new directory where all the .json files will be listed properly
#also you need to have writing permissions on <full_path_to_json_modified3>
find <full_path_to_json_modified2> -type f -name "*.json" -exec cp {} <full_path_to_json_modified3> \;
#now the new .json files with the msa are all in one subdirectory
```

### **Featurization and Model Inference**
Now, it is the time to generate our models. We can't sadly parallelize this process, but it should not be as time intensive as the one before. When running this on my 50 sequences of ~150 bp each, it took me around 5 minutes for it to finish. Here our input *.json* files with msa are in the <full_path_to_json_modified3> directory, and our output with models will be in the output directory we specify <full_path_to_alphafold_output>. This directory should be somewhere where we have writing permissions. we don't need to create it before, it can actually be done by the function itself. 

```bash
#bash
#always full file paths
/opt/alphafold3/run_alphafold3_step2_model.sh <full_path_to_json_modified3> <full_path_to_alphafold_output>
#now your models will be in the <full_path_to_alphafold_output> directory
```

In the <full_path_to_alphafold_output>, you will now found many folders, with each folder corresponding to a *.json* file or one sequence in your original *.fasta* file. The folder name would correspond to the *.json* file name, which is usually the sequence name. Inside each folder, you will have the different outputs of alphafold. For more information about the different outputs, please refer to here https://github.com/google-deepmind/alphafold3/blob/main/docs/output.md 
I think the file that is most-interesting to you would be the *.cif* file which is where your structure is that can be opened with any molecular structural viewer. I use https://molstar.org/viewer/ for that, but then that is your choice. 

## **Miscellaneous**
The scripts are not fully optimized yet- For example, they don't yet check if the number of inputs is correct as well as whether their format is correct. They won't tell you which input is missing or wrong, and they will only output "error". 
However, they should work if you provide *.fasta* file with the right format and followed the tutorials are instructed. I hope to optimize them ASAP. 
In case you need help please don't hesitate to come to me in my office, or contact me on my work email:
tnassr1@uni-koeln.de

Wish you a lot of fun with this tutorial,
Taim





