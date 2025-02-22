[![Review Assignment Due Date](https://classroom.github.com/assets/deadline-readme-button-22041afd0340ce965d47ae6ef1cefeee28c7c493a6346c4f15d667ab976d596c.svg)](https://classroom.github.com/a/IAASVEAZ)
# CSIT5970 Assignment-1: EC2 Measurement (2 questions, 4 marks)

### Deadline: 11:59PM, Feb, 28, Friday

---

### Name: WU, Yung-syuan
### Student Id: 21071014
### Email: yswuab@connect.ust.hk

---

## Question 1: Measure the EC2 CPU and Memory performance

1. (1 mark) Report the name of measurement tool used in your measurements (you are free to choose *any* open source measurement software as long as it can measure CPU and memory performance). Please describe your configuration of the measurement tool, and explain why you set such a value for each parameter. Explain what the values obtained from measurement results represent (e.g., the value of your measurement result can be the execution time for a scientific computing task, a score given by the measurement tools or something else).

    - name of measurement tool: sysbench
      ```shell
      sudo apt-get update
      sudo apt-get install sysbench
      ``` 
    - commands and parameters used
      ```shell
      sysbench --test=cpu --threads=32 run
      sysbench memory --memory-oper=write --threads=32 run
      ```
      We set the number of threads to 32 to account for multithreading performance.  
    - Measurements
      - For the CPU test, SysBench performs a series of prime number calculations using integer arithmetic. We look at the number of primes calculated per second as the benchmark.
      - For the memory test, SysBench performs a series of memory operations to measure bandwidth and latency. We look at the number of bytes transferred per second as the benchmark.
      
2. (1 mark) Run your measurement tool on general purpose `t2.micro`, `t2.medium`, and `c5d.large` Linux instances, respectively, and find the performance differences among these instances. Launch all the instances in the **US East (N. Virginia)** region. Does the performance of EC2 instances increase commensurate with the increase of the number of vCPUs and memory resource?


    Region: US East (N. Virginia). Use `Ubuntu Server 22.04 LTS (HVM)` as AMI.
    | Size        | CPU performance(events per second) | Memory performance (MiB/sec)|
    | ----------- | ------------------------------     | --------------------------- |
    | `t2.micro`  |                      2309.35       |     535.71                  |
    | `t2.medium` |                      4444.00       |     896.86                  |
    | `c5d.large` |                      1803.52       |     7590.38                 |
    - commands used
 
      ```sh
      sudo apt-get update
      sudo apt-get install iperf
      iperf -s -w 256k
      iperf -c 172.31.38.167 -w 256k #ip of server
      ping 172.31.38.167
      ```
      
    We see that the cpu speed increases from `t2.micro` to `t2.medium`, but decreased at `c5d.large`.  So cpu performance does not nessecarily increase with the number of vCPUs.
    We see that the memory speed increases across the 3 instances.

## Question 2: Measure the EC2 Network performance

1. (1 mark) The metrics of network performance include **TCP bandwidth** and **round-trip time (RTT)**. Within the same region, what network performance is experienced between instances of the same type and different types? In order to answer this question, you need to complete the following table.

    | Type                      | TCP b/w (Gbits/sec)  | RTT (ms) |
    | ------------------------- | --------------       | -------- |
    | `t3.medium` - `t3.medium` | 3.81                 |  0.296   |
    | `m5.large` - `m5.large`   | 4.97                 |  0.140   |
    | `c5n.large` - `c5n.large` | 4.96                 |  0.130   |
    | `t3.medium` - `c5n.large` | 2.06                 |  0.799   |
    | `m5.large` - `c5n.large`  | 2.94                 |  0.588   |
    | `m5.large` - `t3.medium`  | 4.51                 |  0.159   |

    We see that instances of the same type all have good bandwidth above 3.8 Gb/s. For different instance types, the bandwidth an decrease, this happened between c5n.large and t3.med. Good bandwidth generally corresponds to less RTT.

2. (1 mark) What about the network performance for instances deployed in different regions? In order to answer this question, you need to complete the following table.

    | Connection                | TCP b/w (Gbits/sec)  | RTT (ms) |
    | ------------------------- | --------------       | -------- |
    | N. Virginia - Oregon      |  0.0147              |61.6      |
    | N. Virginia - N. Virginia |  1.01                |0.369     |
    | Oregon - Oregon           |  1.01                |0.454     |
 
    I used 
    ```txt
    curl ifconfig.me
    ```
    to find the public ip.  
    We see that across different data centers, the bandwidth and RTT is worse compared to intra data center.