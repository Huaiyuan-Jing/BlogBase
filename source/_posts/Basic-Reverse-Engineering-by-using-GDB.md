---
title: Basic Reverse Engineering by using GDB
date: 2024-11-29 11:50:19
tags:
---

<!-- @format -->

# Problem

The challenge is to determine the correct set of five inputs expected by each of the four safes (s1, s2, s3, s4). Running the binary file prompts you to input guesses sequentially, as demonstrated below:

```bash
$ ls
s1* s2* s3* s4*
$ ./s3
input 1 (of 5)? elephant-ears
input 2 (of 5)? one-two-three
input 3 (of 5)? at-the-end
input 4 (of 5)? open-two-door
***************************************
*********** ALARM TRIGGERED ***********
***************************************
$ ./s3
input 1 (of 5)? elephant-ears
input 2 (of 5)? one-two-three
input 3 (of 5)? at-the-end
input 4 (of 5)? open-the-door
input 5 (of 5)? alphabet-soup
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!! SAFE OPENED !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```

# Solution

## Step 1: Check x86 instructions by using objdump

To analyze the binary, first convert it into human-readable assembly instructions:

```bash
objdump -d s1 > s1.txt
```
This generates a text file, s1.txt, containing x86 assembly instructions. Focus on the main function, as it handles the input validation logic.

Here is a snippet of the main function from s1.txt:
```

    ...

00001295 <main>:
    1295:	8d 4c 24 04          	lea    0x4(%esp),%ecx
    1299:	83 e4 f0             	and    $0xfffffff0,%esp
    129c:	ff 71 fc             	push   -0x4(%ecx)
    129f:	55                   	push   %ebp
    12a0:	89 e5                	mov    %esp,%ebp
    12a2:	57                   	push   %edi
    12a3:	56                   	push   %esi
    12a4:	53                   	push   %ebx
    12a5:	51                   	push   %ecx  // prepare
    12a6:	81 ec 28 02 00 00    	sub    $0x228,%esp
    12ac:	e8 5f fe ff ff       	call   1110 <__x86.get_pc_thunk.bx>
    12b1:	81 c3 07 2d 00 00    	add    $0x2d07,%ebx
    12b7:	8b 51 04             	mov    0x4(%ecx),%edx
    12ba:	65 a1 14 00 00 00    	mov    %gs:0x14,%eax
    12c0:	89 45 e4             	mov    %eax,-0x1c(%ebp)
    12c3:	31 c0                	xor    %eax,%eax  // %eax == 0
    12c5:	8b 83 3c 00 00 00    	mov    0x3c(%ebx),%eax
    12cb:	8b 38                	mov    (%eax),%edi
    12cd:	83 39 02             	cmpl   $0x2,(%ecx)
    12d0:	74 6f                	je     1341 <main+0xac>
    12d2:	be 01 00 00 00       	mov    $0x1,%esi
    12d7:	8b 83 3c 00 00 00    	mov    0x3c(%ebx),%eax
    12dd:	89 85 d4 fd ff ff    	mov    %eax,-0x22c(%ebp)
    12e3:	8d 83 ea e0 ff ff    	lea    -0x1f16(%ebx),%eax
    12e9:	89 85 d0 fd ff ff    	mov    %eax,-0x230(%ebp)
    12ef:	83 fe 06             	cmp    $0x6,%esi
    12f2:	74 7f                	je     1373 <main+0xde>
    12f4:	8b 85 d4 fd ff ff    	mov    -0x22c(%ebp),%eax
    12fa:	39 38                	cmp    %edi,(%eax)
    12fc:	74 5f                	je     135d <main+0xc8>
    12fe:	83 ec 04             	sub    $0x4,%esp
    1301:	57                   	push   %edi
    1302:	68 00 02 00 00       	push   $0x200
    1307:	8d 85 e4 fd ff ff    	lea    -0x21c(%ebp),%eax
    130d:	50                   	push   %eax
    130e:	e8 3d fd ff ff       	call   1050 <fgets@plt>
    1313:	83 c4 10             	add    $0x10,%esp
    1316:	85 c0                	test   %eax,%eax
    1318:	74 d5                	je     12ef <main+0x5a>
    131a:	83 ec 04             	sub    $0x4,%esp
    131d:	6a 0a                	push   $0xa
    131f:	6a 00                	push   $0x0
    1321:	8d 85 e4 fd ff ff    	lea    -0x21c(%ebp),%eax
    1327:	50                   	push   %eax
    1328:	e8 93 fd ff ff       	call   10c0 <strtol@plt>
    132d:	83 c6 01             	add    $0x1,%esi
    1330:	83 c4 10             	add    $0x10,%esp
    1333:	39 84 b3 50 00 00 00 	cmp    %eax,0x50(%ebx,%esi,4)
    133a:	74 a7                	je     12e3 <main+0x4e>
    133c:	e8 cc fe ff ff       	call   120d <fail>
    1341:	83 ec 08             	sub    $0x8,%esp
    1344:	8d 83 e8 e0 ff ff    	lea    -0x1f18(%ebx),%eax
    134a:	50                   	push   %eax
    134b:	ff 72 04             	push   0x4(%edx)
    134e:	e8 4d fd ff ff       	call   10a0 <fopen@plt>
    1353:	89 c7                	mov    %eax,%edi
    1355:	83 c4 10             	add    $0x10,%esp
    1358:	e9 75 ff ff ff       	jmp    12d2 <main+0x3d>
    135d:	83 ec 04             	sub    $0x4,%esp
    1360:	56                   	push   %esi
    1361:	ff b5 d0 fd ff ff    	push   -0x230(%ebp)
    1367:	6a 01                	push   $0x1
    1369:	e8 42 fd ff ff       	call   10b0 <__printf_chk@plt>
    136e:	83 c4 10             	add    $0x10,%esp
    1371:	eb 8b                	jmp    12fe <main+0x69>
    1373:	e8 dc fe ff ff       	call   1254 <success>
    1378:	8b 45 e4             	mov    -0x1c(%ebp),%eax
    137b:	65 2b 05 14 00 00 00 	sub    %gs:0x14,%eax
    1382:	75 11                	jne    1395 <main+0x100>
    1384:	b8 00 00 00 00       	mov    $0x0,%eax
    1389:	8d 65 f0             	lea    -0x10(%ebp),%esp
    138c:	59                   	pop    %ecx
    138d:	5b                   	pop    %ebx
    138e:	5e                   	pop    %esi
    138f:	5f                   	pop    %edi
    1390:	5d                   	pop    %ebp
    1391:	8d 61 fc             	lea    -0x4(%ecx),%esp
    1394:	c3                   	ret
    1395:	e8 06 00 00 00       	call   13a0 <__stack_chk_fail_local>
    139a:	66 90                	xchg   %ax,%ax
    139c:	66 90                	xchg   %ax,%ax
    139e:	66 90                	xchg   %ax,%ax

    ...
```

