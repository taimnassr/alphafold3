# alphafold3

Please ignore this tutorial. The tutorial of multiple sequence prediction is found in the fasta_to_structure.md

This an instruction tutorial on how to use alphafold3 on the server:

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

## **1) Prepare your files and directories**

**Directories**:  
Please navigate to the following directory on the server.  
```bash
#bash
cd /opt/alphafold3
```
You should find the sub directories *input_files* and *output_files* there when you check the directories :

```bash
#bash
ls
# input_files output_files should show in the output
```
Navigate to the input_files directory, here I will use the full path which is better:  

```bash
#bash
cd /opt/alphafold3/input_files/
```

Inside the *input_files/* subdirectory, you should fine an *input_file_template.json* . Please **don't** modify it directly. Instead, you copy it first and modify the copy. 

First check if the *input_files/* have the template file: 

```bash
#bash
ls
# input_file_template.json should be there
```
Now, please copy the template file and modify **the copy** and **not** the original. Here how you can copy it:

```bash
#bash
#make sure you are in the /opt/alphafold/input_files directory
cp input_file_template.json <my_file_name>.json
# put whatever file name you want in <my_file_name>
```

Now, open the file you created <my_file_name>.json:
```bash
#bash
nano <my_file_name>.json
```

The input file should be formatted as the following below. Please replace the name (***"sample_name"*** in the example below) with your sample name. Also, replace the sequence
(***"GM...LAV"*** below) with your sequence. After you finish, please press **Ctrl+O (Strg+O)** then press **enter** then press **Ctrl+X (Strg+X)**.  It is **very essential** that your
input file is in the input_directory. 

**Also it is IMPORTANT that the sequence in the nano script is written on one line without any newline character breaks or any spaces**

```
{
  "name": "2PV7",
  "sequences": [
    {
      "protein": {
        "sequence": "GMRESYANENQFGFKTINSDIHKIVIVGGYGKLGGLFARYLRASGYPISILDREDWAVAESILANADVVIVSVPINLTLETIERLKPYLTENMLLADLTSVKREPLAKMLEVHTGAVLGLHPMFGADIASMAKQVVVRCDGRFPERYEWLLEQIQIWGAKIYQTNATEHDHNMTYIQALRHFSTFANGLHLSKQPINLANLLALSSPIYRLELAMIGRLFAQDAELYADIIMDKSENLAVIETLKQTYDEALTFFENNDRQGFIDAFHKVRDWFGDYSEQFLKESRQLLQQANDLKQG"
      }
    }
  ],
  "modelSeeds": [1],
  "dialect": "alphafold3",
  "version": 1
}

```

The **output_directory** is already created by me (Taim) */opt/alphafold3/output_files* , and that is where the output will be stored.

## **2) Run Alphafold3:
I prepared a nano script **./docker_run.sh** that would do the job for you. 


```bash
#bash
#navigate to /opt/alphafold/
cd /opt/alphafold3/
#check if docker_run.sh is there
ls
# you should see docker_run.sh there
#run the following
./docker_run.sh <full_path_to_your_input_directory> <full_path_to_output_directory> <my_file_name.json>
#<full_path_to_your_input_directory> is probably /opt/alphafold3/input_files/
#<full_path_to_your_output_directory> is probably /opt/alphafold3/output_files/ 
#<my_file_name.json> is the file name of the copy input file you create it that should be inside /input_directory
#only use the file_name.json . it won't work if you specified the full path
# it will take a few minutes to run
```

Your output should be now in the *output_directory/<sample_name>*. The output is a folder within the *output_directory/* whose name is the same as the sample name you provide in the <my_file_name.json> file, in the example above it is **"2PV7"**. So  the subdirectory in our example would be *output_files/2PV7*

```bash
#bash
#navigate to output directory
cd /opt/alphafold3/output_files/<your_sample_name>/
#<your_sample_name> is what you put instead of "2PV7" in <my_file_name.json>
```
In that directory, your will find the different outputs by alphafold3. The most relevant one is <your_sample_name.cif> which you can download and view it using any molecular viewer.
I use https://molstar.org/viewer/ for example. 

In case you have any questions, please don't hesistate to come to my office, or contact me on tnassr1@uni-koeln.de 
