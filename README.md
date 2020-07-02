# Sewing Scripting Language

## Aim
The aim of this language is to provide a way to write down the steps layed out to produce sewing patterns, in a format that a computer can render, as you work your way through the writen instructions. The syntax is designed to have each section of code flow similarly to the flow of the writen instructions.
### An example pattern with instructions
![An example instruction set](images\example_instruction_page.jpg)
![An exampole pattern rendering](images\example_pattern_page.jpg)

## Why this method?
The most obvious way to represent one of these patterns (the method that first occured to me) was to simply store the coordinates of each point involved. This can become quite difficult once you account for how the shapes are formed based off measured lengths. I don't doubt that such a system would be possible (especially for the simpler patterns) however the link between these instructions and provided image and representation is not very direct. The conversion requires quite a lot of problem solving unique to each pattern and thus more room for error such as distances calculating wrong or points being missed out all together.

*Most importantly, where's the fun in that?*

## Design Philosohpy 
As stated before the main goal of the language is for it to be writen as similarly to the verbous instructions provided on the page. The compiler can then do the work from there. A secondary goal is for it ro be as simple as possible both to make it esier to understand and learn and because writing compilers is hard enough as it is.

You can fing the current Syntax documentation along with plans for future feeatures [here](language_rules.md).

## How to Use

Once there is an actual program to run instructions on, how to install, run etc. will go here

## Contributing

There is very little you can help contribute at the moment but feedback is appreciated.

## Built with

Currently planning to build with node (javascript is probably not the best language to use but it will be compiling to java script and I didn't want to bother with multiple languages).

## License
This project is licensed under the MIT License - see the LICENSE.md file for details.

## Acknowledgments
- Thank you to my flat mates who sew for their input