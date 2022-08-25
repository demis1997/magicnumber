# magicnumber
The answer to the universe: requires evm and alot of research + banging your head

I have never been so happy for information online, this broke my soul the same way assembly did and took alot of reading plus seeing solutions online but it was worth it.

The point of the contract is to return the number 42 in bytecode and use the only function within the contract to do so. You have to read on bytecodes, opcodes, memory pointers to actually understand what is happening. The idea is that there is a memory pointer to help us push data and when a contract is first initialised, this pointer gets created. 

0x40 refers to the position
0x52 refers to the stored code aka mstore
and 0x80 refers to the size 

but you use 0x40 and then 0x80 since we want the leftmost item to be used first
and then between 0x00 and 0x40 it's all empty and saved for hashing so we dont need that for now.
and what usually happens is that theres a push for the location 0x40 and then a push for the size 0x80 followed by mstore (0x52)
The location is always picked first

Now we need to return "42" and looking at the evm opcodes you will notice there is a return function and for that we will need to store it in memory which is made up of the points above: mstore,location and size. 
To do this we use PUSH1 first to push the value and the size.
In this case the value is 42 so 0x42 and the size is 0x20 
push 42 into the stack
then push 0x50 in the stack to say where the value of 42 will be located and then we will store the value in memory.
Then we will store the size of the value which is 32 bytes and push the position of the value to where it will be located.
Lastly we push the position of the size (0x50) and then return the value and the size of the value.

This will give you a bytecode that is 10 bytes (20 bits)
Note:Stack will use up the least amount of gas out of any memory type in smart contracts
Because of this we need to use codecopy since Stack does not hold information for that long and we dont exactly copy the value.

We copy the runtime code and for that we need the size, the current location and the location we want to send it to.
After the opcodes are done (meaning you pushed and returned) all these steps will come up to a total amount/length and you can see the position of the runtime by counting the number of bytes of your opcodes.
If the length of the opcode is 24 then the position would be 24, if there are 15 bytes then position is 15
The reason this is important is because we need to convert it into the initialization opcode by including the position of it.
The last part is adding the initialization code and runtimecode together and putting that into a variable.
Send that variable as a transaction to the contract and you are set.
This method is the same regardless of what needs to be sent back if there are memory restrictions.
The same principles would apply if the number was 30 and not 42 

I had alot of help for this following tutorials and reading documentation trying to figure it out
Some good resources:
https://github.com/crytic/evm-opcodes
https://ethereum.github.io/execution-specs/autoapi/ethereum/frontier/vm/instructions/stack/index.html
https://www.youtube.com/watch?v=FsPWuKK8mWI
