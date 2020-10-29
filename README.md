## Project #2: Simulating Processor Schedulers

### *** Due on 24:00, November 13 (Friday) ***


### Goal

We have learned various process scheduling policies and examined their properties in the class.
To better understand them, you will implement SJF, SRTF, round-robin, priority, priority + PIP, and priority + PCP scheduling policies on an educational scheduler simulating framework, which imitates the scheduler core of modern operating systems.


### Problem Specification

- The framework maintains the time with `ticks` variable. It is increased by 1 when a scheduling is happened. You may read this varible but should not modify it.

- Firstly, we need a schedulable entity, and it is the process. The framework accepts a process description file as the argument, which describes the processes to simulate. Following example shows an example description file for two processes (process 1 and process 2).

	```
	process 1
		start 0
		lifespan 4
		prio 0
	end

	process 2
		start 5
		lifespan 10
		prio 10
	end
	```

- The framework will start the process 1 at tick 0 (`start 0`) and run it for 4 ticks (`lifespan 4`). The process will be given a priority value 0 by default, and you may specify the priority with `prio` property (`prio 0`). The larger priority value implies the higher priority of processes. Likewise, process 2 will be kicked in at time tick 5 and run for 10 ticks with priority 10. This information is also shown at the beginning of the program execution as follow.
	```
	- Process 1: Forked at tick 0 and run for 4 ticks with initial priority 0
	- Process 2: Forked at tick 5 and run for 10 ticks with initial priority 10
	```

- The framework will realize the processes using `struct process` defined in `process.h`. See the file for the fields that describes processes in the system. Note that some variables are forbidden to access.

- At any moment, `struct process *current` defined as a global variable points to the process that is currently running. You can use the variable as you need to access the currently running process.

- The framework only implements scheduling mechanisms (e.g., replacing the current, counting ticks, ... ), and it interacts with scheduling *policies* that are defined with `struct scheduler` in `sched.h`. `struct scheduler` is a collection of function pointers. The framework will call the functions to ask the scheduling policy for making decisions. Have a look at `fifo_scheduler` in `sched.c` which implements a FIFO scheduler. You may also find other `scheduler` instances in `sched.c` that are waiting for your implementation.

- `struct process *(*schedule)(void)` is the key function for the scheduling policy. The framework invokes the function whenever it needs a process to schedule next. The function should return a process to run next or NULL to indicate there is no process to run. See `fifo_schedule()` in `sched.c`.

- The framework has the ready queue `struct list_head readyqueue` which is supposed to keep the list of processes that are ready to run. Note that the current process *SHOULD NOT* be in the ready queue.

- The system has a number of system resources (32 in this PA) that can be assigned to processes *exclusively*. `struct resource` defines the system resources in `resource.h`. The process may ask the framework to acquire a resoruce and release it after use. Such a resource use is specified in the process description file using `acquire` property. For example, `acquire 1 4 2` means the process will require resource #1 for 2 ticks when it gets aged for 2 ticks. Have a look at `testcases/resources` for an example.

- When the framework gets the resource acquisition request, it calls `acquire()` function of the scheduler. Similarly, the framework calls `release()` function when the process releases a resource. You may find default FCFS acquire/release functions in `sched.c` and the FIFO scheduler uses them to allocate resources.

- Non-priority-based scheduling policies should handle resource acquision requests in a first-come-first-served way. On the other hand, priority-based scheduling policies should dispatch the releasing resource to the process with the highest priority. To this end, you may define your own acquire/release functions and associate them to your scheduler implementation to make a correct scheduling decision.

- The framework is waiting for your implementation of shortest-job first (SJF) scheduler, shortest-remaining time first (SRTF) scheduler, round-robin scheduler, priority-based scheduler, priority-based scheduler with priority ceiling protocol (PCP), and priority-based scheduler with priority inheritance protocol (PIP). You can start the program with a scheduler option and the framework will select the corresponding scheduler automatically. Check the options by running the program (`sched`) without any option.

- When a process is created by the framework, the `forked()` callback function will be invoked. Similarly, when the process is done, `exiting()` callback function is called.

- For round-robin scheduler, you don't need to worry about managing the time quantum; the framework will automaticall call the `schedule()` function whenever the time quantum expires.

- The priority-based schedulers should handle processes with the same priority in the round-robin way; If two or more processes are with the same priority, they should be swiched on each tick.

- To boost the priority of processes in PCP, use `MAX_PRIO` defined in `process.h`.

- When you implement PIP, make sure that the priority of a process is set properly when it releases a resource. There are complicated cases to implement PIP.
	- More than one processes with different priority values can wait for the releasing resource. Suppose one process is holding one resource type, and other process is to acquire the same resource type. And then, another process with higher (or lower) priority is to acquire the resource type again, and then ...
	- Many processes with different priority values are waiting for different resources held by a process.
	You will get the full points for PIP *if and only if* these cases are all handled properly. Hint: calculate the *current* priority of the releasing process by checking resource acquitision status.


### Tips and Restriction

- The grading system only examines the messages printed out to `stderr`. Thus, you can use `printf` as you want.
- Use `dump_status()` function to see the situation.
- Recall PA0 for manipulating the `list_head` queues.
- Make sure you are using `list_for_*_safe` variants if an entry is removed from the list during the iteration, and `list_del_init` to remove an entry from the list. (Do some Internet search for their differences)
- Do not forge the result using fprintf.


### Submission / Grading

- Use [PAsubmit](https://sslab.ajou.ac.kr/pasubmit) for submission
	- 450 pts + 10 pts 
	- Some testcases are hidden and only show the final decision (i.e., pass/fail);

- Code: ***sched.c*** (400 pts)
	- SJF scheduler: 20pts (tested using `multi`)
	- SRTF scheduler: 50pts (`multi`);
	- RR scheduler:  50pts (`multi` and `prio`)
	- Priority scheduler: 60pts (`prio` and `resources-basic`)
	- Priority scheduler + PCP: 70pts (`resources-basic`)
	- Priority scheduler + PIP: 150pts (`resources-adv1` and `resources-adv2`)

- Document: One PDF document (50 pts) including;
	- Description how **each** scheduling policy is implemented
	  - No need to explain the code itself. Instead, try to explain your idea and approach.
	  - Please, do not put screenshots of your code.
	- How the priorities of processes are changed over time for PCP and PIP. Use `resources-basic` for PCP and `resources-adv2` for PIP.
	- Lesson learned
	  - No need recite what is explained in the class.
	- No more than five pages

- Git repository (10 pts)
	- Register http URL and with a deploy token and password.
	- Start the repository by cloning this repository.
	- Make sure the token is valid through November 18 (due + 4 slip days + 1 day)

- *WILL NOT ANSWER THE QUESTIONS ABOUT THOSE ALREADY SPECIFIED ON THE HANDOUT.*
- *QUESTIONS OVER EMAIL WILL BE IGNORED UNLESS IT CONCERNS YOUR PRIVACY.*
