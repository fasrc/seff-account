# seff-account

A modification of [seff-array](https://github.com/ycrc/seff-array) by Yale Center for Research Computing which is an extension of the Slurm command 'seff' designed to handle summarizing job usage for users and accounts over a period of time displays the information in a histogram. Note that if the user does not belong to the relevant account no data will be returned unless that user is an Operator or Administrator in the slurm database.

seff-account generates three types of histograms: 

    1. CPU Efficiency (utilization vs runtime)
    1. Maximum memory usage versus the requested memory
    2. Runtime of each job compared to the requested wall-time

## Usage:

    seff-account [-A account] [-c cluster] [-u username] [-S start time] [-E end time]

If run on a cluster that shares a single Slurm database, you can pass the name of the alternate cluster via `-c cluster`.  The `SLURM_CLUSTER_NAME` env-var is checked and passed to `sacct` if present. 

Start time and end time are formatted the same as the [sacct](https://slurm.schedmd.com/sacct.html) command. By default it looks over the last day.

## Understanding the output

Below is an example of `jharvard_lab` from start time September 15th, 2024 at 00:00:00 (midnight) to end time September 20th, 2024 at 00:00:00.

```
[jharvard@holylogin07 ~]$ seff-account -A jharvard_lab -S 2024-09-15T00:00:00 -E 2024-09-20T00:00:00
--------------------------------------------------------
Job Information
Names: unzip_systems.sh, interactive, process_plinder.sh, vscode, dummy4XDMod, .fasrcood/sys/dashboard/sys/Jupyter, install_env.sh
Cluster: odyssey
Users: user1, user2, user3, user4, user5, user6
Account: jharvard_lab
Start Time: 2024-09-15T00:00:00
End Time: 2024-09-20T00:00:00
Average Requested CPUs: 1.58 cores on 1.00 node(s)
Average Requested Memory: 18.82G
Average Requested Time: 16038.46s
--------------------------------------------------------
Job Status
CANCELLED by 98765: 1
COMPLETED: 8
FAILED: 5
OUT_OF_MEMORY: 1
TIMEOUT: 11
--------------------------------------------------------
--------------------------------------------------------
Job Statistics
Total Number of Jobs: 26
Average CPU Efficiency 7.98%
Average Memory Usage 2.87G
Average Run-time 9187.96s
---------------------

CPU Efficiency (%)
---------------------
+0.00e+00 - +1.00e+01  [18]  ████████████████████████████████████████
+1.00e+01 - +2.00e+01  [ 5]  ███████████▏
+2.00e+01 - +3.00e+01  [ 1]  ██▎
+3.00e+01 - +4.00e+01  [ 1]  ██▎
+4.00e+01 - +5.00e+01  [ 0]
+5.00e+01 - +6.00e+01  [ 1]  ██▎
+6.00e+01 - +7.00e+01  [ 0]
+7.00e+01 - +8.00e+01  [ 0]
+8.00e+01 - +9.00e+01  [ 0]
+9.00e+01 - +1.00e+02  [ 0]

Memory Efficiency (%)
---------------------
+0.00e+00 - +1.00e+01  [18]  ████████████████████████████████████████
+1.00e+01 - +2.00e+01  [ 1]  ██▎
+2.00e+01 - +3.00e+01  [ 2]  ████▌
+3.00e+01 - +4.00e+01  [ 1]  ██▎
+4.00e+01 - +5.00e+01  [ 1]  ██▎
+5.00e+01 - +6.00e+01  [ 0]
+6.00e+01 - +7.00e+01  [ 1]  ██▎
+7.00e+01 - +8.00e+01  [ 1]  ██▎
+8.00e+01 - +9.00e+01  [ 0]
+9.00e+01 - +1.00e+02  [ 1]  ██▎

Time Efficiency (%)
---------------------
+0.00e+00 - +1.00e+01  [11]  ████████████████████████████████████████
+1.00e+01 - +2.00e+01  [ 1]  ███▋
+2.00e+01 - +3.00e+01  [ 1]  ███▋
+3.00e+01 - +4.00e+01  [ 0]
+4.00e+01 - +5.00e+01  [ 0]
+5.00e+01 - +6.00e+01  [ 1]  ███▋
+6.00e+01 - +7.00e+01  [ 0]
+7.00e+01 - +8.00e+01  [ 0]
+8.00e+01 - +9.00e+01  [ 1]  ███▋
+9.00e+01 - +1.00e+02  [11]  ████████████████████████████████████████
--------------------------------------------------------
```

- **Job Information:** summary of all jobs
  - Names: job names
  - Users: users from jharvard_lab that ran jobs between start time and end time
  - Account: slurm fairshare account/PI lab that was queried
  - Start Time: start time for the `seff-account` analysis
  - End time: end time for the `seff-account` analysis
  - Average [field]: average of all jobs between Start and End times
- **Job Status:** indicates the total number of jobs on each state between Start and End Time
  - CANCELLED by 98765: total of job cancelled by user 98765 (you can check which `uid` is yours with the command `id`) 
- **Finished Job Statistics:** summary of all finished jobs. This includes completed, failed, out_of_memory, timeout, and preempted jobs. It excludes pending, running, and cancelled jobs.
- **CPU Efficiency (%)**: horizontal histogram representing the number of jobs that falls within that CPU % efficiency
- **Memory Efficiency (%)**: horizontal histogram representing the number of jobs that falls within that memory % efficiency
- **Time Efficiency (%)**: horizontal histogram representing the number of jobs that falls within that time % efficiency

### Reading the histogram

Keep in mind that plotting a graph on the command line is not an easy task.

Each line represents a bin on a histogram. First line is from 0 to 10% CPU efficiency, second line is from 10 to 20% CPU efficiency, third line is from 20 to 30% CPU efficiency, and so on.

In this example, we can see that most jobs (18 out of 26) had a CPU efficiency of 10%. Thus, users in jharvard_lab, should investigate which jobs are not CPU efficient and then look into improving their CPU efficiency.


```
+0.00e+00 - +1.00e+01  [18]  ████████████████████████████████████████
+1.00e+01 - +2.00e+01  [ 5]  ███████████▏
+2.00e+01 - +3.00e+01  [ 1]  ██▎
+3.00e+01 - +4.00e+01  [ 1]  ██▎
+4.00e+01 - +5.00e+01  [ 0]
+5.00e+01 - +6.00e+01  [ 1]  ██▎
+6.00e+01 - +7.00e+01  [ 0]
+7.00e+01 - +8.00e+01  [ 0]
+8.00e+01 - +9.00e+01  [ 0]
+9.00e+01 - +1.00e+02  [ 0]
```

## Improving your jobs

Ideally, most jobs would fall in to 90 to 100% CPU, memory, and time efficiency (last line of a histogram). For more information on improving your jobs, see [Job Efficiency and Optimization Best Practices](https://docs.rc.fas.harvard.edu/kb/job-efficiency-and-optimization-best-practices/)
