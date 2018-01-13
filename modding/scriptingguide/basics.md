## Scripting Basics
This tutorial does not cover general programming concepts like Variables, Functions, Arrays, etc. Consider reading [this](http://wiki.modsrepository.com/index.php?title=Call_of_Duty_4:_CoD_Script_Handbook) first if you have no clue about programming at all.

### Language Features

Describes how some not so basic language concepts are used in GSC.

#### Supported Operators
* +, -, *, /
* % modulo
* !, <, >, <=, >=
* <<, >> leftshift and rightshift respectively

#### Supported Types
* int (32 bit signed)
* float (32 bit, IEEE 754)
* string
* array/dictionary
* struct

#### Builtin Functions / Keywords
* for, while, do-while
* break, continue, return
* if, else, switch-case
* wait, waittill, waittillframeend

#### Context
Functions can be run inside a context. The context of a function can be referenced with the **self** keyword from within the function.

```C
SayHelloToPlayer()
{
    self printlnbold("Hello " + self.name);
}

...

player SayHelloToPlayer();
```

#### Function Pointers
Usually you can only define and call functions. However, function pointers let you take a function and store it into a variable for later use. A great example for the use of function pointers is **_callbacksetup.gsc** which was used in the previous chapter. 

Assignment of a function pointer: 
```C
foo() { ... }
bar = ::foo;
```

Assign a function inside another file to a function pointer: 
```C
// foo now is defined in a scriptfile inside a folder main_shared/myscripts/script.gsc
bar = myscripts\script::foo;
```

To call function pointers we use double brackets:
```C
bar = myscripts\script::foo;
[[bar]](); // You can define arguments inside '()' parens if you need to.
```

#### Events
You can emit and wait for events. This enables easy communication between different threads in your code.

Example:
```C
player notify("player_iscamping"); // called when a player was detected camping

...

player thread watchCamping(); // called when the player connects

...

watchCamping()
{
    self endon("disconnect");

    for(;;)
    {
        self waittill("player_iscamping");
        self iprintlnbold("You filthy camper!");
        // do something cruel to the player because he's camping
    }
}
```

Notify with parameter:
```C
notify("player_iscamping", 5.0);
```

Waittill event with parameters:
```C
player waittill("player_iscamping", camptime);
```

Full list of built-in script notifies you can get [here](https://github.com/callofduty4x/CoD4x_Server/blob/master/scriptdocumentation/SCRIPT_NOTIFIES.md).


#### Threads
Threads are used to execute multiple functions in parallel. No worries, you don't have to deal with concurreny and synchronization in GSC. Threads are implemented as Fibers aka resumable functions, and are executed sequentially. 

Starting a new thread:
```C
foo() { ... }
thread foo();
```

End a thread at event
```C
foo()
{
  endon("disconnect");
  
  for(;;) // do something in infinite loop
  {
    ...
  }
}

player thread foo(); // threads gets terminated when player disconnects
```

> Protip: Threads are running until they are paused by wait(seconds) or waittill(event). In case you have many heavy threads you can loadbalance them between serverframes.

> Use `waittillframeend;` to pause the thread and keep executing it on the next serverframe. Rarely needed, better use `wait 0.05;`.

#### Referencing external functions

As your code grows bigger you want to split your code into multiple files. As briefly mentioned in the chapter about function pointers (`bar = myscripts\script::foo;`), we can reference external functions via `<foldername>\<scriptfilename>\<functionname>\`. But as you like know it from the C programming lanuage GSC also has a preprocessor. The **#include** directive can be used to insert contents of a gsc file into another one. 

One of the most commonly included files is probably `#include maps\mp\gametypes\_hud_util;`. Which contains different functions for creating serverside hudelements. One of such functions is `setPoint(...)`. Without the **#include** preprocessor directive we would call the function as `maps\mp\gametypes\_hud_util::setPoint(...);`. By adding the include directive as stated before we can shorten this to `setPoint(...);`.
