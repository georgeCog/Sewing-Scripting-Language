# Language Rules
*For more info in the general goals see [README](README.md).*

## Plan

### Commenting and Spacing

// is used to comment out the rest of the line just like in JavaScript.

White spaces are ignored except for next line wich represents the next instruction.

### Compilation

A pattern script will compile to a JavaScript function. This function will take a list of lengths as it's arguments and return a list of points and a list of lines that can then be rendered.

### Data Types

| Name | Eample Declaration in Code |Computer Representation | Description |
| -------- | -------- | -------- | -------- |
| Length | 10 or 1.8 or WAIST |Float | A length in cm |
| Direction | 90 or 8.5 |Float | An angle clockwise from vector (1,0) in degrees |
| Point Reference | 0 or 5 | Int | Reference to point with given index 
| Point | n/a |(Float, Float, Point Reference) | (x,y,i) -> Point at coordinates x,y with the index i |
| Line Reference | bust_line | String | Reference to line with name String |
| Line | n/a |(Point Reference,Point Reference,Line Reference) | (a,b) -> Line between a and b referenced by the given Line Reference |
| ExtLine | n/a | (Point Reference,Direction,Line Reference) | Unbounded line with origin Point in given Direction referenced by the given Line Reference|

A list of Point, Line and ExtLine is output by the compiled function.

### Initilization 

Before instructions are layed out in the code, the input Length names and their ease allowance (if applicable) must be stated. Each on a new line. Length names must be in all caps and underscores. Ease allowance is placed after ':' if required. e.g.
```
ARM_HOLE //Arm hole circumference
TOP_SLEEVE
TOP_ARM:5 //Top arm measurement with 5cm ease
WRIST:6.5 //Wrist measurement with 6.5cm ease
```
The Points list starts with the point (0,0,0) i.e. The origin with index 0

### Instruction Syntax

Each instruction creates a line and begins with a line deffinition,which takes the format:
```
{Point A Reference}->{Point B Reference}{Optional '^' + Line Reference}
//e.g.
2->3 //Will create Line (2,3,null)
4->6^CB //Wille create :ine (4,6,"CB")
```
- Point A must be an existing point
- Point B can be an existing or non-existing point

If Point B already exists then the Line (PointA, Point B) is added to the output lines list

If Point B does not exist then information on how to get to B from A is given after a ':'. This is then used to generate Point B and add it to the output Points list. e.g.
```
0->1:{Something here}
```
#### Direction and Length
To generate Point 1 from Point 0 by traveling a given Length in a given Direction use the syntax:
```
{Length}<{Direction}
```
e.g.
```
0->1:WRIST<90
```
#### Vector
To generate Point 1 by traveling a given vector with/without modified magnitude use the syntax:
```
[{Point A} -> {Point B}] // Vector from A to B
[{Point A} -> {Point B}]/2+5 // Vector from A to B with magnitude divided by 2 plus 5
```
e.g.
```
2->3:[2->4]/2+5.5
```
#### Squaring Off
If you need to square off the point created by the instruction you can end it with | followed by 'SA' (Square across) or 'SD' (Square down) optionaly followed by '^' and a reference string for the generated line. e.g.
```
2->3:5<-90|SA^back //Creates Line (2,3) and ExtLine (2,0,"back")
3->6|SD // Creates line (3,6) and ExtLine (6,90,null)
```

### Functions

Lengths can be created using the oporators +,-,*,/ in the same way they act on Floats in JavaScript

## Example Code

```
///////////Bodice Block////////////
N_W //Nape to waist
BUST:10
BACK:1.6 //Back width
SHOULDER
CHEST:0.6 //Chest width
Waist:4
///////////Instructions/////////////
0->1^CB : N_W+2<90 | SA
0->2 : BUST/2+0.5<0 | SD^CF
0->3 : 2<90 |SA
3->4 : [3->1]+4 | SA^bust_line
```