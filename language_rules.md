# Language Rules
*For more info in the general goals see [README](README.md).*

# Plan

The current system could do with some work but almost all instructions written in a pattern book translate to one line of scripting which I am happy with.

## Commenting and Spacing

// is used to comment out the rest of the line just like in JavaScript.

White spaces are ignored except for next line wich represents the next instruction.

## Compilation

A pattern script will compile to a JavaScript function. This function will take a list of lengths as it's arguments and return a list of points and a list of lines that can then be rendered.

## Data Types

| Name | Eample Declaration in Code |Computer Representation | Description |
| -------- | -------- | -------- | -------- |
| Length | 10 or 1.8 or WAIST |Float | A length in cm |
| Direction | 90 or 8.5 |Float | An angle clockwise from vector (1,0) in degrees |
| Point Reference | 0 or 5 | Int | Reference to point with given index 
| Point | n/a |(Float, Float, Point Reference) | (x,y,i) -> Point at coordinates x,y with the index i |
|Vector | (3,0) or (2->5) | (Float, FLoat) | (x,y) -> Vector with coordinates x,y |
| Line Reference | bust_line | String | Reference to line with name String |
| Line | n/a |(Point Reference,Point Reference,Line Reference, Boolean) | (a,b) -> Line between a and b referenced by the given Line Reference and Boolean showing if it is dotted|
| ExtLine | n/a | (Point Reference,Direction,Line Reference, Boolean) | Unbounded line with origin Point in given Direction referenced by the given Line Reference and Boolean showing if it is dotted|

A list of Point, Line and ExtLine is output by the compiled function.
Lines Referenced by a String beginning "TEMP_" are not output

*Coordinate system goes from top left to bottom right*

## Initilization 

Before instructions are layed out in the code, the input Length names and their ease allowance (if applicable) must be stated. Each on a new line. Length names must be in all caps and underscores. Ease allowance is placed after ':' if required. e.g.
```
ARM_HOLE //Arm hole circumference
TOP_SLEEVE
TOP_ARM:5 //Top arm measurement with 5cm ease
WRIST:6.5 //Wrist measurement with 6.5cm ease
```
The Points list starts with the point (0,0,0) i.e. The origin with index 0

## Instruction Syntax

Instructions Create Lines, a Point or both.

### Line

Each instruction that creates a line begins with a line deffinition, which takes the format:
```
//For solid line
{Point A Reference}-{Point B Reference}{Optional '^' + Line Reference}
For Dotted Line
{Point A Reference}~{Point B Reference}{Optional '^' + Line Reference}
//e.g.
2-3 //Will create Line (2,3,null,false)
4-6^CB //Will create line (4,6,"CB",false)
5~8 // Will create line (5,8,null,true)
```
- Point A must be an existing point
- Point B can be an existing or non-existing point

If Point B already exists then the Line (PointA, Point B) is added to the output lines list

If Point B does not exist then information on how to get to B from A is given after a ':'. This is then used to generate Point B and add it to the output Points list. e.g.
```
0-1:{Something here}
```
#### Direction and Length
To generate Point 1 from Point 0 by traveling a given Length in a given Direction use the syntax:
```
{Length}#{Direction}
```
e.g.
```
0-1:WRIST#90
```
#### Vector
To generate Point 1 by traveling a given vector with/without modified magnitude use the syntax:
```
({Point A} -> {Point B}) // Vector from A to B
({Point A} -> {Point B})/2+5 // Vector from A to B with magnitude divided by 2 plus 5
({Point A} -> {Point B})%5 // Vector from A to B with magnitude set to 5
({Point A} -> {Point B})%({Point C} -> {Point D})
//^^ Vector from A to B with Vector from C to D's magnitude
```
e.g.
```
2-3:(2->4)/2+5.5
8-15:([1->14)%0.5
3-4:(1->3)%(1->2)
```
#### Squaring Off
If you need to square off the point created by the instruction you can end it with | followed by 'SA' (Square across) or 'SD' (Square down) optionaly followed by '^' and a reference string for the generated line

