# Language Rules
*For more info in the general goals see [README](README.md).*

## Plan

### Commenting and Spacing

// is used to comment out the rest of the line just like in JavaScript.

White spaces are ignored except for next line wich represents the next instruction.

### Data Types
There are 7 data types, the first two are for 'pointing' to data and are called IDs.

| Name | Data | Constructor |
| ----------- | ----------- | ----------- |
| Index | Non-Negative Int | *Number |
| StringID | String | *String |
| Length/Angle/Ratio | Float | Number |
| Point | Pair of FLoats | ( ) |
| Vector | Pair of Floats | < > |
| Line | Pair of Points | [ ] |
| Guide | Pair of Floats | { } |

All data is stored in one of 5 lists containing ID, Data pairs.
The Measures list contains the measurments input to the program.
Output lists are output by the program to go on to be rendered.

StringID's are all lower case except when used in Measures where they are all upper case.

Lists:
| Name | ID | Data Value | Input | Output |
| ----------- | ----------- | ----------- | ----------- | ----------- |
| Measures | StringID | Length | y | n |
| Vars | StringID | Any Data Type (Except IDs) | n | n |
| Points | Index | Point | n | y |
| Lines | StringID | Line | n | y |
| Guides | STringID | Guide | n | y |

### Get and Set

The Get function '\*' is the only function that can read from the lists. It returns the item from the list to it's left with the ID to it's right: List\*ID   
This is also how you construct IDs as this is the only context they exist in. 
When used after the set function the get function instead represents the 'location' ID in List.

The Set function '=>' is the only function that can write to the lists. It is preceded by data and followed by a 'location' reference as explained above. Data => List\*ID

Every line ends with a set statement. e.g.

```
    (0,0) => Points*0 //Add the origin to points index 0
    [Points*corner,(5,5)] => Line*back //Add the line from corner to 5,5 to line with value back
    {Points*corner,90} => Vars*cross // Add vertical line from corner as a temporary variable, cross

```

**Abreviations**

When Setting:

Points, Guides and Lines can be put into their exclusive lists without having to reference the list name. Just write '\*ID' to put the value in.
If you only want to output some data without a label, and don't need to reference it again, you can ommit the ID. Just write 'Data\*' to output the value.

When Getting:

When getting from Measures you can choose to ommit the list name as it can be identified by the capitalised name. e.g. '\*SHOULDER'

Similarly, when getting from Points, you my ommit the list name as this is the most commonly referenced list. e.g. '\*5'


```
    [*0,(4,4)] => *diagonal // store the line from point 0 to 4,4 in Lists named diagonal
    <*SHOULDER,90> => Vars*shldr_dwn // Store vector of length SHOULDERin a temporary variable
    [(0,0),(1,5)] => Lines* // Store this line in Lines with no reference
    (2,4) => * // Store this point in Points with no reference
```

### Constructors

With the exception of ID's Lengths Angles and Ratios who's construction has already been explained Data is constructed within a set of brackets. 
*The following is the bare minimum constructors to be expanded on*

- Point (Length,Length)
- Vector <Length,Angle>
- Line [Point,Point]
- Guide {Point,Angle}

Point:

- (Length,Length) - Gets Point at given x and y coordinates
- (Length) - Equivilent to (Length,Length)
- (Point->Point*Ratio+Length) - get the point on the line between the points ratio length along plus the Length

Vector:

-Vector <Length,Angle>

### Functions

| Name | Symbol | Syntax | Description |
| ----------- | ----------- | ----------- | ----------- |
| Add Scalar | + | Length+Length | Add together the two scalar values |
| Sub Scalar | - | Length-Length | Subtract the second scalar from the first |
| Mult Scalar | * | Length*Length | Multiply the two scalar values |
| Div Scalar | / | Length /Ratio | Divide the first scalar by the second|
| Add Vector | : | Point:Vector | Add the two as if they were vectors and return the result as a point |

### Initialisation

The first non-blank/commented out line of the code must contain a comma delimited list of all of the IDs of values to be input into Measures

e.g.
```
SHOULDER,CHEST,WAIST
```

## Example Code

```
/////////Bodice Block//////////
NAPE_WAIST,BUST,BACK,SHOULDER,CHEST,WAIST

(0) => *0
*0:<*NAPE_WAIST+2,90>=> *1
*0:<*BUST/2+.5,0> => *2
```

### Reference Page
![Reference Page](images/example_instruction_page.jpg)
