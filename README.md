# The Python3 Pyed Piper

A modern pythonic replacement for sed/awk and other unix command line manipulation.
This is a fork from the original that supports python3.

## Original

The original pyp was published at [PyCon 2012](https://pycon2012-notes.readthedocs.io/en/latest/pyed-piper.html) and (now unmaintained) is hosted at [Google Code](https://code.google.com/archive/p/pyp/). All respect to original author Toby Rosen of Sony Imageworks.

## Piping Python Through Pipes

Pyp is a linux command line text manipulation tool similar to awk or sed, but which uses standard python string and list methods as well as custom functions evolved to generate fast results in an intense production environment. Pyed Pyper was developed at Sony Pictures Imageworks to facilitate the construction of complex image manipulation "one-liner" commands during visual effects work on Alice in Wonderland, Green Lantern, and the The Amazing Spiderman.

Because pyp employs it's own internal piping syntax ("|") similar to unix pipes, complex operations can be proceduralized by feeding the output of one python command to the input of the next. This greatly simplifies the generation and troubleshooting of multistep operations without the use of temporary variables or nested parentheses. The variable "p" represents each line as a string, while "pp" is entire input as python list: \> ls | pyp "p[0] | pp.sort() | p + ' first letter, sorted!'" #gives sorted list of first letters of every line

In practice, the ability to easily construct complicated command sequences can largely replace "for each" loops on the command line, thus significantly speeding up work-flow using standard unix command recycling.

pyp output has been optimized for typical production scenarios. For example, if text is broken up into an array using the "split()" method, the output will be automatically numbered by field making selecting a particular field trivial. Numerous other conveniences have been included, such as an accessible history of all inter-pipe sub-results, an ability to perform mathematical operations, and a complement of variables based on common metacharcter split/join operations.

For power users, commands can be easily saved and recalled from disk as macros, providing an alternative to quick and dirty scripting. For the truly advanced user, additional methods can be added to the pyp class via a config file, allowing tight integration with larger facilities data structures or custom toolsets.

## Short help (pyp --help)

pyp is a python-centric command line text manipulation tool.  It allows you to format, replace, augment and otherwise mangle text using standard python syntax with a few golden-oldie tricks from unix commands of the past. You can pipe data into pyp or cut and paste text, and then hit ctrl-D to get your input into pyp.

After it's in, you can use the standard repertoire of python commands to modify the text. The key variables are "p", which represents EACH LINE of the input as a PYTHON STRING.  and "pp", which represents ALL of the inputs as a PYTHON ARRAY.

You can pipe data WITHIN a pyp statement using standard unix style pipes ("|"), where "p" now represents the evaluation of the python statement before the "|". You can also refer back to the ORIGINAL, unadulterated input using the variable "o" or "original" at any time...and the variable "h" or "history" allows you to refer back to ANY subresult generated between pipes ("|").

All pyp statements should be enclosed in double quotes, with single quotes being used to enclose any strings.
```
     echo 'FOO IS AN ' | pyp "p.replace('FOO','THIS') | p + 'EXAMPLE'"
       ==> THIS IS AN EXAMPLE
```
Splitting text on metacharacters is often critical for picking out particular fields of interest, so common SPLITS and JOINS have been assigned variables. For example, "underscore" or "u" will split a string to an array based on undercores ("_"), while "underscore" or "u" will ALSO join an array with underscores ("_") back to a string.

Here are a few key split/join variables; run with --manual for all variable and see examples below in the string section.
```
    s OR slash           splits AND joins on "/"
    u OR underscore      splits AND joins on "_"
    w OR whitespace      splits on whitespace (spaces,tabs,etc) AND joins with spaces
    a OR all             splits on ALL metacharacters [!@#$%^&*()...] AND joins with spaces
```
```
EXAMPLES:
------------------------------------------------------------------------------
              List Operations              # all python list methods work
------------------------------------------------------------------------------

print all lines                              ==> pyp  "pp"
sort all input lines                         ==> pyp  "pp.sort()"
eliminate duplicates                         ==> pyp  "pp.uniq()"
combine all lines to one line                ==> pyp  "pp.oneline()"
print line after FOO                         ==> pyp  "pp.after('FOO')"
list comprehenision                          ==> pyp  "[x for x in pp]"
return to string context after sort          ==> pyp  "pp.sort() | p"

-------------------------------------------------------------------------------
            String Operations               # all python str methods work
-------------------------------------------------------------------------------

print line                                   ==> pyp  "p"
combine line with FOO                        ==> pyp  "p +'FOO'"
above, but combine with original input       ==> pyp  "p +'FOO'| p + o"

replace FOO with GOO                         ==> pyp  "p.replace('FOO','GOO')"
remove all GOO and FOO                       ==> pyp  "p.kill('GOO','FOO')"

string substitution                          ==> pyp  "'%s FOO %s %s GOO'%(p,p,5)"

split up line by FOO                         ==> pyp  "p.split('FOO')"
split up line by '/'                         ==> pyp  "slash"
select 1st field split up by '/'             ==> pyp  "slash[0]"
select fields 3 through 5 split up by '/'    ==> pyp  "s[2:6]"
above joined together with '/'               ==> pyp  "s[2:6] | s"

-------------------------------------------------------------------------------
            Logic Filters                   # all python Booleon methods work
-------------------------------------------------------------------------------

keep all lines with GOO and FOO              ==> pyp  "'GOO' in p and 'FOO' in p"
keep all lines with GOO or FOO               ==> pyp  "keep('GOO','FOO')"
keep all lines that are numbers              ==> pyp  "p.isdigit()"

lose all lines with GOO and FOO              ==> pyp  "'GOO' not in p and 'FOO' not in p"
lose all lines with GOO or FOO               ==> pyp  "lose('GOO','FOO')"
lose all lines that are numbers              ==> pyp  "not p.isdigit()"

-------------------------------------------------------------------------------
TO SEE EXTENDED HELP, use --manual
```
```

Options:
  -h, --help            show this help message and exit
  -m, --manual          prints out extended help
  -l, --macro_list      lists all available macros
  -s MACRO_SAVE_NAME, --macro_save=MACRO_SAVE_NAME
                        saves current command as macro. use "#" for adding
                        comments  EXAMPLE:    pyp -s "great_macro # prints
                        first letter" "p[1]"
  -f MACRO_FIND_NAME, --macro_find=MACRO_FIND_NAME
                        searches for macros with keyword or user name
  -d MACRO_DELETE_NAME, --macro_delete=MACRO_DELETE_NAME
                        deletes specified public macro
  -g, --macro_group     specify group macros for save and delete; default is
                        user
  -t TEXT_FILE, --text_file=TEXT_FILE
                        specify text file to load. for advanced users, you
                        should typically cat a file into pyp
  -x, --execute         execute all commands.
  -a EXECUTE_AUX, --execute_aux=EXECUTE_AUX
                        execute commands with auxillary data with custom
                        executer
  -c, --turn_off_color  prints raw, uncolored output
  -u, --unmodified_config
                        prints out generic PypCustom.py config file
  -b BLANK_INPUTS, --blank_inputs=BLANK_INPUTS
                        generate this number of blank input lines; useful for
                        generating numbered lists with variable 'n'
  -n, --no_input        use with command that generates output with no input;
                        same as --blank_inputs 1
  -i, --ignore_blanks   remove blank lines from output
  -k, --keep_false      print blank lines for lines that test as False.
                        default is to filter out False lines from the output
  -r, --rerun           rerun based on automatically cached data from the last
                        run. use this after executing "pyp", pasting input
                        into the shell, and hitting CTRL-D TWICE
 ```
 
## Full Manual (pyp --manual)
 
pyp is a command line utility for parsing text output and generating complex
unix commands using standard python methods. pyp is powered by python, so any
standard python string or list operation is available.

The variable "p" represents EACH line of the input as a python string, so for
example, you can replace all "FOO" with "GOO" using "p.replace('FOO','GOO')".
Likewise, the variable "pp" represents the ENTIRE input as a python array, so
to sort the input alphabetically line-by-line, use "pp.sort()"

Standard python relies on whitespace formating such as indentions. Since this
is not convenient with command line operations, pyp employs an internal piping
structure ("|") similar to unix pipes.  This allows passing of the output of
one command to the input of the next command without nested "(())" structures.
It also allows easy spliting and joining of text using single, commonsense
variables (see below).  An added bonus is that any subresult between pipes
is available, making it easy to refer to the original input if needed.

Filtering output is straightforward using python Logic operations. Any output
that is "True" is kept while anything "False" is eliminated. So "p.isdigit()"
will keep all lines that are completely numbers.

The output of pyp has been optimized for typical command line scenarios. For
example, if text is broken up into an array using the "split()" method, the
output will be conveniently numbered by field because a field selection is
anticipated.  If the variable  "pp" is employed, the output will be numbered
line-by-line to facilitate picking any particular line or range of lines. In
both cases, standard python methods (list[start:end]) can be used to select
fields or lines of interest. Also, the standard python string and list objects
have been overloaded with commonly used methods and attributes. For example,
"pp.uniq()" returns all unique members in an array, and p.kill('foo') will
eliminate all  "foo" in the input.

pyp commands can be easily saved to disk and recalled using user-defined macros,
so a complicated parsing operation requiring 20 or more steps can be recalled
easily, providing an alternative to quick and dirty scripts. For more advanced
users, these macros can be saved to a central location, allowing other users to
execute them.  Also, an additional text file (PypCustom.py) can be set up that
allows additional methods to be added to the pyp str and list methods, allowing
tight integration with larger facilities data structures or custom tool sets.

### PIPING IN THE PIPER

You can pipe data WITHIN a pyp statement using standard unix style pipes ("|"),
where "p" now represents the evaluation of the python statement before the "|".
You can also refer back to the ORIGINAL, unadulterated input using the variable
"o" or "original" at any time...and the variable "h" or "history" allows you
to refer back to ANY subresult generated between pipes ("|").

All pyp statements should be enclosed in double quotes, with single quotes being
used to enclose any strings.
```
   echo 'FOO IS AN ' | pyp "p.replace('FOO','THIS') | p + 'EXAMPLE'"
     ==> THIS IS AN EXAMPLE
```

### THE TYPE OF COLOR IS THE TYPE

pyp uses a simple color and numerical indexing scheme to help you identify what
kind of objects you are working with. Don't worry about the specifics right now,
just keep in mind that different types can be readily identified:

```
strings:                 hello world

integers or floats:      1984

split-up line:           [[0]hello[1]world] 

entire input list:       [0]first line
                       [1]second line

dictionaries:            {hello world: 1984}

other objects:           RANDOM_OBJECT
```

The examples below will use a yellow/blue color scheme to seperate them
from the main text however. Also, all colors can be removed using the
--turn_off_color flag.

### STRING OPERATIONS

Here is a simple example for splitting the output of "ls" (unix file list) on '.':
```
  ls random_frame.jpg | pyp "p.split('.')"
      ==>  [[0]random_frame[1]jpg] 
```
The variable "p" represents each line piped in from "ls".  Notice the output has
index numbers, so it's trivial to pick a particular field or range of fields,
i.e. pyp "p.split('.')[0]"  is the FIRST field.  There are some pyp generated
variables that make this simpler, for example the variable "d" or "dot" is the
same as p.split('.'):
```
  ls random_frame.jpg | pyp "dot"
      ==> [[0]random_frame[1]jpg]

  ls random_frame.jpg | pyp "dot[0]"
      ==>   random_frame
```
To Join lists back together, just pipe them to the same or another built-in
variable(in this case "u", or "underscore"):
```
  ls random_frame.jpg | pyp "dot"
      ==> [[0]random_frame[1]jpg]

  ls random_frame.jpg | pyp "dot|underscore"
      ==> random_frame_jpg 
```
To add text, just enclose it in quotes, and use "+" or "," just like python: 
```
  ls random_frame.jpg | pyp "'mkdir seq.tp_' , d[0]+ '_v1/misc_vd8'"
      ==> mkdir seq.tp_random_frame_v1/misc_vd8'" 
```
A fundamental difference between pyp and standard python is that pyp allows you
to print out strings and lists on the same line using the standard "+" and ","
notation that is used for string construction. This allows you to have a string
and then print out the results of a particular split so it's easy to pick out
your field of interest: 
```
  ls random_frame.jpg | pyp "'mkdir', dot"
   ==> mkdir [[0]random_frame[1]jpg] 
```
In the same way, two lists can be displayed on the same line using "+" or ",".
If you are trying to actually combine two lists, enclose them in parentheses:
```
  ls random_frame.jpg | pyp "(underscore + dot)"
  ==> [[0]random[1]frame.jpg[2]random_frame[3]jpg] 
```
This behaviour with '+' and ',' holds true in fact for ANY object, making
it easy to build statements without having to worry about whether they
are strings or not.

### ENTIRE INPUT LIST OPERATIONS

To perform operations that operate on the ENTIRE array of std-in, Use the variable
"pp", which you can manipulate using any standard python list methods. For example,
to sort the input, use:
```
 pp.sort()
```
When in array context, each line will be numbered with it's index in the array,
so it's easy to, for example select the 6th line of input by using "pp[5]".
You can pipe this back to p to continue modifying this input on a
line-by-line basis: 
```
 pp.sort() | p  
```
You can add arbitrary entries to your std-in stream at this point using
list addition. For example, to add an entry to the start and end:
```
 ['first entry']  +  pp  +  ['last entry']  
```
The new pp will reflect these changes for all future operations.

There are several methods that have been added to python's normal list methods
to facilitate common operations. For example, keeping unique members or
consolidating all input to a single line can be accomplished with: 
```
 pp.uniq()
 pp.oneline()
```
Also, there are a few useful python math functions that work on lists of
integers or floats like sum, min, and max. For example, to add up
all of the integers in the last column of input: 
```
 whitespace[-1] | int(p) | sum(pp) 
```
### MATH OPERATIONS

To perform simple math, use the integer or float functions  (int() or float())
AND put the math in "()" + 
```
  echo 665 | pyp "(int(p) + 1)"
     ==> 666 
```
### LOGIC FILTERS

To filter output based on a python function that returns a Booleon (True or False),
just pipe the input to this function, and all lines that return True will keep
their current value, while all lines that return False will be eliminated. 
```
  echo 666 | pyp  "p.isdigit()"
     ==> 666
```
Keep in mind, that if the Boolean is True, the entire value of p is returned.
This comes in handy when you want to test on one field, but use something else.
For example, a[2].isdigit() will return p, not a[2] if a[2] is a digit.

Standard python logic operators such as "and","or","not", and 'in' work as well.

For example to filter output based on the presence of "GOO" in the line, use this:
```
  echo GOO | pyp "'G' in p"
     ==> GOO
```
The pyp functions "keep(STR)" and "lose(STR)", and their respective shortcuts,
"k(STR)" and "i(STR)", are very useful for simple OR style string
filtering. See below.

Also note, all lines that test False ('', {}, [], False, 0) are eliminated from
the output completely. You can instead print out a blank line if something tests
false using --keep_false. This is useful if you need placeholders to keep lists
in sync, for example.

### SECOND STREAM, TEXT FILE, AND BLANK INPUT

Normally, pyp receives its input by piping into it like a standard unix shell
command, but sometimes it's necessary to combine two streams of inputs, such as
consolidating the output of two shell commands line by line.  pyp provides
for this with the second stream input. Essentially anything after the pyp
command that is not associated with an option flag is brought into pyp as
the second stream, and can be accessed seperately from the primary stream
by using the variable 'sp'

To input a second stream of data, just tack on strings or execute (use backticks)
a command to the end of the pyp command, and then access this array using the
variable 'sp'  
```
  echo random_frame.jpg | pyp "p, sp" `echo "random_string"`
     ===> random_frame.jpg random_string
```
In a similar way, text input can be read in from a text file using the
--text_file flag. You can access the entire file as a list using the variable
'fpp', while the variable 'fp' reads in one line at a time. This text file
capability is very useful for lining up std-in data piped into pyp with
data in a text file like this:
```
  echo normal_input | pyp -text_file example.txt "p, fp" 
```
This setup is geared mostly towards combining data from std-in with that in
a text file.  If the text file is your only data, you should cat it, and pipe
this into pyp.

If you need to generate output from pyp with no input, use --blank_inputs.
This is useful for generating text based on line numbers using the 'n'
variable.

### TEXT FILE AND SECOND STREAM LIST OPERATIONS

List operations can be performed on file inputs and second stream
inputs using the variables spp and fpp, respectively.  For example to sort
a file input, use: 
```
 fpp.sort() 
```
Once this operation takes place, the sorted fpp will be used for all future
operations, such as referring to the file input line-by-line using fp.

You can add these inputs to the std-in stream using simple list
additions like this: 
```
  pp + fpp 
```
If pp is 10 lines, and fpp is 10 line, this will result in a new pp stream
of 20 lines. fpp will remain untouched, only pp will change with this
operation.

Of course, you can trim these to your needs using standard
python list selection techniques: 
```
  pp[0:5] + fpp[0:5] 
```
This will result in a new composite input stream of 10 lines.

Keep in mind that the length of fpp and spp is trimmed to reflect
that of std-in.  If you need to see more of your file or second
stream input, you can extend your std-in stream simply:
```
  pp + ['']*10 
```
will add 10 blank lines to std-in, and thus reveal another 10
lines of fpp if available.

### MACRO USAGE

Macros are a way to permently store useful commands for future recall. They are
stored in your home directory by default. Facilites are provided to store public
macros as well, which is useful for sharing complex commands within your work group.
Paths to these text files can be reset to anything you choose my modifying the
PypCustom.py config file.  Macros can become quite complex, and provide
a useful intermediate between shell commands and scripts, especially for solving
one-off problems.  Macro listing, saving, deleting, and searching capabilities are
accessible using --macrolist, --macro_save, --macro_delete, --macro_find flags.
Run pyp --help for more details.

you can pyp to and from macros just like any normal pyp command. 
```
  pyp "a[0]| my_favorite_macros | 'ls', p" 
```
Note, if the macro returns a list, you can access individual elements using
[n] syntax:
```
  pyp "my_list_macro[2]" 
```
Also, if the macro uses %s, you can append a %(string,..) to then end to string
substitute: 
```
  pyp "my_string_substitution_macro%('test','case')" 
```
By default, macros are saved in your home directory. This can be modifed to any
directory by modifying the user_macro_path attribute in your PypCustom.py. If
you work in a group, you can also save macros for use by others in a specific
location by modifying group_macro_path. See the section below on custom
methods about how to set up this file.

### CUSTOM METHODS

pyed pyper relies on overloading the standard python string and list objects
with its own custom methods.  If you'd like to try writing your own methods
either to simplify a common task or integrate custom functions using a
proprietary API, it's straightforward to do. You'll have to setup a config
file first:
```
  pyp --unmodified_config > PypCustom.py
  sudo chmod 666 PypCustom.py
```
There are example functions for string, list, powerpipe, and generic methods.
to get you started. When pyp runs, it looks for this text file and automatically
loads any found functions, overloading them into the appropriate objects. You
can then use your custom methods just like any other pyp function.

### TIPS AND TRICKS

If you have to cut and paste data (from an email for example), execute pyp, paste
in your data, then hit CTRL-D.  This will put the data into the disk buffer. Then,
just rerun pyp with --rerun, and you'll be able to access this data for further
pyp manipulations!

If you have split up a line into a list, and want to process this list line by
line, simply pipe the list to pp and then back to p: pyp "w | pp |p"

Using --rerun is also great way to buffer data into pyp from long-running scripts

pyp is an easy way to generate commands before executing them...iteratively keep
adding commands until you are confident, then use the --execute flag or pipe them
to sh.  You can use ";" to set up dependencies between these commands...which is
an easy way to work out command sequences that would typically be executed in a
"foreach" loop.

Break out complex intermediate steps into macros. Macros can be run at any point in a
pyp command.

If you find yourself shelling out constantly to particular commands, it might
be worth adding python methods to the PypCustom.py config, especially if you
are at a large facility.

Many command line tools (like stat) use a KEY:VALUE format. The shelld function
will turn this into a python dictionary, so you can access specific data using
their respective keys by using something like this: shelld(COMMAND)[KEY]

### HERE ARE THE BUILT IN VARIABLES:

  STD-IN (PRIMARY INPUT)
  p        line-by-line std-in variable. p represents whatever was
           evaluated to before the previous pipe (|).

  pp       python list of ALL std-in input. In-place methods like
           sort() will work as well as list methods like sorted(LIST)

  SECOND STREAM
  sp       line-by-line input second stream input, like p, but from all
           non-flag arguments AFTER pyp command: pyp "p, sp" SP1 SP2 SP3 ...

  spp      python list of ALL second stream list input. Modifications of
           this list will be picked up with future references to sp

  FILE INPUT
  fp       line-by-line file input using --text_file TEXT_FILE. fp on
           the first line of output is the first line of the text file

  fpp      python list of ALL text file input. Modifications of
           this list will be picked up with future references to fp


  COMMON VARIABLES
  original original line by line input to pyp
  o        same as original

  quote    a literal "      (double quotes can't be used in a pyp expression)
  paran    a literal '
  dollar   a literal $

  n        line counter (1st line is 0, 2nd line is 1,...use the form "(n+3)"
           to modify this value. n changes to reflect filtering and list ops.
  nk       n + 1000

  date     date and time. Returns the current datetime.datetime.now() object.
  pwd      present working directory

  history  history array of all previous results:
             so pyp "a|u|s|i|h[-3]" shows eval of s
  h        same as history

  digits   all numbers [0-9]
  letters  all upper and lowercase letters (useful when combined with variable n).
           letters[n] will print out "a" on the first line, "b" on the second...
  punctuation all punctuation ```[!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~]```


THE FOLLOWING ARE SPLIT OR JOINED BASED ON p BEING A STRING OR AN ARRAY:
```
  s  OR slash          p split/joined on "/"
  d  OR dot            p split/joined on "."
  w  OR whitespace     p split/joined on whitespace (on spaces,tabs,etc)
  u  OR underscore     p split/joined on '_'
  c  OR colon          p split/joined on ':'
  mm OR comma          p split/joined on ','
  m  OR minus          p split/joined on '-'
  a  OR all            p split on [' '-_=$...] (on "All" metacharacters)
```
Also, the ORIGINAL INPUT (history[0]) lines are split on delimiters as above, but
stored in os, od, ow, ou, oc, omm, om and oa as well as oslash, odot, owhitepace,
ocomma, ominus, and oall

### HERE ARE THE BUILT IN FUNCTIONS AND ATTRIBUTES:
```
 Function                Notes
 --------------------------------------------------------------------------------
      STRING     (all python STRING methods like p.replace(STRING1,STRING2) work)
 --------------------------------------------------------------------------------
  p.digits()           returns a list of contiguous numbers present in p
  p.letters()          returns a list of contiguous letters present in p
  p.punctuation()      returns a list of contiguous punctuation present in p

  p.trim(delimiter)    removes last field from string based on delimiter
                       with the default being "/"
  p.kill(STR1,STR2...) removes specified strings
  p.clean(delimeter)   removes all metacharacters except for slashes, dots and
                       the joining delimeter (default is "_")
  p.re(REGEX)          returns portion of string that matches REGEX regular
                       expression.
  p.rereplace(REG,STR) find REG regular expression and replace with STR.
                       this is more reliable than p.replace(p.re(REGEX),STR)

  p.dir                directory of path
  p.file               file name of path
  p.ext                file extension (jpg, tif, hip, etc) of path

  These fuctions will work with all pyp strings eg: p, o, dot[0], p.trim(), etc.
  Strings returned by native python functions (like split()) won't have these
  available, but you can still access them using str(STRING). Basically,
  manually recasting anything using as a str(STRING) will endow them with
  the custom pyp methods and attributes.

 --------------------------------------------------------------------------------
      LIST        (all LIST methods like pp.sort(), pp[-1], and pp.reverse() work)
 --------------------------------------------------------------------------------
 pp.delimit(DELIM)     split input on delimiter instead of newlines
 pp.divide(N)          consolidates N consecutive lines to 1 line.
 pp.before(STRING, N)  searches for STRING, colsolidates N lines BEFORE it to
                       the same line. Default N is 1.
 pp.after(STRING, N)   searches for STRING, colsolidates N lines AFTER  it to
                       same line. Default N is 1.
 pp.matrix(STRING, N)  returns pp.before(STRING, N) and pp.after(STRING, N).
                       Default N is 1.
 pp.oneline(DELIM)     combines all list elements to one line with delimiter.
                       Default delimeter is space.
 pp.uniq()             returns only unique elements
 pp.unlist()           breaks up ALL lists up into seperate single lines

 pp + [STRING]         normal python list addition extends list
 pp + spp + fpp        normal python list addition combines several inputs.
                       new input will be pp; spp and fpp are unaffected.
 sum(pp), max(pp),...  normal python list math works if pp is properly cast
                       i.e. all members of pp should be integers or floats.

 These functions will also work on file and second stream lists:  fpp and spp

 --------------------------------------------------------------------------------
      NATIVE PYP FUNCTIONS
 --------------------------------------------------------------------------------
 keep(STR1,STR2,...)   keep all lines that have at least one STRING in them
 k(STR1,STR2,...)      shortcut for keep(STR1,STR2,...)
 lose(STR1,STR2,...)   lose all lines that have at least one STRING in them
 l(STR1,STR2,...)      shortcut for lose(STR1,STR2,...)

 rekeep(REGEX)         keep all lines that match REGEX regular expression
 rek(REGEX)            shortcut for rekeep(REGEX)
 relose(REGEX)         lose all lines that match REGEX regular expression
 rel(REGEX)            shortcut for relose(REGEX)

 shell(SCRIPT)         returns output of SCRIPT in a list.
 shelld(SCRIPT,DELIM)  returns output of SCRIPT in dictionary key/value seperated
                       on ':' (default) or supplied delimeter
 env(ENVIROMENT_VAR)   returns value of evironment variable using os.environ.get()
 glob(PATH)            returns globed files/directories at PATH. Make sure to use
                       '*' wildcard
 str(STR)              turns any object into an PypStr object, allowing use
                       of custom pyp methods as well as normal string methods.
```
### SIMPLE EXAMPLES:
```
 pyp "'foo ' + p"                 ==>  "foo" + current line
 pyp "p.replace('x','y') | p + o" ==>  current line w/replacement + original line
 pyp "p.split(':')[0]"            ==>  first field of string split on ':'
 pyp "slash[1:3]"                 ==>  array of fields 1 and 2 of string split on '/'
 pyp "s[1:3]|s"                   ==>  string of above joined with '/'
```
