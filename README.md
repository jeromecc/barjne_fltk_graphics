## Use Bjarne's sample graphics code on Debian GNU/Linux

Compile code samples in 3 easy steps.

### 1. Install FLTK packages

<code>apt-get install libfltk1.3-dev libfltk1.3-compat-headers</code>

### 2. clone the repository


### 3. compile

g++ -lfltk -lfltk_images *.cpp


### modifications

## headers

Headers were changed from "" to <>
For instance:
    \-    #include "FL/Fl.H"
    \+    #include <FL/Fl.H>

## conversion error in Graph.cpp
    Graph.cpp: In function ‘bool Graph_lib::can_open(const string&)’:
    Graph.cpp:313:9: error: cannot convert ‘std::ifstream {aka std::basic_ifstream<char>}’ to ‘bool’ in return
    return ff;

Fix:

    \-         return ff;
    \+         return static_cast<bool>(ff);


#### Narrowing conversion

Original code Color lcolor {(fl_color()}; gives warnings about narrowing unsigned int (the type returned by fl_color()) to int (the type of Color).                              
This hack works as long as you don't use RGB True Color see http://www.fltk.org/doc-1.3/drawing.html#drawing_colors
If you need RGB True Color, you'll have to find another fix or just disable warnings with: g++ -Wno-narrowing -lfltk -lfltk_images *.cpp

    \-    Color lcolor {(fl_color()};
    \+    Color lcolor {static_cast<int>(fl_color())};


    The C++11 standard does not allow "narrowing conversions" inside braced initialization lists, meaning conversions to a type with less precision or a smaller range, for example:


    int i = 127;
    char s[] = { i, 256 };

    In the above example the value 127 would fit in char but because it's not a constant it is still a narrowing conversion. If the value 256 is larger than CHAR_MAX then that is also a narrowing conversion. Narrowing conversions can be avoided by using an explicit cast, e.g. (char)i.
