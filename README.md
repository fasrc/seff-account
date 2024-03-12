# seff-account

A modification of [seff-array](https://github.com/ycrc/seff-array) by Yale Center for Research Computing which is an extension of the Slurm command 'seff' designed to handle summarizing job usage for users and accounts over a period of time displays the information in a histogram.       

seff-account generates three types of histograms: 

    1. CPU Efficiency (utilization vs runtime)
    1. Maximum memory usage versus the requested memory
    2. Runtime of each job compared to the requested wall-time

## Usage:

    seff-account [-a account] [-c cluster] [-u username] [-S start time] [-E end time]

If run on a cluster that shares a single Slurm database, you can pass the name of the alternate cluster via `-c cluster`.  The `SLURM_CLUSTER_NAME` env-var is checked and passed to `sacct` if present. 

Start time and end time are formatted the same as the [sacct](https://slurm.schedmd.com/sacct.html) command. By default it looks over the last day.
