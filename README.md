#Beginner's guide to Crick

##Contents:

###All about Crick:
- What is Crick
- Setting up your account
- Crick defaults
- Installing programs on Crick
- Special Crick things [scratch usage, tentakel]
- Submit jobs to Crick 
    
###Unix computer tips:
- Bash 
- R basics [input/output especially, herefiles, pull from distributions, creating your own distribution, things people in the lab need]

###All about Crick:

####What is Crick:

Crick is a computer cluster that runs a distribution of Linux called CentOS version 5.8. 

Crick has a **head node** , which controls the cluster, and **compute nodes** which is where the action happens. For the most part, you interact with Crick using scripts to launch jobs on the compute nodes; you don't run processes on the head node and you don't log into the compute nodes directly.

####Setting up your account:

In order to create an account on Crick you need to send the CSE Help department your SSH public key. This key is a file created on your computer (not Crick) that identifies your computer as your computer to Crick. It's like a password that you don't need to type in. The process for generating your SSH public key is as follows:

#####Mac:

a. Open the terminal (Applications -> Utilities -> Terminal)
b. Generate your key:

		$ ssh-keygen -t rsa

c. Accept the default location by pressing Enter.

d. Type in a passphrase.

e. Your system will now generate a key pair:

		Your identification has been saved in /Users/myname/.ssh/id_rsa.
		Your public key has been saved in /Users/myname/.ssh/id_rsa.pub.
		The key fingerprint is:
		ae:89:72:0b:85:da:5a:f4:7c:1f:c2:43:fd:c6:44:38 myname@mymac.local
		The key's randomart image is:

		+--[ RSA 2048]----+
		|                 |
		|         .       |
		|        E .      |
		|   .   . o       |
		|  o . . S .      |
		| + + o . +       |
		|. + o = o +      |
		| o...o * o       |
		|.  oo.o .        |
		+-----------------+

**WARNING:** There are two keys generated: private and public. The public key is sent to the Crick and the private key stays where it is. NEVER SHARE YOUR PRIVATE KEY. Any computer with your private key can log in to your account. Treat your private key like a password.

f. Your keys are located in the .ssh folder in your home directory:

		/Users/[username]/.ssh

g. Your public key is in a file called `id_rsa.pub`. Attach this file to the email you send to CSE Help.
http://wiki.cse.ucdavis.edu/cgi-bin/index2.pl

The cluster we are signing up for is the Crick/MCB/MMG/BioMath
MCB is sponsoring our account.

#####Windows:
a. Follow the steps here:
If you have Cygwin: 

<https://help.github.com/articles/generating-ssh-keys/>

If not: 

<http://siteadmin.gforge.inria.fr/ssh_windows.html>

b. Your public key is in a file called `id_rsa.pub`. Attach this file to the email you send to CSE Help.
http://wiki.cse.ucdavis.edu/cgi-bin/index2.pl

The cluster we are signing up for is the Crick/MCB/MMG/BioMath
MCB is sponsoring our account.

###Crick Defaults:
Crick comes with a lot of software preinstalled. Here's a list of some of the most commonly used ones:

* gcc
* Git
* Perl
* Python
* R

####Navigating a unix environment (Useful commands)
ls 				#lists files in the current directory
pwd 			#shows working directory
mkdir testdir	#makes a new directory/folder called testdir
rmdir testdir	#removes directory/folder called testdir; Alternatively use: rm -r testdir
cd /testdir		#go to folder called testdir
cd ~/			#go to default/home folder
cd ..			#go up one folder


####Using vi/vim editor
vi/vim is a popular text editor that is preinstalled into Crick and almost all unix/linux environments. It is worth learning since it is used a lot. 

-Create a shell script called testrun.sh
vim testrun.sh 

To start editing/writing script press 'I' on your keyboard.

When you're ready to save hit 'ESC' on your keyboard and type:

:wq     #to write(save) and quit
:q!     #to quit without saving
ZZ      #shift + Z + Z to save any changes that have been made


#####Submitting Jobs to Crick:

Create a submit script. The submit script is usually in shell script.

The main submit script is preceded by the SLURM header which tells the job manager how to run your script.

The -D tells sbatch which directory to be working from.  -o is where standard out goes, -e is where standard error goes.  -J is the job title. -p is the node(s) you want to submit to. 

More here: <http://wiki.cse.ucdavis.edu/support:hpc:software:slurm>

The example below is an example submit script called testrun.sh:
#!/bin/bash
#SBATCH -D /home/kvdistor/test
#SBATCH -o /home/kvdistor/stout-%j.txt 
#SBATCH -e /home/kvdistor/sterr-%j.txt
#SBATCH -J centcper
#SBATCH -p serial
set -e
set -u

sbatch R CMD BATCH test.r       #Run an R script named test.r

