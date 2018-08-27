# smanage
Slurm Manage, for submitting and reporting on job arrays run on slurm

The script manages jobs running on a compute cluster that uses the SLURM scheduler (https://slurm.schedmd.com/). 
It was developed in bash to take advantage of the automatic output from the slurm programs available on the command line, namely sacct and sbatch. The script has two basic modes described below:

## 1. Reporting (default mode)
The reporting mode parses the output from sacct to create more consise output for large sets of job runs. 
The most useful example is to show the number of jobs in each state. The following is the output from a batch of 1000 jobs submitted using 'sacct --array' named "BATCH_JOBS":

```
$ smanage --report --name BATCH_JOBS
Finding jobs using: /usr/bin/sacct -XP --noheader --name BATCH_JOBS
8 COMPLETED jobs
2 FAILED jobs
0 TIMEOUT jobs
8 RUNNING jobs
982 PENDING jobs
```

When using the report mode, any of the sacct commands (https://slurm.schedmd.com/pdfs/summary.pdf) can be added to generate a report on specific jobs. For example, to report the jobs ran on a specific date (note, on that date 1000 jobs named BATCH_JOBS ran to completion):

```
$ smanage --report --name=BATCH_JOBS --starttime=2018-08-27
Finding jobs using: /usr/bin/sacct -XP --noheader --name BATCH_JOBS
1008 COMPLETED jobs
2 FAILED jobs
0 TIMEOUT jobs
8 RUNNING jobs
982 PENDING jobs
```

Adding the '--verbose' flag adds more useful information about the jobs. When added to the example above and providing the sacct flag to only see COMPLETED jobs, the run time information is added to the output: 


```
$ smanage --report --verbose --name=BATCH_JOBS --starttime=2018-08-27 --state=COMPLETED
Finding jobs using: /usr/bin/sacct -XP --noheader --name BATCH_JOBS
1008 COMPLETED jobs
Avg Run Time: 02:14:15
Avg Wall Time: 07:33:40
```

When looking at FAILED jobs, providing a path to the directory where the .err files are for the run will print the errors for these jobs and a list of jobs to rerun (for easy copy and paste into your next 'sbatch --array' or 'smanage --submit' call):

```
$ smanage --report --verbose --name=BATCH_JOBS --starttime=2018-08-27 --state=FAILED
Finding jobs using: /usr/bin/sacct -XP --noheader --name BATCH_JOBS
2 FAILED jobs
Job 34 Failed: "ls: ~/myjobdir/: No such file or directory"
Job 52 Failed: "ls: ~/myjobdir/: No such file or directory"
Rerun these jobs: 34,52
```

The output for verbose commands can be extended to parse the .err or .out files to provided even more information using the 'SMANAGE_EXT_SOURCE' environment variable.

# 2. Submitting



