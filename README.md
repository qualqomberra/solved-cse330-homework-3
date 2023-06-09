Download Link: https://assignmentchef.com/product/solved-cse330-homework-3
<br>
For this project you are to write routines that perform standard queuing functions. The functions work on multiple queues, and structure each queue as a doubly linked, circular list.

<strong>Data Structures:</strong>

A queue consists of a head-pointer and a set of q-elements.

A q-element is a structure, consisting of a prev and next pointer, and a payload consisting of 1 integer. The header is a pointer to the first element of the queue<em>. The</em> <em>head pointer is “null” if the q is empty.</em>

<strong>Functions</strong>:

The functions that you implement are:

<ol>

 <li><strong>item = NewItem</strong>(); // returns a pointer to a new q-element</li>

 <li><strong>InitQueue</strong>( &amp;head) // creates a empty queue, pointed to by the variable head.</li>

 <li><strong>AddQueue</strong>(&amp;head, item) // adds a queue item, pointed to by “item”, to the queue pointed to by head.</li>

 <li>item = <strong>DelQueue</strong>(&amp;head) // deletes an item from head and returns a pointer to the deleted item</li>

 <li><strong>RotateQ</strong>(&amp;head) // Moves the header pointer to the next element in the queue. This is equivalent to AddQ(&amp;head, DeleteQ(&amp;head)), but is simpler to use and more efficient to implement.</li>

</ol>

<strong>Note</strong>: All the routines work on pointers. They do<strong> <em>not</em> </strong>copy q-elements. Also they to not allocate/deallocate space (except NewItem()). You may choose to implement a <strong>FreeItem</strong>(item) function – optional.

<strong>Implementation and Testing</strong>

The routines should be implemented in C under the Linux operating system. If you are not familiar with Linux and/or do not have it installed. Please use a Ubuntu Virtual Machine. For more details/questions post requests on the discussion board.

All the above routines and data structures are to be placed in 1 file, called “q.h”. Do not include other files into this file. The test routines can be in “proj-1.c” and this will include q.h and other standard header files.

Write test routines that thoroughly test the queue implementation. Use multiple queues. Pay special attention to deleting the last element of a q. Also make sure “RotateQ” is behaves properly (strange behavior of this routine means the insert/delete routines have bugs.)

Further warning: Bugs in the Q routines have been the #1 cause for strange errors in the project, always. Careful that you get it right, else things will go bump later.

Part 2: <strong>(This part of the project is graded.)</strong>

<strong>Overview</strong>:

Using the queuing routines (not all will be used) this project will implement the ability to run multiple functions as threads, using non-preemptive scheduling.

<strong>Step 1: TCB and context</strong>:

The queue items defined in your q.h file has to changed, to be of type TCB_t. The TCB_t and an initialization routine are provided in a header file <a href="../Documents/CSE430Notes/430-assignments/tcb.h">tcb.h</a>. Note that TCB_t has previous and next pointers along with an ucontext_t field to store a thread context.

There is a routine in the tcb.h file called init_TCB, which is used to initialize a TCB for a new thread. The arguments to init_TCB are:

<ol>

 <li>pointer to the function, to be executed</li>

 <li>pointer to the thread stack</li>

 <li>size of the stack</li>

</ol>

Step 1 consists of understanding tcb.h and changing the q-element type in your q.h file to TCB_t

<strong>Step 2:</strong>

Write a routine called start_thread and put it in a file called “<strong>threads.h”.</strong> Also routines defined in Step 3 will go into this file. You will need to include q.h into threads.h. The code for start_thread looks like:

<strong>void start_thread(void (*function)(void))</strong>

{ // begin pseudo code

allocate a stack (via malloc) of a certain size (choose 8192)

allocate a TCB (via malloc)

call init_TCB with appropriate arguments

call addQ to add this TCB into the “RunQ” which is a global header pointer

//end pseudo code

}

<strong>Step 3:</strong>

Write the routines called “yield” and “run” which cranks up the works (and put them in threads.h). These routines are defined as:

<strong>void run()</strong>

{        // real code

ucontext_t parent;             // get a place to store the main context, for

faking

getcontext(&amp;parent);        // magic sauce

swapcontext(&amp;parent, &amp;(RunQ-&gt;conext));     // start the first thread

}

<strong>void yield() // similar to run</strong>

{

rotate the run Q;

swap the context, from previous thread to the thread pointed to by RunQ

}

<strong>Step 4:</strong>

Write a driver program in a file called thread_test.c. Into thread_test.c.c include threads.h which includes q.h, which includes TCB.h, which includes ucontext.h.

Declare a global RunQ.

Write N functions with while loops (put a yield in each loop) counting the number of times each function is executed. There should be one global variable. Each function will have a local variable. Each function should increment the local variable by 1 and the global variable by the thread ID. If there were three functions in three threads (one in each thread), the first function will increment the global variable by 1 and the local variable by 1, the second function should increment the global variable by 2 and local variable by 1, the third function will increment the global variable by 3 and the local variable by 1. Each function should print the following before yielding

<em>printf(“This is %d th execution of thread X with global var value % d”, local variable, global variable)</em>

<em>Where X can be 1 2 and 3 depending on the thread ID</em>

<strong>Test cases</strong>

Each test case will have two numbers of the form

A,B

Here A is the total number of threads and B is the number of times each thread should be executed.

For example if a test case input is

2,3

You should output the following

This is 1 th execution of thread 1 with global var value 1

This is 1 th execution of thread 2 with global var value 3

This is 2 th execution of thread 1 with global var value 4

This is 2 th execution of thread 2 with global var value 6

This is 3 th execution of thread 1 with global var value 7

This is 3 th execution of thread 2 with global var value 9

To get the output simply use the following code in command line

gcc thread_test.c -o a.out

./a.out &lt; test_case_file.txt &gt; output_file.txt

If A = 0 then output

No Threads

<strong>__________________________________</strong>

<strong>tcb.h</strong>

#include &lt;ucontext.h&gt;

typedef struct TCB_t {

<table>

 <tbody>

  <tr>

   <td width="152">struct TCB_t</td>

   <td width="80">*next;</td>

  </tr>

  <tr>

   <td width="152">struct TCB_t</td>

   <td width="80">*prev;</td>

  </tr>

  <tr>

   <td width="152">ucontext_t</td>

   <td width="80">context;</td>

  </tr>

  <tr>

   <td width="152">} TCB_t;</td>

   <td width="80"> </td>

  </tr>

 </tbody>

</table>

void init_TCB (TCB_t *tcb, void *function, void *stackP, int stack_size) {

memset(tcb, ‘ ’, sizeof(TCB_t)); getcontext(&amp;tcb-&gt;context);

// wash, rinse

// have to get parent context,

else snow forms on hell tcb-&gt;context.uc_stack.ss_sp = stackP;

tcb-&gt;context.uc_stack.ss_size = (size_t) stack_size; makecontext(&amp;tcb-&gt;context, function, 0);// context is now cooked