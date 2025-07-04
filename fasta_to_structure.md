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