sbatch matlab -nojvm -nodisplay -r "[[pre,post,tstr]=getNames(3,1,1,275,1,5,xystring,1,2,'2015-01-07-mcf7ampkar','c1','c2','c3','c4','tif',[10 50]);" # Run the getNames.m Matlab script

#####To submit a job simply type:

	$ srun testrun.sh #<----- Not working yet
	$ sh testrun.sh #<----- Use this one. **Warming** submits to bigmem. If bigmem crashes due to bad scripting, all the nodes and jobs running underneath it crash.


#####Checking up on jobs:

	$squeue

To check on a big job:

	$top

* Run top and watch for the following to ensure that you don't crash a node:

	- Does the load average spike?

	- Does the %wa (3rd line) spike?

	- Does any process balloon in memory usage? (sort by memory use with M)

	- Does any process saturate at 100% cpu usage? Which?

	- Does swap usage increase?


#####Alias:

Bash has something called aliases, which let you put your own shorter (usually) command to stand for another command. For example, I could create an alias for my thesis program called `graduate` which runs `/home/user/programs/thesis`. I could also create an alias for `ls -a` (show hidden files) called `la`. To do this, you need to create a file called `.bash_profile`

a. Make sure you're in the home directory

		$ cd

b. Create `.bash_profile`

		$ touch .bash_profile

c. Open it up in your favorite text editor
	
		$ nano .bash_profile

d. Create the aliases by typing this in:
	
		alias graduate="/home/user/programs/thesis"
		alias la="ls -a"

e. Save the file:
In nano this is control+O (WriteOut)

f. Reload bash by logging out or with this command:

		$ source ~/.bash_profile

#####Add to path:
When you type in a command into bash, bash looks for a binary file matching that command. The places it looks by default on Crick are: 

	/share/apps/ge-6.2/bin/lx24-amd64
	/usr/kerberos/bin
	/usr/local/bin
	/bin
	/usr/bin

We can shorten the command we have to type by adding the binary file to the path. To do this, we add it to the `.bash_profile`.

a. Make sure you're in the home directory

		$ cd

b. Create `.bash_profile`

		$ touch .bash_profile

c. Open it up in your favorite text editor

		$ nano .bash_profile

d. Add to your path by typing this in:
		
		PATH=$PATH:/home/user/programs/
		export PATH

e. Save the file:
In nano this is control+O (WriteOut)

f. Reload bash by logging out or with this command:

		$ source ~/.bash_profile

This will add all binary files in `/home/user/programs/`including the one we want, `thesis`. To see what is in your path, type this into bash:

	$ echo $PATH

To add more folders to your path, just append it to the line with a colon in the beginning:

	PATH=$PATH:/home/user/programs/:/home/user/who-needs-organization/bin/
	export PATH


#####Downloading a zip:
a. Go to the parent directory of where you want the software installed

		$ cd programs

b. Visit the Github repository and on the very right there is a button that says `Download zip`. Right click this link and click `copy link`. 

c. Back in Crick, download the zip with wget:

		$ wget https://github.com/[user]/[repository]/archive/[branch].zip

d. Unzip this folder with unzip:

		$ unzip [repository]

e. Change to the directory you just created (the name of the repository) and follow the installation instructions that are (hopefully) provided.


#####Modules:

This package allows system administrators to update the system libraries and other core programs without breaking your code or scripts. Modules contain the information that the shell needs to locate programs and libraries without getting confused about different versions and different files. For you, this means that before using something like gcc, you need to load it:

	$ module load gcc
	Module GCC 4.5.0 Loaded.

To see what modules are available, type:

	$ module avail

Any one of these modules can be loaded and should be. The special Crick thing here is that if you run python, for example, without loading the module, you'll run version 2.4.3. However, if you load the python module, you will get 2.7.3. This becomes an issue when you run python from a script but forget to load the module because you'll be running an old version of python. So remember, if you're using a preinstalled program, make sure you load the module. 





======

###Unix computer tips:
This section contains tips and information applicable to any Unix-based computer you use. (Hereafter referred to as Unix).

####Bash:
In order to effectively use the Crick computer, you need to know how to move around in bash. Use this course to learn how: <http://cli.learncodethehardway.org/book/>


####Special Unix things:

#####Screen:
Screen is especially useful for Crick because it lets you run things that take a while without entering a specific node (Be careful not to run things that take hours and lots of memory though). Screen lets you open another session on the Crick which you can detach from. This lets whatever program or script you have running keep running even after you log out. To open a new screen, type:

	$ screen

Now you'll have a new bash session. To detach from that screen, press they keys Control-a, d. To see what screens you have detached, use the -ls option:

	$ screen -ls

This will output a list of existing screens along with a number corresponding to each screen. In order to switch to a particular screen, use the -r option along with that screen's number:

	$ screen -r [number]

To find out more, use the manual page:

	$ man screen
