+++
author = "mukund"
title = "Binary Bomb Phase 6"
date = "2025-05-11"
description = "Binary Bomb Phase 6"
tags = [
    "assembly", "reverse engineering"
]
+++

I completed the course '[Architecture 1001: x86-64 Assembly](https://apps.p.ost2.fyi/learning/course/course-v1:OpenSecurityTraining2+Arch1001_x86-64_Asm+2021_v1/home)' from **Open Security Training 2**, and there was a very nice challenge in this course - `Binary Bomb`.

You can find the challenge binary here - https://gitlab.com/opensecuritytraining/arch1001_x86-64_asm_code_for_class/-/tree/master/binary_bomb_lab/windows?ref_type=heads

I will be walking through the last phase - 6 of the Binary Bomb challenge using WinDbg.

Open the executable with the solutions file path in the argument. The solutions file would be containing the solutions to previous 5 phases in separate lines.

Reload the symbols -> `.reload /f`

Disassemble the phase_6 function -> `uf phase_6`

Below is the C code written after analysing the assembly instructions:

```
#include <stdio.h>
#include <stdlib.h>

struct Node {
    int value;
    int index;
    struct Node *next;
}; 

struct Node* createNode(int val, int index) {
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->value = val;
    newNode->next = NULL;
    return newNode;
}


int phase6(int inp[])
{
    struct Node* ll1 = createNode(0x212,1);
    struct Node* ll2 = createNode(0x1c2,2);
    struct Node* ll3 = createNode(0x215,3);
    struct Node* ll4 = createNode(0x393,4);
    struct Node* ll5 = createNode(0x3a7,5);
    struct Node* ll6 = createNode(0x200,6);

    ll1->next = ll2;
    ll2->next = ll3;
    ll3->next = ll4;
    ll4->next = ll5;
    ll5->next = ll6;

    struct Node* head = ll1;

    //first part
    int a = 0;
    while(a < 6)
    {
        if(inp[a] >=1 && inp[a]<=6)
        {
            int b = a + 1;
            while(b < 6) 
            {
                if(inp[a] != inp[b])
                {
                    b++;
                }
                else {
                    return 0;
                }
            }
            a++;
        }
        else {
            return 0;
        }
    }
    printf("first part done!\n");

    //second part
    a = 0;
    struct Node* narr[6] = {0};
    struct Node* curr = NULL;
    while(a < 6) 
    {
        curr = head;
        int b = 1;    
        while(b < inp[a])
        {
            curr = curr->next;
            b++;
        }
        narr[a] = curr;
        a++;
    }

    head = narr[0];
    curr = head;

    //third part
    a = 1;
    while(a < 6)
    {
        curr->next = narr[a];
        curr = curr->next;
        a++;
    }
    
    //fourth part
    curr->next = NULL;
    curr = head;
    a = 0;
    while(a < 5)
    {
        if(curr->value < curr->next->value)
        {
            return(0);
        }
        else
        {
            curr = curr->next;
            a++;
        }
    }
    return 1;
}

int main()
{
    int inp[] = {5,4,3,1,6,2};
    int out = phase6(inp);
    if(out == 0)
    {
        printf("Game over, bomb exploded\n");
    }
    else
    { 
        printf("Game completed\n");
    }
}
```

Let's go through the assembly step-by-step and map it to the C code:

- We see a call to the function 'read_six_numbers'. Add a breakpoint on the call instruction if you want to explore the function.
- The function `read_six_numbers` will take `rcx`, `rdx` as arguments. `rdx` is set with the address of `rbp+48h`. The user input values will be set from the address starting at `rdx`, and hence `rbp+48h` will have the input values.

![read_six_numbers](/images/sscanf%20numbers.png)
![breakpoint on read_six_numbers](/images/sscanf%20breakpoint.png)

- Add breakpoint to check the output of read six numbers. It can be seen that even if the input contains more than 6 digits, the output is 6, so it could mean that first 6 digits would be considered.

