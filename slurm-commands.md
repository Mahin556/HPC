### scontrol

- General
  ```
  scontrol show jobs
  scontrol show nodes
  scontrol show partitions
  scontrol show config #show slurm config
  ```

- View job information
  ```
  scontrol show job <job_id>
  ```
  Displays full info: job name, state, nodes, CPUs, memory, time limits, dependencies, etc.
  Options:
  -d → Detailed output
  -o → Output in a single line

- Modify a job
  ```
  scontrol update JobId=<job_id> [Field=Value ...]
  ```
  - Change priority:
    ```
    scontrol update JobId=12345 Priority=1000
    ```
  - Change time limit:
    ```
    scontrol update JobId=12345 TimeLimit=02:00:00
    ```
  
- Hold / Release jobs
  - Hold a job (prevents it from running):
    ```
    scontrol hold <job_id>
    ```
  - Release a held job:
    ```
    scontrol release <job_id>
    ```

- Requeue / Cancel jobs
  - Requeue a job (put back in the queue):
    ```
    scontrol requeue <job_id>
    ```
  - Requeue failed jobs:
    ```
    scontrol requeue <job_id> Force
    ```
  - Cancel a job:
    ```
    scontrol cancel <job_id>
    ```

- Suspend / Resume jobs
  - Suspend a running job:
    ```
    scontrol suspend <job_id>
    ```
  - Resume a suspended job:
    ```
    scontrol resume <job_id>
    ```

    
- View node information
  ```
  scontrol show node <node_name>
  ```
  Displays state, CPUs, memory, partitions, jobs running, etc.
  Options:
  -d → Detailed
  -o → One-line output

- Update node state
  ```
  scontrol update NodeName=<node_name> State=<state> [Reason="text"]
  scontrol update NodeName=node01 State=DOWN Reason="hardware failure"
  scontrol update NodeName=node01 State=RESUME
  scontrol update NodeName=<node> State=DRAIN Reason="maintenance"
  ```
  Common states:
    DOWN → Node is unavailable
    DRAIN → Node will finish current jobs, no new jobs
    RESUME → Bring node back online
    FAIL → Mark node as failed

- View partition information
  ```
  scontrol show partition <partition_name>
  ```

- Update partition
  ```
  scontrol update PartitionName=<partition> [Field=Value ...]
  ```
  - Change max time:
    ```
    scontrol update PartitionName=debug MaxTime=01:00:00
    ```
  - Enable/disable partition:
    ```
    scontrol update PartitionName=debug State=UP
    scontrol update PartitionName=debug State=DOWN
    ```

- Reservation
  - View all active reservations:
    ```
    scontrol show reservation
    ```
  - Show a specific reservation:
    ```
    scontrol show reservation <reservation_name>
    ```
  - Show reservations by user:
    ```
    scontrol show reservation | grep UserId=<username>
    ```
  - Create Reservations
    ```
    scontrol create reservation <parameters>
    ```
    Common parameters:
    - ReservationName=<name> → Name of reservation (default: Resv_JobID_xxx)
    - StartTime=<time> → When reservation begins (now, YYYY-MM-DD[Thh:mm:ss])
    - Duration=<minutes | D-HH:MM:SS> → Length of reservation
    - EndTime=<time> → Alternative to Duration
    - Nodes=<node_list> → Nodes included (node[01-04], ALL)
    - CoreCnt=<count> → Number of cores (instead of full nodes)
    - Features=<features> → Restrict to nodes with specific features
    - Users=<user_list> → Allowed users (comma separated)
    - Accounts=<account_list> → Allowed accounts
    - Groups=<group_list> → Allowed Unix groups
    - Licenses=<license_list> → Reserve licenses
    - Flags=<options> → Special behavior flags
      
  - Example 1: Reserve 4 nodes for user alice
    ```
    scontrol create reservation ReservationName=alice_res StartTime=now Duration=02:00:00 Nodes=node[01-04] Users=alice
    ```
  - Example 2: Reserve a partition for a workshop(users)
    ```
    scontrol create reservation ReservationName=workshop StartTime=2025-09-28T10:00:00 Duration=4:00:00 PartitionName=training Users=user1,user2,user3
    ```
  - Example 3: Reserve cores instead of nodes
    ```
    scontrol create reservation ReservationName=test_res Duration=1:00:00 StartTime=now CoreCnt=32 Users=bob
    ```

  - Update Reservations
    - Modify an existing reservation:
      ```
      scontrol update reservation ReservationName=<name> <parameters>
      ```
    - Extend the duration:
      ```
      scontrol update reservation ReservationName=alice_res Duration=3:00:00
      ```
    - Add another user:
      ```
      scontrol update reservation ReservationName=alice_res Users=alice,bob
      ```
    - Add more nodes:
      ```
      scontrol update reservation ReservationName=alice_res Nodes=node[01-06]
      ```
  - Delete Reservations
    - Cancel a reservation:
      ```
      scontrol delete reservation ReservationName=<name>
      scontrol delete reservation ReservationName=workshop
      ```

  - Flags for Reservations
    - Reservations support special flags via:
      ```
      scontrol create reservation ... Flags=<flag>
      ```
      Common flags:
        MAINT → Maintenance reservation
        DAILY → Recurring daily reservation
        WEEKLY → Recurring weekly reservation
        STATIC_ALLOC → Prevent jobs from oversubscribing reserved CPUs
        IGNORE_JOBS → Create reservation even if jobs are already running
        ANY_NODES → Allow allocation of any available nodes (not strict)
        TIME_FLOAT → Allow flexible start within the reservation window

  - Jobs & Reservations
    - When submitting a job, you can bind it to a reservation:
      ```
      sbatch --reservation=<ReservationName> job.sh
      sbatch --reservation=alice_res -N2 -t 01:00:00 job.sh
      srun --reservation=workshop -n8 --time=2:00:00 --pty bash
      ```
      
