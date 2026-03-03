The objective of this project is to design and demonstrate a hardware accelerator that processes 2D spatial point data stored 
in EEPROM to identify the closest pair of points. For this purpose we have used a ROM that takes input from a 4-bit counter 
initiated with 0. 
The 0th address will give us the value of the number of points used (n). Since the permitted values of n are between 4 and 8, 
we extracted the first three bits (maximum n=7 which is 111 in binary) of the ROM and have stored it in a register (n-register).
We enabled this register such that it takes input only when the output of counter is 0 using a NAND gate. 

Now we need to loop through the indices such that i=0;i<n;i++ and j=i+1;j<n;j++(to avoid repeated distance calculation for the
same pair). For this purpose we have initiated two counters respectively for i and j. The counter i starts at 0 and counter j 
starts from i+1 which we have implemented using an adder. To ensure counter j stops at n-1 we have used a 4-bit comparator 
whose inputs are n and j respectively, and when j = n the load signal of j counter is connected to the output of the 
comparator(A=B) via a NOT gate(i.e., if j=n then A=B goes high which goes through a NOT gate and becomes 0 but the load 
itself has a NOT gate attached to it thus it becomes 1 and the j counter is loaded with i+1.). To ensure the i is incremented
only after j loops till n-1, we have connected the output of comparator(A=B) to ENP and ENT of counter i.

Then we used two 4 bit comparators, to compare the indices, whose one input is from the first three bits of EEPROM and 
the second is from i counter for first comparator and j counters for the second respectively. This step makes sure that we 
are extracting the points in a sequential manner instead of randomly.

We then used 4-registers to store the points (x_i,y_i) and (x_j,y_j) whose distance is to be caclulated. To enable these
registers we have used the axis selector from EEPROM as well as the comparator output connected through an AND gate. 

Moving onto the next step, we made a subtractor using the adders to compute Delx(x2-x1) and Dely(y2-y1). Then, we made 
2 mulipliers to compute Delx square and Dely square. We then added them using adders to get the distance between the two
points. 

As we need to store the values of points, minimum distance and the indices for further calculations, comparisons and output 
respectively we use 8 registers in which 3 are used to store the distance(9-bit maximum),2 are used to store indices(3 bits 
each) and 4 are used to store values of the points(4 bits for each point). To reduce the cost we have used a common register
in which the 1st bit will be the MSB of the distance and he rest three bits will be the i index.

We have initiated the distance registers initial value to be 1111(maximum) so that the first computed distance will be the
minimum so far.
We then used comparators to compare the minimum distance coming from the register and the distance that we have just 
calculated. If it is less than the stored minimum we have updated all the registers used for minimum storage. We have used 
multiplexers for this purpose. This was done by enabling the registers and multiplexers with A<B output pin of the comparator 
used.
For the case when A=B we have implemented two modulo circuits(one for each pair). Then we used comparators to find the 
best pair among the two.
To ensure the registers are updated with the best indices and values of points we have used AND and OR gates. With the 
inputs of AND gate being A=B of the distance comparator and A<B of the index comparator of modulo. Inputs of OR gate being
the output of AND gate previously used and A<B of the distance comparator.
Now that the registers hold the indices for the least distance pair, we have connected them to a 7-segment BCD display to 
display the final result.
