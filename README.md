As assembler and emulator for a custom RISC instruction set architecture.


## The Language
The language is for a machine with four registers:
- Two registers, A & B, arranged as an internal stack.
- A program counter, PC
- A stack pointer, SP

These registers are 32 bits in size. Instructions have either no operands or a single operand. The
operand is a signed 2's complement value. The encoding uses the bottom 8 bits for opcode and
the upper 24 bits for operand.


The language is line based (one statement per line). **Comments**: Comments begin after a `;` and anything after a `;` is ignored. Trailing and leading **Whitespaces** are ignored as well.

An instruction involves a label followed by an optional statement. For branch instructions label use should calculate the branch displacement. For non-branch instructions, the label value should be used directly.

**Valid Label Name:** An alphanumeric string beginning with a letter. 

**Valid Operand:** An operand is either a label or a number, the number can be decimal, hex or octal. 

### Example Instructions
The following are permitted lines.
```asm
; a comment
                    ; another comment
label1:              ; a label on its own
ldc 5                ; an instruction
label2: ldc 5        ; a label and an instruction
        adc 5        ; an instruction
label3:ldc label3    ;look no space between label and mnemonic 
```

### The assembler
The produces three files:
- A log (`*.log`) file showing the error logs.
- The assembler listing (`*.lst`) file showing the assembly instructions along with their corresponding machine code, and what value is stored at each address.
- The object (`.o`) file of the produces machine code.

The `SIMPLE` instruction set
- 
| Mnemonic | Opcode | Operand | Formal Specification                           | Description                                                                                            |
|----------|--------|---------|------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| data     |        | value   |                                                | Reserve a memory location,<br>initialized to the value specified                                       |
| ldc      | 0      | value   | `B := A;`<br>`A := value;`                     |                                                                                                        |
| adc      | 1      | value   | `A := A + value;`                              | Add the value specified to the accumulator                                                             |
| ldl      | 2      | offset  | `B := A;`<br>`A := memory[SP + offset];`       | Load Local                                                                                             |
| stl      | 3      | offset  | `memory[SP + offset] := A;`<br>`A := B;`       | Store Local                                                                                            |
| ldnl     | 4      | offset  | `A := memory[A + offset];`                     | Load non-local                                                                                         |
| stnl     | 5      | offset  | `memory[A + offset] := B;`                     | Store non-local                                                                                        |
| add      | 6      |         | `A := B + A;`                                  | Addition                                                                                               |
| sub      | 7      |         | `A := B - A;`                                  | Subtraction                                                                                            |
| shl      | 8      |         | `A := B << A;`                                 | Shift Left                                                                                             |
| shr      | 9      |         | `A := B >> A;`                                 | Shift Right                                                                                            |
| adj      | 10     | value   | `SP := SP + value;`                            | Adjust Stack Pointer                                                                                   |
| a2sp     | 11     |         | `SP := A;`<br>`A := B;`                        | Transfer A to Stack                                                                                    |
| sp2a     | 12     |         | `B := A;`<br>`A := SP;`                        | Transfer Stack top to A                                                                                |
| call     | 13     | offset  | `B := A;`<br>`A := PC;`<br>`PC := PC + offset` | Call Procedure                                                                                         |
| return   | 14     |         | `PC := A;`<br>`A := B;`                        | Return from procedure                                                                                  |
| brz      | 15     | offset  | `if A == 0 then:`<br>    `PC := PC + offset`   | If accumulator is zero, branch to the specified offset.                                                |
| brlz     | 16     | offset  | `if A < 0:`<br>    `PC := PC + offset`         | If accumulator is less than zero, then branch to the specified offset.                                 |
| br       | 17     | offset  | `PC := PC + offset`                            | Branch to the specified offset.                                                                        |
| HALT     | 18     |         |                                                | Stops the emulator. This is not a 'real' instruction, but needed to tell your emulator when to finish. |
| SET      |        | value   |                                                | Set the label on this line to the specified value (rather than the PC).                                |

## Listing File
The assembler produces a listing file in the following format
```
00000000				label:
00000000	00000000	ldc	0
00000001	fffffb00	ldc	fffffb00
00000002	00000500	ldc	500
00000003				loop:
```


## The emulator
- The loads the object file and emulates the program. 
- The `-before` and `-after` options can be used to produce the memory dump of program, before and after execution respectively, as specified.
- The `-trace` option can be used to view the trace of instructions being executed. Can be particularly helpful in debugging.