## Step 2: Analysis main function

Now we have two choices of reading this list of instructions. First is translating these instructions one by one. Second is rebulid the control flow reversely. I choose the second way.

By observation, we can see there two sentences call success and fail functions:

```
    1373:	e8 dc fe ff ff       	call   1254 <success>
```

```
    133c:	e8 cc fe ff ff       	call   120d <fail>
```

Looking into it, we can find the sentence before the success function call is a jmp instruction to previous lines. So success function cannot be entered by sequence control flow. So we check the jmp instruction that jumps to 1373.

```
    12e3:	8d 83 ea e0 ff ff    	lea    -0x1f16(%ebx),%eax
    12e9:	89 85 d0 fd ff ff    	mov    %eax,-0x230(%ebp)
    12ef:	83 fe 06             	cmp    $0x6,%esi
    12f2:	74 7f                	je     1373 <main+0xde>
```

So these are sentences jumps to 1373 and its requirements. We can see "%esi == 6" is the key point to trigger success function. So we continue looking into sentences that change register %esi.

```
    132d:	83 c6 01             	add    $0x1,%esi
    1330:	83 c4 10             	add    $0x10,%esp
    1333:	39 84 b3 50 00 00 00 	cmp    %eax,0x50(%ebx,%esi,4)
    133a:	74 a7                	je     12e3 <main+0x4e>
```

These instructions is the only place that changes %esi's value and jump to the previous block. So the requirements of this block is "%eax == %ebx + %esi _ 4 + 0x50", which %eax is the result of strtol of our inputs. Now the requirement is clear: our inputs must be an integer and equals to value in "%ebx + %esi _ 4 + 0x50".

## Step 3: Using GDB to get password

The last step is adding a break point at instruction 1333, which is the compare operation. Can print the value in "0x50(%ebx,%esi,4)" so we can get the answer.

1. Use gdb to open the file and add a breakpoint at main function:

```bash
$ gdb ./s1
(gdb) break main
Breakpoint 1 at 0x12a6
```

2. Start to run:

```bash
(gdb) run
Starting program: /home/hyjing/Onedrive/ClassFiles/Comp Sci 354/p5/s1
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".

Breakpoint 1, 0x565562a6 in main ()
```

3. Add the second breakpoint at 1333:

```bash
(gdb) break *0x56556333
Breakpoint 2 at 0x56556333
(gdb) continue
Continuing.
input 1 (of 5)? [Any Input]

Breakpoint 2, 0x56556333 in main ()
```

4. Get the point and start to print the result:

```bash
(gdb) print *(0x50 + $ebx + 4*$esi)
$1 = 352303
(gdb) print *(0x50 + $ebx + 4*($esi + 1))
$2 = 855053
(gdb) print *(0x50 + $ebx + 4*($esi + 2))
$3 = 170240
(gdb) print *(0x50 + $ebx + 4*($esi + 3))
$4 = 4961
(gdb) print *(0x50 + $ebx + 4*($esi + 4))
$5 = 108932
```

5. Get the five passwords, save them in s1.solution and check them.

```bash
$ ./s1 < s1.solution
input 1 (of 5)? input 2 (of 5)? input 3 (of 5)? input 4 (of 5)? input 5 (of 5)? !!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!!!!!!!!!! SAFE OPENED !!!!!!!!!!!
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
```
