# Install-XDMoD-SUPReMM-on-Centos-7
Steps on my installation and configuration of XDMod+SUPReMM, with Slurm

The folloiwng lists the steps that I made to install and configure XDMoD with SUPReMM, with Slurm.

The version of Slurm I use is 2.4.5. The new version seems can not work well with XDMoD for some reason, basically the "sacct" and "xdmod-shredder".

For the installation process on these software packages, I will prefer to build rpm using command "rpmbuild". In this way, one can easily install and erase packages whenever needed.

1) Install Slurm.

  If you need to install Slurm, you can download it from Slurm's official website, and the run the command:
  
  >rpmbuild -ta slurm-2.4.5.tar.bz2 

which will generate the rpm files in $HOME/rpmbuild. Find the right rpm file and run:

> rpm -ivh lurm-2.4.5-1.el7.centos.x86_64.rpm slurm-munge-2.4.5-1.el7.centos.x86_64.rpm slurm-plugins-2.4.5-1.el7.centos.x86_64.rpm

You may need to install other packages if necessary.

Then follow the instructions on Slurm website to configure it.

2) Download XDMoD 5.5, the rpm files, from it's website: https://sourceforge.net/projects/xdmod/files/xdmod/, choose the right OS version.
The rpm files I use are:

xdmod-5.5.0-1.0.el7.centos.noarch.rpm

xdmod-appkernels-5.5.0-1.0.el7.centos.noarch.rpm

xdmod-supremm-5.5.0-0.6.beta1.el7.centos.noarch.rpm

Then, follow the instruction on it's website to install and configure.

3) Install and configure SUPReMM.

For some reason, the rpm file of supremm-0.9.0-1.x86_64.rpm (https://sourceforge.net/projects/xdmod/files/supremm/0.9.0/) does not work for me. 
So I went to Github(https://github.com/ubccr/supremm) and checked out the most recent version.

Once you have downloaded the source package of SUPReMM, unzip it and then go to it's folder, check to see if you need to update it's 
config file of "config/config.json"(or you can update it after you have installed, which should be found at "/etc/supremm/config.json"),
then run:

>python setup.py bdist_rpm

Once it's done, you can find the rpm file in "dist/" folder, like "dist/supremm-0.9.1-1.el7.centos.x86_64.rpm".

Run rpm command to install it:

>rpm -ivh dist/supremm-0.9.1-1.el7.centos.x86_64.rpm

4) Install and configure PCP(Performance Co-Pilot,http://pcp.io/).

Follow the instructions on http://xdmod.sourceforge.net/supremm-compute-pcp.html . Please NOTE, you need PCP version newer than 3.1.8. 
CentOS 7 has PCP version 3.10.6, but you can download the source file to compile and install or to download it's rpm files directly: https://bintray.com/pcp/el7/pcp/3.10.8/#files.

The procedure to compile the PCP source codes is not difficult. The version of the source file is 3.10.9: https://bintray.com/pcp/source/pcp/view

Once you have downloaded it, you can run command:

>rpmbuild -ta pcp-3.10.9.src.tar.gz 

After the compilation is done, you can find the rpm files in $HOME/rpmbuid. Then follow the instructions on http://xdmod.sourceforge.net/supremm-compute-pcp.html.


You will also need to configure the SUPReMM Open XDMoD module, and insatll mongodb, which can be followed here: http://xdmod.sourceforge.net/supremm-overview.html

Also check the page http://xdmod.sourceforge.net/configuration.html for instructions.


That's it!


To transfer your Slurm job info to XDMoD, you can also run commands manually:

>TZ=UTC sacct --allusers --allclusters   --parsable2 --noheader --allocations  --format jobid,jobid,cluster,partition,account,group,gid,user,uid,submit,eligible,start,end,elapsed,exitcode,state,nnodes,ncpus,reqcpus,MaxRSS,timelimit,nodelist,jobname   --state CANCELLED,COMPLETED,FAILED,NODE_FAIL,PREEMPTED,TIMEOUT   --starttime 2013-01-01T00:00:00 --endtime 2016-04-01T23:59:59    >/tmp/slurm.log
 
 >xdmod-shredder -r 83-161 -f slurm -i /tmp/slurm.log
 
 >xdmod-ingestor
 
 >aggregate_supremm.sh
 
 and  optially:
 
>supremm_update

>summarize_jobs.py

 Please be aware, the output of Slurm's "sacct" does not match XDMoD very well, you might need to manually adjust it.
 
 To my experience, the most problems for using XDMoD/SUPReMM are the database issues and slight differences of the format of job info between Slurm and XDMod.
 
 
 