If you want to square off the line created just is 'S' (Square)
. e.g.
```
2-3:5#-90|SA^back //Creates Line (2,3) and ExtLine (2,0,"back")
3-6|SD // Creates line (3,6) and ExtLine (6,90,null)
15-8|S // Creates line (15,8) and ExtLine(15,(15,8)'s angle + 90, null )
```
### Point

Each instruction that creates a point begins with a point reference followed by a ':' and a method to create a point e.g.
```
2:{Something Here}
```

#### Intersection

To generate the point at the intersection of two lines use the format:
```
[{Line A}, {Line B}]
```
e.g.
```
6 : [underarm_line,CF]
```
*Works with Lines and ExtLines*
#### MidPoint
To generate the point that is a given ratio along a line use the format:
```
[{Line}~{Ratio as Float}]
```
e.g.
```
[underarm_line~0.5] //Returns center point of underarm line
```
*Only works with Lines*
### Functions

Lengths can be created using the oporators +,-,*,/ in the same way they act on Floats in JavaScript

Vector addition can be applied to a Point, both points of a Line or the origin point of an ExtLine using the + oporator followed by a Vector declared using () e.g.
```
7_11+(0,2)
TEMP_LINE+(4->6)
```

## Example Code

```
///////////Bodice Block////////////
N_W //Nape to waist
BUST:10
BACK:1.6 //Back width
SHOULDER
CHEST:0.6 //Chest width
Waist:4
N_B //Neck Base Circumference
B_S // Bust Seperation

///////////Instructions/////////////
0-1^CB : N_W+2#90 | SA^waist_line
0-2 : BUST/2+0.5#0 | SD^CF
0-3 : 2#90 |SA
3-4 : (3->1)+4 | SA^bust_line
4-5 : 3#-90 | SA^underarm_line
6 : [underarm_line,CF]
5-7 : (5->3)/2^X_back_line
0-8 : NB/5-0.2#0 | SD
//Need to add support for ExtLines that only extend to eachothers intersection and 
//for curved lines to complete step 0-8, 2-9, 2-10 and 7-11 (probably more to come)
//possibly add square to line option
2-9 : N_B/5-1.6#180 | SD
2-10 : N_B/5+0.2#90 | SA 
7-11 : BACK/2#0 | SD //Should be square down to underarm_line
3-13 : (3->7)/3-0.4 //Instructions say square across but next line makes that redundant
13-14 : (7->11)+2.4
8-14^provisional_back_shoulder
14-15 : (14->8)%SHOULDER+0.5 | S^TEMP_to_17
15-16 : (15->8)%1.4
// Point 17 is the intersection of the line perpendicular to 15-14 through 15
// and the line 2 cm up from x back line
17 : [TEMP_to_17,X_back_line+(0,-2)] | SD^back_dart_center
15-17
16-17
10-18 : (10->6)/2+2 | SA
18-19 : CHEST/2+2.2#180 | SD
10-20 : (18->19)+4.3
20-21 : 0.3#-90
9-21^provisional_front_shoulder
22 : [bust_line,CF+(-B_S,0)]
21-23 : (21->9)%(14->15)
22-23
//Need to add support for extending past line as is required fro step 22-23 and 22-24
23-24 : (23->9)%BUST/16-0.3
22-24
25 : [underarm_line~1/2] | SD^provisional_side_seam
27 : [waist_line,CF]
1-27^TEMP_waist_line
26 : [TEMP_waist_line~0.5]
27-28 : 1.3#90

////////Shape Arm Hole///////////
//Requires lots of curves I am going to gloss over as straight lines for now
14~11
//Next instruction requires curve through 11-12-25-29-20
//with each curve either being offset from a straightline by a certain amount
//at that line's mid point or doing a bezier kind athing where is goes from 
//point a to point b curving towards a corner point.
//need to think about best way to add this
11~12
12~25
25~19
19~20

////////Bodice Waist Reduction/////////////
//Ask lauren about this I don't really understand it

////////Position Darts on line 1-28/////////
// may need darting specific functions
//following is sudo code
topPoint : [back_dart_center,bust_line]
dart from topPoint around back_dart_center to line 1-28 4.5 cm wide
etc.
```