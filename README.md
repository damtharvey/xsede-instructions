# Instructions
These instructions will apply to most people in our group. We mostly program in Python, use CUDA for numerical computation, and we manage our environments using conda.

0. If you haven't created an XSEDE account, go to the [XSEDE User Portal](https://portal.xsede.org/) and click "Create Account".

1. Ask our allocation manager Saeed Taheri (staheri@cs.utah.edu) to add you to our project (we only have one) and activate you as a user of the GPU-AI resource. Give him your XSEDE user name.

2. Save your local environment into a file. Suppose you want to save it as `environment.yml`. Make sure your environment is activated, and
```
conda env export > environment.yml
```

3. Login to Bridges. Suppose your username is `username`, then
```
ssh username@bridges.psc.xsede.org
```

4. Somehow get your `environment.yml` in here. You can use `scp` (or WinSCP). They have Git if you want to use that.

5. They have Environment Modules. To see which environment modules are available, 
```
module avail
```
Take note of the one(s) you want to use. For most of us, `cuda/10.1` and `anaconda3` are sufficient.

6. For now, use `anaconda3` to set up your environment. Assuming your `environment.yml` is in this directory,
```
module load anaconda3
conda env create -f environment.yml
```

7. Find your charge ID for the GPU-AI resource. You can see it using
```
projects
```

8. Write your batch script. See the [Bridges User Guide](https://portal.xsede.org/psc-bridges) for instructions on how to do this. Below is an example with comments.
```sh
#!/bin/bash  ## This specifies the shell that will interpret the script. There are a few you can choose from.

## The following lines preceded by #SBATCH are sbatch options.

#SBATCH --account=cc123xy  ## This is the charge ID that you found before.
#SBATCH --partition=GPU-AI
#SBATCH --nodes=1
#SBATCH --gres=gpu:volta16:8  ## This has to be in the form gpu:type:number where type is either
                              ## volta16 or volta32. volta16 is 16G V100, while volta32 is 32G V100.
                              ## If you use volta16, number can be 1-8.
                              ## If you use volta32, number can be 1-16.
#SBATCH --time=01:00:00  ## time limit, in the form hours:minutes:seconds
#SBATCH --mail-type=ALL  ## Choose what you want to be emailed about.
#SBAtCH --mail-user=u1234567@utah.edu  ## Your email address.

source /etc/profile.d/modules.sh  ## Required for using module in GPU-AI

## Load the modules you noted from before.
module load cuda/10.1
module load anaconda3

conda init bash   ## Required before using conda activate
source ~/.bashrc  ## Required before using conda activate
conda activate some_environment

python some_experiment.py
```

9. Run your batch script. Suppose you named your batch script `some_batch_script.sh`. Then
```
sbatch some_batch_script.sh
```

# Notes
* For each additional environment you want to use, repeat steps 2-6.
* GPU time is calculated as **total GPU time**. Therefore, if you use 16 GPUs for 1 hour, you will use 16 GPU hours.
* If you need technical support, you can go to https://portal.xsede.org/group/xup/help-desk.

If these instructions are wrong, please let me know.