![read_six_numbers input](/images/read%20six%20numbers.png)
![output of read_six_numbers](/images/read%20six%20numbers%20output.png)

- After the call to read_six_numbers, a variable `rbp+0c4h` (let's call this as variable `a`) is set to 0 and there is jump statement.

- The jump statement takes the control to the compare function. This function checks if the value of the variable is greater than or equal to 6.

![compare length of user input](/images/jump1.png)

- If the value is greater than equal to 6, the jump statement is executed and takes the control to a different location which is outside the loop. This seems like the false condition for a loop.

![jump for wrong loop condition](/images/a%20loop%20wrong%20condition%20jump%20loc.png)

- But, if the value of `a` is less than 6, the jump does not take place and flow continues after the jump statement.

![first loop](/images/first%20loop%20condition.png)

- `rbp+48h` had the user input values. The value of variable `a` is being used as an array index (let's call the array as `inp`). 
The value of array at index *a* `inp[a]` is compared with 1.
If the value turns out to be less than 1, the explode_bomb is function being called with jump statement.

![first loop condition 1](/images/func%201%20condition%201.png)

- If the value is more than 1, then again the `inp[a]` is being compared with 6. If the value is greater than 6, the jump statement is not executed and explode_bomb function will be called.
**So, the value of `inp[a]` should be in range of 1 and 6.**

![first loop condition 2](/images/func%201%20condition%202.png)

- If the value of `a` is in [1,6], then a new variable `rbp+0e4h` (let's call it variable `b`) is assigned the value `a+1`.
- The jump statement takes to condition statement, where the value of `b` is compared with 6. If the value is greater than or equal to 6, the jump statment again takes control to the increment operation of variable `a`.

- If the value of `b` is less than 6, then there is a comparison between `inp[a]` and `inp[b]`. 

![array values comparison](/images/func%201%20array%20values%20cmp.png)

- If the values are equal, the jump statement is not taken, and explode_bomb function is called. If the values are not equal, the jump statement takes the control to increment of variable `b` and back to the loop condition of comparison of value `b` with 6.

![array values not equal jump](/images/func%201%20array%20values%20not%20equal%20jump.png)
![b value increment](/images/func%201%20array%20values%20not%20equal%20jump.png)

- Now, if we go back and look around the read_six_numbers function, there is an instruction related to node. Address of this node is saved to `rbp+8`.

![node instruction](/images/node%20instruction.png)

- If we try to print the value at address, we could see that it contains a different address as well. We can print that too, and find a pattern.

![node address1](/images/node%20address1.png)
![node address1](/images/node%20address2.png)
![node address1](/images/node%20address3.png)

- This shows a Linked List structure where each node has a value and reference the next node address. The last node with the address *b108f000* seems to be the last node, as it doesn't point to another address.

- When the condition of main loop became false, the just statement took the control to outside the loop where the value of `a` is set to 0 and then jump statement takes to another condition where `a` is compared with 6. This seems to be the start of another main loop.

![a compared against 6 again](/images/a%20ge%206%20condition%202.png)

- If the value of `a` again is greater than or equal to 6, the control passes to a different location. 

- If the value of `a` is less than 6, `rbp+8` which contains the address of first linked list node, is passed to `rbp+28h`. The value of `b` is set to 1.
Let's call the node at `rbp+8` as `head`, and `rbp+28h` as `curr` (current).

![second loop](/images/second%20loop%20inside.png)

- The value of `b` is compared with the value of array at index `a`. If value of `b` is less, the value of `curr` is set to `curr->next` (the next node). For each node, the value at the address is the node value, then there is index, and then the address to next node. So, the <node address> + 8, gives the address of next node.

![second loop first condition](/images/second%20loop%20condition%201.png)
![second loop node iteration](/images/second%20loop%20node%20iteration.png)

- The value of `b` is increased by 1 and agin the comparison starts, so it means the comparison of `b` with value of array at index `a` was a loop condition (nested loop) and here the loop ends.

![second loop b increment](/images/second%20loop%20b%20increment.png)

- If the value of `b` when compared with value of array at `a` was greater, then the jump would pass the control outside the inner loop. `rbp+78h` contained the address of the start of the node of linked list. From this address, an array is taken (let's call it `narr`), whose value at index *a* `narr[a]` is set with the value of `curr` node.

![second loop outside inner loop](/images/second%20loop%20outside%20inner%20loop.png)

- Jump instruction takes the control to iteration where the value of `a` is incremented by 1, and again the condition is checked, which means the loop ends.

![second loop iteration](/images/second%20loop%20iteration.png)

- If the value of `a` becomes greater than or equal to 6, then the loop condition breaks and control passes outside the loop.
The address at `rbp+78h` (`narr[0]` node array) is being set to `rbp+8` (`head` node). The address from `rbp+8` (`head`) is being set to `rbp+28h` (`curr` node).

![second loop linked list address 1](/images/second%20loop%201.png)

- We can check the value of `rbp+78` (`narr`) has address of different nodes of the linked list.

![second loop linked list address 1](/images/second%20loop%20nodes%20address.png)

- We can break at the line where `rbp+28h` (`curr`)is assigned and check the value. It has the starting node address of linked list.

![rbp 28h starting address](/images/rbp%2028h%20starting%20address.png)

- So, this indicates something like `head = narr[0]; curr = head;`

- The value of `a` is set to 1, and jump takes the control to a comparison statement which again compares the value of `a` with 6. This could be the start of another loop.

![third loop start](/images/third%20loop%20start.png)

- If the value of `a` is greater than or equal to 6, the jump statement takes the control to a different location.

- If the value of `a` is less, the loop starts/continues.
The `curr` node (`rbp+28h`) next element is set to the node at index `a` of the node array `narr` (`curr->next = narr[a]`). Then `curr` node is again set to `curr->next`.

![third loop inside](/images/third%20loop%20inside.png)

- The jump instruction takes the control to iteration where the value of `a` is increased by 1. After the iteration again there is the comparison statement of `a` which is the loop condition, so the loop ends.

![third loop iteration](/images/third%20loop%20iteration.png)

- If the value of `a` becomes more than or equal to 6, the loop breaks and control moves out of loop.
The `curr` node next address is set to 0, which indicates to NULL. It means that the `curr` node does not point to any other node.
- Then the `curr` node is pointed to the `head` node. Value of `a` is set to 0.

![after third loop](/images/after%20third%20loop.png)

- The jump statement then takes the control to the comparison instruction. If the value of `a` is greater than or equal to 5, jump statement takes the control to another location.

![fourth loop condition](/images/fourth%20loop%20condition.png)

- If the value of `a` is less than 5, comparison happens between values of `curr` and `curr->next`. If `curr` value is less than `curr->next` value, the `explode_bomb` function is being called.

![fourth loop true condition](/images/fourth%20loop%20condition%20true.png)

- If the value of `curr` is equal to or greater than value of `curr->next`, then `curr` node is set to `curr->next` node.

![curr curr next condition](/images/curr%20curr%20next%20condition.png)

- The jump statement takes the control to iteration where the value of `a` is increased by 1. The loop condition comes again, which means that the loop ends.

![fourth loop iteration](/images/fourth%20loop%20iteration.png)

- If the value of `a` is greater than or equal to 5, the control passes outside the loop.

![after fourth loop](/images/after%20fourth%20loop.png)

The program ends here.

To create the C program, we need to create the nodes of the linked list. We can take the values of nodes as shown in the assembly code by printing the values at **00007ff6`b108f000**.

- The first loop is trying to check if the user input values are also distinct.
- The second loop is trying to put different nodes at different array index based on an algorithm. Our input is used to set the order of nodes in the array.
- The third loop is trying to make links between different nodes of the array to form the linked list.
- The fourth loop is trying to check if the nodes of linked list are linked in the descending order of their values.

So, we should input the numbers such that the nodes at those indices (our inputs) be in descending order of their value.