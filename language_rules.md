# Language Rules
*For more info in the general goals see [README](README.md).*

## Plan
*Note Tags are currently not that well thought out, this is just a starting point and needs a lot of work*

### Commenting

// is used to comment out the rest of the line just like in JavaScript.

### Line Syntax

All lines end with ";" like in JavaScript.

### Data Types
To begin there are five data types I can foresee being necessary, not including those related to lists:

| Pattern component | Computer representation | Constructor | Example |
| ----------- | ----------- | ----------- | ----------- |
| Length (cm) | Positive Float | {Digit} | 2 or 4.5 or .9 |
| Point/Vector | (Float, Float) | ({Something}) | (4,6) or (a->b) |
| Line | (Point, Point) | [{Something}] | [(5,6),a] or [a->b] |
| Text | String | "{String}" | "Chest Width" |
| Tag | ( Point or Line , String) | <{Point or Line},{Text or Length}>| <(6,.5),"Neck Line"> |

\* *Constructors will be explained later* 

\*\* *Tag may need to store more data for label placing*

There are two data types ralated to lists:

| Pattern component | Computer representation | Constructor | Example |
| ----------- | ----------- | ----------- | ----------- | 
| Id | Non-Negative Integer or String | {Digit} or {String} only ever found after "*" function | *5 or *back_line |
| List | String | String | {Capitalised String}| Points or Tags |

Rather than having declarable lists there will only be one list for each data type and a second Length list for the input measurements. The idea behind this is that after the script has been run the values in those lists in a .json file will be the output pattern to then be rendered.

A list entry will look like this:
{id}|{value}. 

Where id is an Id, String or Null, value is of the type of the list. any value representing a function, list or data type is a protected word and can't be used.

Lists:
| Name | Data Type | Allowed ids |
| ----------- | ----------- | ----------- |
| Measures (Cannot be modified) | Lengh | All caps String | 
| Lengths | Length | String |
| Points | Point\Vector | Id, String or Null |
| Lines | Line | Id, String or Null |
| Text | Text | String |
| Tags | Tag | ?Not sure yet? |
| Vars (Not added to output) | Any | String |

### List Functions

There are two list functions:

 "\*" gets the element with the id to the right of it from the list to the left. Null id's can't be referenced e.g.
 ```
Lines*5 //Get element 5 from Lines
Lengths*diag // Get element diag from Lengths
*8 // No id assumes taking from Points unless...
*WAIST // All caps id assumes taking from list Measures
Vars*half_waist // reference temporary variable half_waist
 ```

 "=>" takes the element to the left and stores it in the location referenced on the right. This ends the line. This is the only function that returns nothing and the only one that edits the output.
 ```
Lines*5 => Lines*6; //coppies the line at 5 into 6
(5,8) => *1; //Puts point (5,8) into Points id 1
Lines*back => ;// puts back into Lines with id null
*6 => ;// Puts points id 6 into Points with id null
10 => Vars*ten ;// Put 10cm into temporary variable ten
 ```

### Other Functions

| Name | Character | Synntax| Output |
| ----------- | ----------- | ----------- | ----------- |
| Vector Add | : | {Point A}:{Point B} | Vector addition of A and B |
| Rotate | . | {Vector}.{Angle - technicaly a length} | Vector rotated by angle |
| Set Mag | ^ | {Vector}^{Length} | Vector with same angle and Length as magnitude |
| Scalar Mult | x | {Point}x{Length} | Scalar multiplication of Point by Length |
| Scalar Division | / | {Point}/{Length} | Scalar multiplication of Point by 1/Length |
| Magnitude Add | x | {Point}x{Length} | POint as a Vector with magnitude + Length |
| Mult | x | {Length A}x{Length B} | A times B |
| Div | / | {Length A}/{Length B} | A divided by B |
| Add | + | {Length A}+{Length B} | A plus B |
| Sub | - | {Length A}-{Length B} | A minus B |

### Constructors

#### Point/Vector

| Syntax | Description | 
| ----------- | ----------- | 
| (Length,Length) | Point at (x,y) |
| (Length) | Equivilent to (Length,0) |
| (Length->Length) | Vector from first length to second |
| (Line \| Line) | The intersection of two lines extended (If they are parallel return (0)|

#### Line

| Syntax | Description | 
| ----------- | ----------- | 
| [Point,Point] | Line from first point to second |

#### Tag

To be worked out

### Initialisation

Each program starts with a comma seperated list of input measurements surrounded by {} e.g.
```
{
    NW, // Naip to Waist
    BUST,
    SHOULDER
};
```

## Example Code
*"///////Something//////// comments denote what part of the reference instructions this part of code represents*
```
/////////Bodice Block//////////
////////Measurements Layed Out//////////
{
    NW, // Naip to waist
    BUST,
    BACK,
    SHOULDER,
    CHEST,
    WAIST,
    NBC // Neck base circumference
}
/////////Square lines out from 0///////
(0) => *0;
(WAIST+2).90 => *1;
[*0,*1] => ;
(*BUST/2+.5) => *2;
[*0,*2] => ;
*2:*1 => Vars*corner;
[*1,Vars*corner] => ;
[*2, Vars*corner] => *cf;
(2).90 => *3; square once we have 8
*3:(*3:*1)/2+4 => *4;
[*4,([*4,*4:(1)]|Vars*cf)] =>; // Squaring off at angle
                            Consider making function
*4:(3).270 => *5;
([*5,*5:(1)]|Vars*cf) => *6;
[*5,*6] => ;// Squaring off at angle
*5:(*5->*3)/2 => *7;
///////For back neck///////////
*NBC/5-.16 => *8;
([*3,*3:(1)]|[*8,*8:(1).90]) => Vars*temp; // Squaring off at angle
[*3,temp] => ;
[*8,temp] => ;
////////For front neck/////////

TODO: continue
```

### Reference Page
![Reference Page](images/example_instruction_page.jpg)
