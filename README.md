# pscache-data

This Github site contains the artifact and all experimental results of the submission titled "Partial Solution Based Constraint Solving Cache in Symbolic Execution" in ASE 2023. We provide this repository to assist the reviewers in evaluating our work.

The experimental results are provided in the form of tar packages, which are also included in the artifact along with data analysis scripts. The artifact is a Docker image that contains executables, benchmarks, raw data and running scripts of our submission. The reviewers can follow the steps below to install the docker image and try our work.

## Install docker-engine
Before attempting to reproduce the experimental results, please ensure that you have installed the Docker. If you have not done so, please refer to [this site](https://docs.docker.com/get-docker/) for assistance. Our Docker image has been tested on a **Linux** host operating system.

## Load the Docker image
First, you should pull the Docker image from hub:
```bash
$ docker pull pscache/pscache-ase:latest
```
If the image is loaded successfully, you can use the following command to verify it. You should see that an image named `pscache/pscache-ase` exists.
```bash
$ docker images
REPOSITORY            TAG       IMAGE ID         CREATED          SIZE
pscache/pscache-ase   latest    <container_id>   1 minutes ago    4.81GB
```
Then, you can start to run the container in interactive mode.
```bash
$ docker run -it pscache/pscache-ase:latest /bin/bash
```
Now, you are inside the Docker image. We provide some useful commands here. You can find more useful [tutorials](https://docs.docker.com/) in the official site.

**Notes:**

when inside the container,

(1) to exit the docker container and terminate all running jobs inside the container, use:
```bash
$ exit
```
(2) to turn interactive mode into daemon mode (detach from the running container without stopping it),
```bash
press CTRL+P followed by CTRL+Q
```

when outside the container and looking to attach to it, use:
```bash
$ sudo docker attach <container_id>
```

## Experimental environment
Once inside the container, navigate to the /cache directory and list its contents. Then, the experimental environments of three experiments in our submission are shown.
```bash
$ cd /cache && ls
experiment1  experiment2  experiment3  klee-uclibc
```
Next, we will guide the reviewers to view the detailed results of our experiments, which are computed by the data analysis script inside each environment.

Take the first experiment as an example, navigate to the directory containing the compression package of its raw data and decompress the package.
```bash
$ cd ./experiment1/raw_data && tar xf exp1-data.tar.gz && ls
exp1-data exp1-data.tar.gz
```
The data analysis script `analyze.py` is located inside `exp1-data`, and each directory corresponds to the raw data of one combination of parameters Kp and Ks. For example, `150_100` indicates that the value of Kp is 150 and that of Ks is 50.
```bash
$ cd exp1-data && ls
150_100  150_150  150_50  300_100  300_150  300_50  analyze.py
```
The script incorporates many options. We recommend that the reviewers focus on the `-t` option, which displays a visually appealing ASCII table (`PrettyTable`) of the detailed results. Other options generate the latex code of figures in the submission. For example, if you are interested in the results of `150_100`, you can invoke the script in the following way. The detailed results (average value of five runs) under three search strategies will be displayed.
```bash
$ ./analyze.py -t 150_100/*
+--------------------------------------------------+
|               Statistics under dfs               |
+--------------------------------------------------+
...
+--------------------------------------------------+
|               Statistics under bfs               |
+--------------------------------------------------+
...
+--------------------------------------------------+
|               Statistics under rcn               |
+--------------------------------------------------+
...
```
The detailed results of the other two experiments also can be shown through `-t` option.
```bash
  # Experiment 2 
$ cd /cache/experiment2/raw_data && tar xf exp2-data.tar.gz && cd exp2-data
$ ./analyze.py -t vanilla/* ps_2000_2000/*
  # Experiment 3
$ cd /cache/experiment3/raw_data && tar xf exp3-data.tar.gz && cd jpf_results
$ ./analyze.py -t 50_100/*
```

## Reproduce the experimental results

We can use this docker image to repoduce the experimental results presented in the submission. Our experiments were performed on a server with **Intel(R) Xeon(R) Platinum 8269CY CPU @ 2.50GHz** and the operating system is **Ubuntu 20.04**. To reproduce the results, a machine with similar **CPUs(~2.50GHz)** is required. Running the artifact on a different machine could possibly diverge the execution and lead to different results.

### Experiment 1
Navigate to `/cache/experiment1/scripts` and invoke the running script.
```bash
$ cd /cache/experiment1/scripts
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
```
The `run.py` script will automatically detect the number of cores in the current working machine, set the number of programs in parallel to a half, and perform the experiment on each benchmark program for 1,800 seconds (30 min).

### Experiment 2
Similarly, navigate to `/cache/experiment1/scripts` and invoke the running script.
```bash
$ cd /cache/experiment2/scripts
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
$ ./run.py # wait...
```

### Experiment 3

Navigate to `/cache/experiment3/jpf-cache/jpf-symbc` and invoke `run-exp.sh`, which is a shell script.
```bash
$ cd /cache/experiment3/jpf-cache/jpf-symbc
$ ./run-exp.sh exp-1 8 # wait...
$ ./run-exp.sh exp-2 8 # wait...
$ ./run-exp.sh exp-3 8 # wait...
$ ./run-exp.sh exp-4 8 # wait...
$ ./run-exp.sh exp-5 8 # wait...
```
The first argument of `run-exp.sh` is the output directory, and the second argument is the number of parallel tasks. In the above his case, we are analyzing 8 benchmark programs simultaneously.