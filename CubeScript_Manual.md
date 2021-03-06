<i>By Kevin "Hirato Kirata" Meyer, stolen from [1](https://github.com/Hirato/lamiae/wiki/CubeScript)</i>

Lamiae inherits the scripting language as defined in cube 2, known as CubeScript. There are two very important things to note before getting started.

<strong>Firstly</strong>, CubeScript is incompatible with static word wrapping, a newline, as well as a `;` character denotes the end of a statement. So if you use static word wrapping, <strong>turn it off</strong> or configure your editor to only break after the characters, `[`, `(`, and `;`. Free reign is permitted during comments.

<strong>Secondly</strong>, your editor should be configured to open blocks via the characters, `[]()` on the <strong>same</strong> line. New lines are bad as they will terminate the statement and parse the contents of the brackets as a separate statement.

Whilst indenting doesn't matter to the parser, we would strongly advise using tabulators set to jumps of 4 spaces or higher.

If you're unfamiliar with CubeScript, I would also advise you to read this article a few times. Things do not progress from the most to least simple, though attempts were made to order them as such.

<h1 id="syntax-highlighting">
Syntax Highlighting

</h1>
A syntax highlighter is a really useful tool when writing code, and there are a few publicly available for CubeScript.

1.  <strong>Kate</strong> from KDE 4.10.x or newer ships with one.
    -   Users of 4.9.x or earlier can navigate to `Settings` `-->` `Configure` `Kate` `-->` `Open/Save` `-->` `Modes/Filetypes` `-->` `Download` `Highlight` `Files` to get updated highlighter files, including one for CubeScript

2.  <strong>TextMate, NotePad++, and SublimeText</strong> users can get one from [2](https://github.com/srbs/CubeScript-syntax-highlighting), or alternatively for <strong>NotePad++</strong>, from [3](https://github.com/IceflowRE/community/blob/ice_WIP/Cubescript_RE-N%2B%2B_UserLang.xml)
3.  A CubeScript major mode for <strong>Emacs</strong> can be acquired from [4](https://github.com/rspeele/cubescript-mode)

<h1 id="an-overview-of-cubescript">
An Overview of CubeScript

</h1>
The iteration of CubeScript shipped with Lamiae included a bytecode compiler, which grants is a great many additional features and enhanced flexibility, as well as a significant bonus to speed.

Everything in CubeScript is accessible as a string, and conversion between forms is done implicitly without raising any errors. If for example you needed a number and you passed the string `"bob"`, you will get the number 0. The bytecode compiler optimises out superfluous conversions, if it's stored internally as an integer, it won't do any conversions if an integer is requested.

CubeScript draws heavy influences from LISP, and inherits many features, including one absolutely killer feature, the ability for code to generate code.

Just so we have a typical hello world sample...

    echo Hello World!

<h2 id="comments">
Comments

</h2>
The most important feature of any language is to know how to add empty statements that don't do anything. With CubeScript that takes the form `//text` `here`, anything following two slashes is a comment and this makes the parser skip towards the next line, there is no means of continuing the line, such as C++ allows with `//` `text` `here` `\<newline>more` `text`

    //This is a comment
    //This is discarded and completely unused &quot;^
    echo &quot;This is not a comment.&quot;

<h2 id="aliases">
Aliases

</h2>
Aliases are essentially user defined variables and macros. These store intermediaries. While all CubeScript idents have a stack, aliases are the only type whose stack is pushed and popped during use.

This may very well go over your head, but this is mentioned first up since it is a very important aspect of CubeScript and will likely be the basis of just about everything you do with it.

Aliases can be defined and set through several ways. <strong>Firstly</strong> there is the command `alias` `name` `[body]` which defines an alias named after the first param and gives it the value given by the second param.

    alias myalias 5

The <strong>second</strong> method is a unique feature, the one and only existing infix operator in CubeScript, all other commands and operators use prefix notation. This invocation is of the form `name` `=` `body` and for all intents and purposes, this is equivalent to `alias` `name` `[body]`.

    myalias = [echo &quot;poodles&quot;]

Aliases can also be defined through the `push` and `local` commands, but this is more a side effect of their behaviour, `local` will be discussed later in the [Blocks](#blocks "wikilink") section.

push is of the form `push` `id` `value` `[body]` and pushes a value onto the alias's stack, which is used during the execution of the body. It pops the stack immediately afterwards, returning it to its previous value.

    myalias = 5
    push myalias 10 [
        echo $myalias
    ]
    echo $myalias

Anything except a number can be set to an alias, so you could set the most crazy things as an alias, you're not limited as long as it's not a number. And don't worry about the example, if it scares you, you will never encounter such code in the wild.

    [my
        alias
            ] = [
                echo &quot;stuff&quot;
            ]

This alias's name contains new lines and whitespace and is completely legal within CubeScript, it will be read as `my^n^talias^n^t^t`. See the [Strings](#strings "wikilink") section for move information of what that means.

<h3 id="lookups">
Lookups

</h3>
There are two ways to do look ups, the first is through the use of the `$` character, and the second through the use of the `@` character. The `@` character has rather complex and intricate rules, you may be better off familiarising yourself with the other aspects of CubeScript ([Blocks](#blocks "wikilink") especially) before trying to familiarise yourself with that.

The most basic and primitive form of the `$` based lookup is as described in the `push` example above. You simply prefix the name of an alias with the `$` character. You can also prefix it with multiple `$` characters to do recursive lookups.

    printvar = [
        echo The value of (escape $arg1) is: $$arg1
    ]
    myalias = 10;
    printvar myalias

Also of special note here, when an alias is called as a user created command, the arguments it is called with are named `arg1`, `arg2`, `arg3`, etc, up to `arg24`. In the example above, printvar is called with the single parameter `myalias`, this is in turn aliased to `arg1`. We do a lookup of `$arg1` which returns the value `myalias` before passing it to the `escape` command, which will return `"myalias"`. We then print the value, the order is literally as follows `$$arg1` --&gt; `$myalias` --&gt; `10`

The name of the alias can also be generated via commands inside `()` blocks, or inside `[]` blocks through use of the `@` macro token. Let's say I want to loop through several named tokens with a predictable pattern, such as `arg1`, and company above. We can use some sort of iterator and generate the names on the fly. The smaller, and faster `@` version is shown further below. Also see Iterators below for a more thorough explanation of them.

    myalias = [
        loop+ i 1 $numargs [
            echo $(concatword arg $i)
        ]
    ]
    myalias 1 2 3 4

`numargs` is a reserved alias which documents the amount of arguments given to an invocation of an alias.

You can also specify string literals to lookup, for more information on the makeup of strings, consult Strings further below. This is written in the format, `$"myalias"` to lookup the alias, myalias. This has the unique feature of allowing double slashes, eg `$"//not` `a` `comment"` will literally lookup the alias "//not a comment".

<strong>If you're new to CubeScript, the rest of this section will likely fly over your head.</strong> It's advisable that you familiarise yourself with the other parts of CubeScript before venturing forth.

`@` is a macro token, and is one of CubeScript's many very Lispy features. It is very useful for generating code... from code. It works on the principles of substitution and nesting (see [Blocks](#blocks "wikilink")). `@` tokens can also be compounded to raise the level of nesting at which substitution will take place, only `[]` blocks raise the nesting level. It is an error to have more `@` tokens than there are nesting levels.

`@` is a literal substitution into the code being executed. With enough tokens it is possible to make the results permanent.

    myalias = 10
    foo = [
        echo @myalias
    ]
    myalias = 5; foo

The above for example permanently substitutes 10 into foo, running foo will always echo 10 onto the screen, despite changing it to 5 immediately after. This is a very useful feature, as it allows you to add persisting data where it is vital they don't change, for example, the `sleep` command, the contents of its body argument are very much based on the state of the game upon its time of execution.

    //Every iteration here will print whatever i is at the time of execution.
    //It can be anything; even nothing.
    loop i 10 [
        sleep (* $i 100) [
            echo $i
        ]
    ]

    //This one will behave as expected, and print 0, 1, 2...
    loop i 10 [
        sleep (* $i 100) [
            echo @i
        ]
    ]

As a general rule, if you're scripting something with deferred execution that depends heavily upon the context it has been deferred from, use `@` to substitute the values.

`@` can also be used for concatenation, the parser isn't too discriminatory with where you put them, as long as there is enough nesting for all the tokens. Take the above example using `$` to lookup the aliases, `arg1`, `arg2`, etc, in order. We can rewrite this in CubeScript by using blocks and the substitution powers of `@`. As a side effect, code is generally much shorter and somewhat faster through the use of `@` over calling `concatword`, whether one or the other is more readable is up to you.

    myalias = [
        loop i $numargs [
            // these two are equivalent.
            // echo $(concatword arg (+ $i 1))
            echo $[arg@(+ $i 1)]
        ]
    ]
    myalias 1 2 3 4

The macro token also has one final and very useful purpose, getting the whitespace in the code re-evaluated. Let's take the command, `getcampos` for example, it takes no arguments and returns a list of 3 elements, such as `100` `200` `-300`, which is a lateral representation of the camera's coordinates at the time. If I want to call this against myalias as defined above, ie `myalias` `(getcampos)`, this would be passed in as a single argument, and in the case of the previous, print the result on only 1 line.

This is incidentally why macro use is often surrounded by a `[]` block, to avoid this effect and keep it as a singular unit. If I wanted to have the output of `getcampos` parsed as 3 discrete arguments, I can write my invocation of myalias, or another command like `at` (say, if I wanted to get the Y or Z coordinate), as follows.

    do [
        myalias @(getcampos)
    ]

`do` is a command which executes what is given, verbatim. In this case it is used to give this snippet enough nesting to work.

<h2 id="commandsoperators">
Commands/Operators

</h2>
With the exception of the infix `=` operator used to declare aliases, everything in CubeScript is a command and is called as such. Even operators like `+` or `-` are commands, alternatively it can be said that they are <em>prefix operators</em>. We have already mentioned and used several commands up above, like `echo`, `alias` and `do`. Essentially everything takes the following form.

    command arg1 arg2 arg3 arg4 arg5 ... arg24

You may have noticed that I stopped that at arg24 instead of argn, that's because CubeScript has a limit of 25 words per statement, the command is the first and this leaves room for 24 arguments. There are ways to get past it, but that's an advanced topic.

Not all commands return values, not all commands print stuff to screen, and not all of them take input. The user is able to define aliases as collections of commands with which to define their own. Let's first start with operators, below you'll find a list of available mathematical operations and an example of their use. Excepting the `!` operator, you can also specify 2 or more operations for all operators.

    // Integer Arithmetic
    +        (+ 1 2 3 4)   ==&gt; 10
    -        (- 12 8 2)    ==&gt; 2
    *        (* 12 12)     ==&gt; 144
    div      (div 6 5)     ==&gt; 1
    mod      (mod 5 3)     ==&gt; 2
    abs      (abs -35)     ==&gt; 35
    min      (min 10 5 -1) ==&gt; -1
    max      (max 10 5 -1) ==&gt; 10

    // Bit-wise Arithmetic
    ^        (^ 7 15)      ==&gt; 8
    &amp;        (&amp; 7 15)      ==&gt; 7
    |        (| 7 15)      ==&gt; 15
    ~        (~ -2)        ==&gt; 1
    ~        (~ 1 2)       ==&gt; 3     // equiv to: (^ (~ 1) (~ 2))
    ^~       (^~ -1 7)     ==&gt; 7
    &amp;~       (&amp;~ 0xFF 7)   ==&gt; 248
    |~       (|~ 15 -1)    ==&gt; 15
    &lt;&lt;       (&lt;&lt; 16 2 2)   ==&gt; 256
    &gt;&gt;       (&gt;&gt; 256 4)    ==&gt; 16

    // Logical Operations
    !        (! 4)              ==&gt; 0
    &amp;&amp;       (&amp;&amp; 1 1 1 1 1 0)   ==&gt; 0
    ||       (|| 0 0 0 0 0 1)   ==&gt; 1

    // Logical Integer Operations
    =        (= 5 5.12345) ==&gt; 1   // a reminder that these are for integers
    !=       (!= 4 5)      ==&gt; 1
    &lt;        (&lt; 10 4)      ==&gt; 0
    &gt;        (&gt; 10 4)      ==&gt; 1
    &lt;=       (&lt;= 11 10)    ==&gt; 0
    &gt;=       (&gt;= 11 11)    ==&gt; 1

    // Floating Point Arithmetic
    +f       (+f 2.5 3 2)             ==&gt; 7.5
    -f       (-f 6 .66)               ==&gt; 5.34
    *f       (*f 6 6)                 ==&gt; 36
    divf     (divf 6 5)               ==&gt; 1.2
    modf     (modf 2.5 2)             ==&gt; .5
    absf     (absf -3.141)            ==&gt; 3.141
    minf     (minf -3.1 -3.14 -3.141) ==&gt; -3.141
    maxf     (maxf -3.1 -3.14 -3.141) ==&gt; -3.1

    // Logical Floating Point Operations
    =f       (=f 2.5 2)    ==&gt; 0
    !=f      (!=f 2.5 2)   ==&gt; 1
    &lt;f       (&lt;f 3 3.5)    ==&gt; 1
    &gt;f       (&gt;f 3 3.5)    ==&gt; 0
    &lt;=f      (&lt;=f 2.5 2.5) ==&gt; 1
    &gt;=f      (&gt;=f 2.5 5)   ==&gt; 0

    // Floating Point Operations
    sin      (sin 90)     ==&gt; 1
    cos      (cos 90)     ==&gt; 0
    tan      (tan 45)     ==&gt; 1
    asin     (asin 1)     ==&gt; 90
    acos     (acos 0)     ==&gt; 90
    atan     (atan 1)     ==&gt; 45
    sqrt     (sqrt 9)     ==&gt; 3
    pow      (pow 3 2)    ==&gt; 9
    loge     (loge 2.818) ==&gt; about 1
    log2     (log2 2)     ==&gt; 1
    log10    (log10 10)   ==&gt; 1
    exp      (exp 1)      ==&gt; about 2.818

    // String comparisons
    =s       (=s &quot;bob&quot; &quot;susie&quot;)  ==&gt; 0
    !=s      (!=s &quot;bob&quot; &quot;susie&quot;) ==&gt; 1
    &lt;s       (&lt;s &quot;bob&quot; &quot;susie&quot;)  ==&gt; 1
    &gt;s       (&gt;s &quot;bob&quot; &quot;susie&quot;)  ==&gt; 0
    &lt;=s      (&lt;=s &quot;bob&quot; &quot;susie&quot;) ==&gt; 1
    &gt;=s      (&gt;=s &quot;bob&quot; &quot;susie&quot;) ==&gt; 0

With that, you've been introduced to all of the available operators and mathematical functions. It's all well and good that you know 3 and 2 can be added together by `+` `3` `2`, but what if you wanted to do something a bit more complex like `3` `*` `2` `+` `5` `*` `5`. We're skipping ahead here, but you would put these inside their own `()` blocks. For that example, we would have `3` `*` `2` and `5` `*` `5` in their own blocks, with the two contained in another block that adds the two together. CubeScript has no knowledge of operator precedence, and that is irrelevant considering how it works.

    // 3 * 2 + 5 * 5 as written in CubeScript whilst respecting BIMDAS
    echo (+ (* 3 2) (* 5 5))

How about we put some of the magic we've learnt in the last section to use as well. We as users can define our own commands via aliases. What if we wanted to write a command to evaluate a parabolic function of the form `y` `=` `ax^2` `+` `bx` `+` `c`. For clarity's sake, let us first rewrite this without substituting in the lookups.

    y = (+f (*f a (pow x 2)) (*f b x) c)

Let's say we declared our alias to take 4 arguments in the order of a, b, c, and x. We would use $arg1, $arg2, $arg3 and $arg4 to refer to these respectively. For clarity, we will print them as well. Also note that `=` must be the second word of a statement for it to be considered as an infix operator.

    parabola = [
        echo a = $arg1 b = $arg2 c = $arg3 x = $arg4
        echo y = (+f (*f $arg1 (pow $arg4 2)) (*f $arg2 $arg4) $arg3)
    ]
    parabola 2 2 3 4

Congrats, you've made your own function for calculate the coordinates of a parabolic function!

The word limit does bear some more talking about too. There is a limit of 25 words per statement, and the command itself uses the first. There are some commands, like `echo` and `concat` which take input as is without a single care given to how many words there is, these commands also preserve spaces between words, a fact we make free use of in here.

Then there are others which are sensitive to the limit, if the limit is reached, all the remaining words are concatenated into one final, giant word. You can parse this easily enough with the `at` command, but detecting a case in which this happens is somewhat difficult, what if the last argument were the actual, final argument, and a list at the same time?

Nearly all built in commands take up to 8 or so arguments, some can take a variable number up to 24, its these that may cause trouble with an unsuspecting word limit. You'd almost never need this many, but if you do, there are ways to get around it, and it's usually a sign that you need to rethink your code.

Now that you know the basics of executing commands, you can find a list of generic CubeScript commands in the [Command Reference](Command_Reference "wikilink"). Also consult the [Scripting](Scripting "wikilink") page for the RPG subsystem's.

<h2 id="iteration">
Iteration

</h2>
Loops are available through several commands. As CubeScript has some of its roots from Lisp, it lends itself to iteration through recursion without these commands.

There are several loops available, you have basic ones like `loop` and `while`, and you have more esoteric ones like `loopfiles` which bring up a list of matching filenames and directories which are iterated over. Those defined for the RPG scripting subsystems work identically.

    A list of iterator commands

    loop id num [body]
    while [cond] [body]
    loopwhile id num [cond] [body]
    loopconcat id num [body]
    loopconcatword id num [body]
    looplistconcat id $list [body]
    looplistconcatword id $list [body]
    looplist id $list [body]
    looplist2 id1 id2 $list [body]
    looplist3 id1 id2 id3 #list [body]
    loopfiles id directory extension [body]

<h3 id="loop">
loop

</h3>
This is the most simple loop available, it iterates from 0 to n-1, where n is the number of iterations specified. The given id has a stack pushed onto it and containers the value of the iterator. Whatever changes you make to the ident will be undone come the next iteration, therefor the loop can not be exited early, for that see loopwhile.

Let's consider a basic loop from 0 to 100, and combine it with some of the arithmetic magic we've learnt in the previous page...

    loop i 100 [
        echo (- 99 $i) more iterations to go
    ]

<h3 id="while">
while

</h3>
This is the second primitive loop command, the gotcha here is that you better make sure you pass it a block of code for the condition, and not a pre-evaluated block of code. Let's consider an example to better explain it.

    tmp = 5
    while (!= $tmp 0) [ tmp = (- $tmp 1); echo $tmp ]

The above is an example of the infamous infinite loop. You can consult the [Blocks](#blocks "wikilink") section for more details on why, but basically `(!=` `$tmp` `0)` is substituted in, and since it was true, the while loop will always be true, in spite of the fact that its body changes the value of tmp into something that would eventually evaluate to false, if the condition were ever reevaluated, which it is not. <strong>This is a very common pitfall when using the while loop, so you should make sure to watch out!</strong> The above rewritten to work correctly would look like this.

    tmp = 5
    while [!= $tmp 0] [ tmp = (- $tmp 1); echo $tmp ]

This will print 4, 3, 2, 1 and 0, at which the condition becomes false and the loop exits.

<h3 id="loopwhile">
loopwhile

</h3>
loopwhile is a combination of the above two loops, it end the loop when either <strong>a)</strong> the iterator reaches the limit, or <strong>b)</strong> the condition becomes false. Aptly, we will use a combination of the above two loops to demonstrate.

    tmp = 5
    loopwhile i 100 [ != $tmp 0 ] [
        tmp = (- $tmp 1)
        echo (- $99 $i) more loops to go?
    ]

This is the <strong>ONLY</strong> loop which can be terminated early.

<h3 id="loopconcat-and-loopconcatword-lists">
loopconcat and loopconcatword + lists

</h3>
`loopconcatword` is basically the exact same as `loopconcat`, with the exception that `loopconcat` adds a space between list items. Essentially `loopconcat`'s purpose is to generate a list, the body generates a string which is then appending onto the end of a string, which it returns once it's done looping.

`looplistconcat` and `looplistconcatword` also exist to produce the same effect but from a list.

Let's say we wanted it to calculate all the squareroots of the numbers 1 through 10, we can write the following.

    //1.0 1.414214 1.732051 2.0 2.236068 2.44949 2.645751 2.828427 3.0 3.162278
    // we store it in a list here to demonstrate looplistconcat later on.
    squares = (loopconcat i 10 [
        sqrt (+ $i 1)
    ])

    // we use prettylist to pretty up the printing
    echo the squareroots of the numbers 1-10 are: (prettylist $squares and)

    // we now calculate the originals with the new list
    echo (prettylist (looplistconcat n $squares [ pow 2 $n ]) and)

As mentioned, the `concatword` variants work identically, except for the fact that whitespace is not implicitly added between elements. You will need to manage them and you are solely responsible for it.

<h3 id="looplist23">
looplist\[23\]

</h3>
This command takes an id and a list, and iterates through the list by setting the id to each list item in turn. This is much faster than using a traditional loop and dereferencing the list, but you will also lack access to an iterator. Technically it would be cheaper to maintain a separate iterator via this mechanism if you required one, as you wouldn't need to pass and parse a large list every time.

    &quot;my list&quot; = [
        zero one two three four five size seven eight nine
        ten eleven twelve thirteen fourteen fifteen sixteen seventeen eighteen nineteen
    ]
    looplist item $[my list] [
        echo $item
    ]


    //For the sake of argument, the exact same is done with a traditional loop.
    loop i (listlen &quot;my list&quot;) [
        //this `at` by the way, is the bottleneck
        echo (at $[my list] $i)
    ]

`looplist2` and `looplist3` work similarly, but advance 2 and 3 elements respectively per iteration. The typical use case for such iteration is when having the list setup with key-value(-value) sets.

    list = [ key value key2 value2 key3 value3 ]
    looplist2 key value $list [ echo key is (escape $key) and value is (escape $value) ]

For more information on handling lists, see the [List Management](#lists "wikilink") section.

<h3 id="loopfiles">
loopfiles

</h3>
loopfiles takes a directory path and filters files by extension. If the extension is omitted, all files are listed <strong>WITH</strong> their extensions.

Let's try a simple example. We will print the names of each and every map available.

    // packages/maps is the official map directory, and .ogz the file extension.
    loopfiles filename packages/maps ogz [
        echo The map (escape $filename) is available
    ]

<h2 id="branching">
Branching

</h2>
The first and most commonly used branching operation in CubeScript is `if`. This command takes 3 arguments, the truth value, and what it does it true, and when false. The first first value looks for a non-zero number. Note strings are generally considered true, unless the string is empty. Otherwise if the string can be converted to a number, that value is used to determine truth.

    // if trueval [true body] [false body]

    GreaterThan100 = [
        if (&gt;= $arg1 100) [
            echo $arg1 is at least 100
        ] [
            echo $arg1 is less than 100
        ]
    ]

    GreaterThan100 500
    GreaterThan100 65

The ternary operator, `?`, is also available if you wish to substitute in the results directly, without executing it.

    // the if version could also be rewritten in a similar way...
    //(if (&gt;= $arg1 100) [result &quot;at least&quot;] [result &quot;less then&quot;])

    GreaterThan100 = [
        echo $arg1 is (? (&gt;= $arg1 100) &quot;at least&quot; &quot;less then&quot;) 100
    ]

    GreaterThan100 500
    GreaterThan100 65

The second most commonly used is the `case` statement, and this is available in 3 different flavours for integers, floating points and strings, respectively these are `case`, `casef`, and `cases`. The first argument these take defines the value, subsequently it takes value and body pairs, it will iterate through and execute the body of the first pair that compares true against the given value. To specify a default case, pass a `VAL_NULL` as the first member of a pair, this esoteric value can easily be generated by passing `()` as an argument. Keep it mind the word limit of above applies, allowing you a maximum of up to 11 pairs.

    // an example for strings

    cases (enttype) light [
        echo &quot;The light! It shines!
    ] particles [
        echo &quot;shinies everywhere!&quot;
    ] () [ //default
        echo &quot;Some really boring entity I&#39;m sure&quot;
    ]

The third most commonly used is the `cond` statement. It takes two pairs of script bodies, the first defining a condition, and the second define a body to be executed, essentially it's a massive if-else block. You can provide up to 12 pairs before you encounter the word limit. The default/else case is rather interesting as you can write it in two ways due to a technicality. The official method would be `[` `1` `]` `[` `do` `stuff` `here]`, but because the conditional is executed to determine whether or not to execute the body, you can in fact skip the condition and let that be the body, ie, just `[do` `stuff` `here]`.

    // This example uses Lazy-Evaluation because cond accept two code blocks.
    // See below for applying it to if.
    cond [ &lt; $arg1 3 ] [
        echo $arg1 is less than three
    ] [ &lt; $arg1 10 ]
        echo $arg1 is less than 10
    ] [ 1 ] [
        // you can just omit the [ 1 ] if you wanted
        echo $arg1 is greater than or equal to 10
    ]

<h3 id="lazy-evaluation-advanced">
Lazy Evaluation (Advanced)

</h3>
You can find more background about how this works in the [Blocks](#blocks "wikilink") section further below.

The short of it is, `()` blocks are executed on the spot, and the `&&` and `||` operators take a list of script blocks, which they execute in order from left to right. Let's take a look at the following piece of code, and see how it is evaluated.

    foo = 20
    bar = 50
    baz = 35
    if (&amp;&amp; (= $foo $bar) (&lt; $foo $baz) (!= $bar $baz)) [
        echo pass!
    ]

    // Because of how the () blocks work...
    // (&amp;&amp; (= $foo $bar) (&lt; $foo $baz) (!= $bar $baz))
    // will be interpreted as...
    // (&amp;&amp; 0 1 1)

Essentially, when using `()` blocks for your comparisons, each and every comparison will always be done with the results substituted in. So if you had comparisons you only want done under certain conditions, you could put them inside a `[]` block, like so.

    foo = 20
    bar = 50
    baz = 35
    if (&amp;&amp; [= $foo $bar] [&lt; $foo $baz] [!= $bar $baz]) [
        echo pass!
    ]

Because of how the `[]` blocks work, that will literally be executed as is. With the above example, it will see that `[=` `$foo` `$bar]` is false, aka 0, and terminate there without evaluating the other two conditions.

Now, if you wanted to turn lazy evaluation up to 11, you can use another esoteric feature available in CubeScript. The last statement of a block is responsible for the return value. Take our example for instance, `baz` isn't needed until the second comparison is done, so we can defer its creation/initialisation until then.

To do this, we'd write it as follows, with the initialisation of the variable inside the block. We can put either the comparison last, or use the `result` command to explicitly set the result. We will do the former for simplicity's sake.

    foo = 20
    bar = 50
    if (&amp;&amp; [= $foo $bar] [baz = 35; &lt; $foo $baz] [!= $bar $baz]) [
        echo pass!
    ]

Of course, you can also mix the two forms if you needed to have something always get executed. Also note that lazy evaluation is best used in cases were additional conditions require the previous ones to have passed (or failed as the case may be).

<h3 id="getting-past-the-word-limit-advanced">
Getting Past The Word Limit (Advanced)

</h3>
The word limit of 25 can be pretty suffocating. But there are always of getting around it. You should note that reaching this limit is bad form, and is a sign that you should probably rethink your code.

The first, which is also the quickest and simplest, is simply to not bother with the branching commands, and instead rely on the code generation facilities of CubeScript. Of course, this works best if you're using a simple and predictable pattern, and this is actually used a few times within Lamiae's code.

1.  Universal Delta

n

<li>
Hover scrolling actions for entities

</li>
<li>
Quick edit menus for entities

</li>
<li>
lower left editmode helper for entities.

</li>
</ol>
Let's look at Universal Delta works, it's one of the easier samples to show an example of.

    universaldelta = [
        [delta_@(? $editing edit game)_@[modifier]] $arg1
    ]

Basically it generates a simple alias, which is then executed with an argument, this is either 1 or -1 depending on which way you scrolled your wheel. We can see here that the name of the alias called by universal delta will always start with `delta_`, this will be followed by either `edit` or `game` depending on whether or not we're in editmode, and after this a modifier value is appended. This modifier value is set by various keys. For example, `1` when held changes the modifier to 11, and `G` changes it to 1.

    delta_edit_1 = [ nodebug [ gridpower (+ $arg1 $gridpower) ] ]
    delta_edit_2 = [ editfacewentpush $arg1 0 ] // push face/corners selected
    delta_edit_3 = [ editfacewentpush $arg1 2 ] // push corner pointed at by cursor
    delta_edit_4 = [ editrotate $arg1 ] // rotate 90 degrees
    delta_edit_5 = [ entproperty 0 $arg1 ]  // and the others
    delta_edit_6 = [ edittex $arg1 ] // change textures
    delta_edit_9 = [ selectbrush $arg1 ] // change heightmap brushes
    delta_edit_10 = [ entautoview $arg1 ]
    delta_edit_11 = [ entproperty 0 (* $arg1 $multiplier) ]
    delta_edit_12 = [ entproperty 1 (* $arg1 $multiplier) ]
    delta_edit_13 = [ entproperty 2 (* $arg1 $multiplier) ]
    delta_edit_14 = [ entproperty 3 (* $arg1 $multiplier) ]
    delta_edit_15 = [ entproperty 4 (* $arg1 $multiplier) ]
    delta_edit_16 = [ entproperty 5 (* $arg1 $multiplier) ]
    delta_edit_17 = [ entproperty 6 (* $arg1 $multiplier) ]
    delta_edit_18 = [ entproperty 7 (* $arg1 $multiplier) ]

And depending on the value of `modifier` it can execute any one of those. you can also employ other methods, but <strong>all</strong> of these are slower and not as easy to follow.

For example, I could take the above and arrange them in a list, look up the appropriate list entry and execute it. like so... With such a list, it is generally bad form to use the `arg#` family of aliases.

    delta_edit = [
        [ nodebug [ gridpower (+ $arg1 $gridpower) ] ]
        [ editfacewentpush $arg1 0 ]
        [ editfacewentpush $arg1 2 ]
        ....
    ]

    universaldelta = [
        (at (? $editing $delta_edit $delta_game) $modifier) $arg1
    ]

You can even create a table of condition/body pairs, and execute these by passing it to a parser of your own making. The example table below just contains random gibberish. Basically you're making an inefficient version of cond with CubeScript's code generation capabilities.

    mytable = [
        [
            [ &lt; $arg1 5 ]
            [ echo I can count this many ]
        ]
        [
            [ &lt; $arg1 15 ]
            [ echo limerick ]
        ]
        [
            [ &lt; $arg1 50 ]
            [ echo John the elderly ]
        ]
        [
            [ 1 ]
            [ echo This table is a massive potato ]
        ]
    ]

    parser = [
        local done
        done = 0
        loopwhile i (listlen $$arg1) [ ! $done ] [
            local conditional body
            conditional = (at $$arg1 $i 0)
            body = (at $$arg1 $i 1)

            if (conditional @(loopconcat j (- $numargs 1) [ escape $[arg@(+ $j 2)] ])) [
                done = 1
                body @@(loopconcat j (- $numargs 1) [ escape $[arg@(+ $j 2)] ])
            ]
        ]
    ]

    parser mytable 10

<h2 id="strings">
Strings

</h2>
Strings are basically just a collection of characters, and always surrounded by `""` characters when whitespace is involved. Strings are a special context as it literally reads text verbatim until the terminating `"` character. A newline also terminates the string context. Unlike Bash and other shell languages, which can have substitutions in string literals, CubeScript does not support such a feature, strings are WYSIWYG, with the exception of escaped characters...

To add an escaped character, you enter `^` and then enter the character you want escaped. It will then add the next character, even a linefeed (), onto the end of the string as is. There are a few special cases and they are summarised in the list below.

    // ^&lt;newline&gt; allows the sentence to stretch over multiple lines
    string1 = &quot;this is a string^
    over multiple lines&quot;

    // ^t inserts a tabulator character
    string2 = &quot;My^tstring&quot;

    // ^n inserts a linefeed character, aka a newline.
    string3 = &quot;My string^nuses^n3 lines.&quot;

    // ^&quot; allows you to insert a literal &quot; character into the string
    string4 = &quot;This is my ^&quot;string^&quot;&quot;

    // ^^ inserts a ^ character into the string
    string5 = &quot;This string contains the character, ^^.&quot;

    // ^f# inserts a format token
    string6 = &quot;^f0This string is coloured green&quot;

There are various tokens that can be given to `^f#`, the most common ranges from 0-7, or uses the letter, `r`, or `s`. They're listed as follows, also note that Lamiae supports 24 additional colours. These can be accessed by a capital letter, such as 'A' or 'G', the ones listed here are supported by all cube 2 derivatives.

-   `s` pushes the current colour onto the colour stack
-   `r` pops the colour stack, restoring the previous colour
-   `0` Colours the coming text green
-   `1` Colours the coming text blue
-   `2` Colours the coming text yellow
-   `3` Colours the coming text red
-   `4` Colours the coming text grey
-   `5` Colours the coming text magenta
-   `6` Colours the coming text orange
-   `7` Colours the coming text white

Anything else will restore the default colour.

<h3 id="fiddling-with-strings">
Fiddling with Strings

</h3>
Strings can be toyed with in various ways, this section will explore some of the more common commands.

    concat C
    concatword {word}
    format &quot;format&quot; sub1 sub2 sub3 ...
    escape str
    unescape str
    strstr str pattern
    strreplace str oldval newval
    strlen str
    substr str skip [count]

<h4 id="concat-and-concatword">
concat and concatword

</h4>
`concat` and `concatword` are probably the easiest way to add onto strings, and we've used these quite heavily throughout the examples on this page.

`concat` essentially takes a long string of text and returns these, evaluated, and with spaces included. `concatword` does likewise, but operates on the individual words, rendering it unable to preserve the whitespace.

    echo (concat one two)
    echo (concatword one two three)

<h4 id="format">
format

</h4>
`format` is one of the most widely used commands to format text. It works on the basis of a pattern, with which it substitutes the given argument. The tokens the pattern matches are defined by `%n`, where n is a number between 1-9. The values to substitute are passed as extra arguments.

    fox = &quot;quick brown fox&quot;
    dog = &quot;lazy dog&quot;

    echo (format &quot;the %1 jumped over the %2.&quot; $fox $dog)

<h4 id="escape-and-unescape">
escape and unescape

</h4>
Basically two commands that undoes the other. The `escape` command is the only way in which to escape strings to work flawlessly in all cases. Especially if you intend to later add it to a list. One level of `unescape` is usually performced implicitly as needed, so you may not need to ever worry about that.

    echo (escape rftvcygs&quot;sdfj&quot; )
    // &quot;rftvcygs^&quot;sdfj^&quot;&quot;

<h4 id="strstr">
strstr

</h4>
`strstr` works exactly as its C coounterpart does, only here it returns the index instead of a pointer, and matches NULL with -1. It takes a string and a pattern (aka needle) and returns a value &gt;= 0 if it's found.

<h4 id="strreplace">
strreplace

</h4>
This command works exactly as advertised. It takes a string, takes a pattern that matches an existing value in the string, and replaces it with the new one. It does so for every matching instance in the original string

    // the usage of %str% is just to make the substitution blatantly obvious.
    echo (strreplace &quot;%str% and something %str%&quot; &quot;%str%&quot; &quot;%str% potatoes&quot;)

    // to demonstrate there&#39;s no recursion.
    // &quot;%str% potatoes and something %str% potatoes&quot;

<h4 id="strlen">
strlen

</h4>
Works exactly like its C counterpart, returns the length of a given string.

<h4 id="substr">
substr

</h4>
`substr` cuts out a segment of a string. It takes a string, the character to start from, and how many to go for. In the absence of a count it goes until it reaches the end.

    str = &quot;ashkdSome String65646&quot;
    echo (substr $str 5 (strlen &quot;Some String&quot;))

<h2 id="blocks">
Blocks

</h2>
There are two types of blocks in CubeScript, ones denoted by `[]` and ones denoted by `()`. We'll start with the specifics of the latter as the basics are easier to understand.

Both these types permit nesting and can be nested inside the other, keep in mind that they cannot terminate the other, ie `[)` or `(]` is an error. Both of these can also contain a series of statements that span multiple lines, but the rule in which a newline serves as an end of statement indicator still applies. Statements can also be prematurely ended with a `;` character.

Also note that the final statement in a block determines its return value, or result, and that it is impossible for a block to cease execution early. Once a block started execution, it will execute right until the end, CubeScript has no commands which are functionally equivalent to the `break`, `continue` or `return` of most imperative programming languages.

The first unique feature of the `()` brackets is that they are executed as soon as the statement they are part of is executed, and their results are substituted in before execution of the statement itself takes place.

    //for example, this will be parsed as &quot;echo 8&quot;
    echo (+ 5 3)

This also means that if a `()` block is the lone statement of a block, its result will be executed.

    //equivalent to the above, but rather strange
    ( result &quot;echo&quot; ) (+ 5 3)

You can even do crazy levels of nesting. Keep in mind that the following example is considered poor code and is only intended to demonstrate a feature.

    num = 0
    nest = [
        num = (+ $num 1)
        ? (&gt; $num 5) &quot;echo&quot; &quot;nest&quot;
    ]
    ((((((nest)))))) (+ 5 3)

The more typical use for the nesting is for recursive evaluation of mathematical expressions.Take the expression `48` `/` `2` `(9` `+` `3)` (assume implicit multiplication). To do this normally, you would first evaluate `(9` `+` `3)` for the answer 12 and substitute this in, to get `48` `/` `2(12)`. Then you'd handle the implcit multiplication to get `48` `/` `24` and you'd then do this final division to get `2`.

CubeScript doesn't have an order of precedence, in part because everything uses prefix notation. The math is still done in that order though, but using the `()` blocks coupled with nesting. We could write that expression as follows.

    echo (div 48 (* 2 (+ 9 3)))

The `[]` type is more often than not referred to as a "code block" and it serves as the standard means of encasing and writing executable CubeScript. The `[]` blocks are not executed until they are explicitly called, making them ideal for that purpose. Their other significant feature is that opening one of these blocks increases the global nesting level, and this ties in directly with the type `@` lookups which rely on this behaviour. Otherwise you don't need to pay much heed to that.

Since `[]` blocks aren't executed when they're encountered, this also makes them really useful for keeping objects and definitions together, and also for keeping lists. Lists will be covered in more depth in the next section.

    //a basic list.
    mylist = [
        &quot;one&quot;
        &quot;two&quot;
        &quot;three&quot;
    ]

Both of these types directly work with the lookups, for example if I tried to lookup `$[main]` it will try to lookup the alias, `main`, `@` behaves similarly. As mentioned previously, `[]` increases the nesting level, and that `@` tokens can be placed nest to each other to let substitutions occur at an earlier nesting level. The following example does the substitutions at the earliest permissible nesting level.

    foo = 1
    myalias = [
        bar = 2
        echo @foo
        echo $bar

        do [
            baz = 3
            echo @@foo
            echo @bar
            echo $baz

            do [
                echo @@@foo
                echo @@bar
                echo @baz
            ]
        ]
    ]

As a general note you would typically also put the lookup itself inside `[]` brackets when using `@` for lookups, like follows...

        baz = 3
        echo @@foo
        echo @bar
        echo $baz

    |    |    |    |
    V    V    V    V

        baz = 3
        echo [@@@foo]
        echo [@@bar]
        echo $baz

The final feature of the `[]` blocks is also one of its more useful ones. There is a command named `local` which accepts a series of values. These are the names of aliases whose stacks will get pushed and be bound to the block, essentially declaring local variables whilst shadowing the old instance. `local` doesn't initialise them, nor does it allow you to do so as part of the declaration, the initialisation must follow somewhere after.

    myalias = 1
    do [
        //note how it takes a list
        local foo bar baz myalias

        //initialise the local copy
        myalias = 5
        echo $myalias
    ]
    echo $myalias

<h2 id="list-management">
List management

</h2>
Lists are CubeScript's innate means of storing collections of elements. As you can store even additional lists in the list, recursing as deep as you wish, it's quite accurate to compare these to the lists in LISP. The basic form of a list is an alias with one or more elements in it. Yes, even `a` `=` `5` is a list of one element, and `b` `=` `"this` `is` `totally` `a` `string"` a list of 5 elements.

    list = [
        one two three
        four five six
    ]

    // You can even nest them.

    nestedlist = [
        [one-one one-two one-three]
        [two-one two-two two-three]
        three
        [
            four-one
            [ four-two-one four-two-two ]
        ]
    ]

The most important thing to know is how to actually make use of segments of the lists. The principle command for this is `at`, of course, if you're iterating through the list, it would be much more efficient to use `looplist`. The length of a list can be queried with `listlen`

`at` is defined with the syntax `at` `$list` `{$idx}`, using recursive at calls is equivalent to giving the initial invocation additional arguments. In other words, `at` `(at` `$nestedlist` `1)` `2` is equivalent to `at` `$nestedlist` `1` `2`.

    loop i (listlen $nestedlist) [
        echo (at $nestedlist $i)
        loop j (at $nestedlist $i) [
            echo (at $nestedlist $i $j)
        ]
    ]

    //the exact same done with looplist

    looplist item $nestedlist [
        echo $item
        looplist item $item [
            echo $item
        ]
    ]

Now that we've managed that, the second most important thing when it comes to lists is to add more stuff. This is traditionally done via the commands `concat` and `concatword`, though anything will do provided they can bring two or more blocks of text together; the original list and whatever you're adding. Even lookups can be used for this end.

    //appending the traditional way
    list1 = (concat $list seven eight nine)
    list2 = (concatword $list seven eight nine)
    list3 = [ @list seven eight nine ]

Now, some of you will notice I used the word "append" there, and be asking "how do I insert items at position n instead." In the past this was laborious, you had to make a new list with the first n elements, add your new stuff, then add the rest of the list onto it, this process was slow and error prone. but the `listsplice` command has since appeared.

`listsplice` takes 4 arguments. The command is of the form `listsplice` `$olditems` `$newitems` `skip` `[remove]`. It works like this, it takes the old list and skips the first `skip` elements, at this point it inserts the `newitems`. It then removes the next n elements specified by `[remove]` from the original list and append whatever remains. If remove is bigger than the number of elements left over, it will silently stop when it reaches the end.

Essentially the new list it produces is of this form: `[skipped` `elements]` `[new` `elements]` `[removed` `elements]` `[remaining` `elements]`

    list = [
        one two three four 5
        six seven eight nine ten
    ]

    //For this example we /accidentally/ wrote 5 as a number
    //We&#39;ll use listsplice here to replace it with a word
    list = (listsplice $list [five] 5 1)

Next up on the topic of lists is finding elements in them. Unless you'd like to manually go through them with iteration and loops, you have two commands available depending on your needs.

The first, and simplest, is the command `indexof`, which takes two commands, the list, and the exact <strong>full</strong> name of the element you're searching for.

The second command for finding things in the list is the command listfind, this takes three arguments, an id, the list, and the body for a comparison. This comparison should return a true value to indicate the element has been found so that the search may terminate. You're allowed a lot more freedom than with indexof, indexof could be defined quite easily in CubeScript, `indexof` `=` `[` `listfind` `i` `$arg1` `[` `=s` `$i` `$arg2` `]]`.

Both of these return the index of the element, provided it has been found. Otherwise it returns an invalid index, specifically -1, therefore you would use `>=` `(listfind` `...)` `0` to determine if the search was successful. Let's use these commands to search for some things in the previous lists.

There are also `listfind=`, `listfind=f`, and `listfind=s` commands available. These are equivalent to using listfind with the `=`, `=f`, and `=s` operators respectively, and require you to give an element, as opposed to a script body. Unlike the original, these variants also take an optional skip value, which lets you start searching from a specific element onwards, allowing you to easily ignore multiple hits, or to quickly parse the list for all matches.

    list = [
        one two three
        four five six
    ]

    ind = (indexof $list &quot;five&quot;);

    //alternative way with listfind=s, which starts looking from &quot;four&#39; onwards
    //ind = (listfind=s $list &quot;five&quot; 3)
    echo The position of &quot;five&quot; is $ind


    ind = (listfind l $list [
        strstr $l ee
    ]
    if (&gt;= $ind 0) [
        echo (escape (at $list $ind)) is the first element with two e characters in a row
    ] [
        echo &quot;There are no elements with two e characters in a row
    ]

If your list is effectively an associative key table, you can use `listassoc` and variants, `listassoc=`, `listassoc=f`, and `listassoc=s` to look up the corresponding element; do note that this works only in a key + 1 element setup, like so.

    foods = [
        potato  vegetable
        carrot  vegetable
        apple   fruit
        bread   grain
        bacon   meat
    ]

`listassoc` takes the form of `listassoc` `id` `$list` `[body]`, and the variants the form `listassoc##` `$list` `value`. If we wanted to for example look up the classification of carrot as per the above list, we would write it as follows

    echo (listassoc id $foods [=s $id carrot])
    echo (listassoc=s $foods carrot)

Moving on, we now find ourselves at the need to delete items form the list. The facilities for this were rather limited until recently. There is the slow and manual process of deleting the desired elements yourself. There is also the amptly named `listdel` command, which accepts a list of elements which it indiscriminantly removes from the given list. For more controlled deletion, `listsplice` should be used.

As previously shown, `listsplice` can be used to remove items from lists. the secret to doing this is to use the `[remove]` argument and pass an empty argument for the new items to insert. As previously stated. the 3rd argument defines a skip, and the 4th argument completely ignores the next n words. for example, `listsplice` `$list` `()` `9` `2` will delete the 10th and 11th words, if they exist.

    //in this example, we will delete the third row.
    //we tell it to skip the first 10 elements, and to delete 5 of them from the 11th onward.

    matrix = [
        1 0 1 0 1
        0 1 0 0 1
        1 0 1 1 1
        1 1 1 0 1
        0 0 0 1 0
    ]
    echo (listsplice $matrix &quot;&quot; 10 5)

    //if formatting is preserved
    // 1 0 1 0 1
    // 0 1 0 0 1
    // 1 1 1 0 1
    // 0 0 0 1 0

    //else
    // 1 0 1 0 1 0 1 0 0 1 1 1 1 0 1 0 0 0 1 0

Like `indexof`, `listdel` wants an exact match, but unlike `indexof` you give the command a list. It will remove <strong>any</strong> and <strong>all</strong> matching instances from the list. An easy trick to escape an element that could be read as a list, eg a string, is to wrap it like so: `listdel` `$mylist` `(concatword` `"["` `$myelement` `"]")`. Whitespace isn't preserved; a new list is generated without the offending elements.

For this example we will use a 5x5 matrix with the elements 0, 1, and 2 random placed. We will be removing 0 and 2 for this example.

    matrix = [
        0 1 2 2 1
        1 2 2 1 0
        0 0 1 1 2
        1 1 1 1 0
        2 0 1 2 1
    ]
    echo (listdel $matrix [0 2])

    //note that listdel does NOT preserve whitespace
    // 1 1
    // 1 1
    // 1 1
    // 1 1 1 1
    // 1 1

There is also the sublist command, you use this command to extract portions of a list, this can in fact be used to extract two lists around the offending elements and assembling it to simulate an inefficient listsplice. `sublist` is best use if you need to extract a contiguous area of elements to act upon, such as a key with multiple elements setup.

    //an example of said list.
    list = [
        key item item item item
        key2 item item item item
    ]

`sublist` takes 3 arguments, first is of course, the list you wish to act upon, the second is where to start extracting a portion of the list, an argument of 0 corresponds to the start. the third is how many elements to go for. Generally speaking, `listsplice` is easier and faster to utilise to this end.

    // The thing to note here, is that we want to cut the first list from
    // 0...index and the second from index+1 onwards, so if we know where the
    // element is, we can remove it, no problem!

    list = [
        one two three THREE
        four five six
    ]

    echo (concat (sublist $list 0 3) (sublist $list 4))

There is one final item to cover on the subject of lists, and that is sorting them. It should be noted that there is almost never any use for this. The usefulness of this is pretty much limited to sorting an inventory automatically, or if you were doing a multiplayer game, ordering the scoreboard. The command `sortlist` is available, which is a CubeScript hook against an internal implementation of quicksort.

The command takes 4 arguments, like so `sortlist` `$list` `left` `right` `[body]`. left and right refer to the name of aliases that the routine will be using to compare list elements for sorting. Naturally, this means the body field is used to define a comparison function using the aliases you gave it for left and right. Let's consider the following example.

    // we have constructed a list of fragmented sentences.
    list = [
        &quot;a sentence&quot;
        &quot;a sentence contains multiple&quot;
        &quot;a sentence contains multiple words and punctuation marks&quot;
        &quot;a sentence contains&quot;
        &quot;a sentence contains multiple words and&quot;
        &quot;a sentence contains multiple words&quot;
        &quot;a sentence contains multiple words and punctuation&quot;
        &quot;a&quot;
    ]

    // We wanted a word pyramid so we are sorting by string length
    echo (sortlist $list a b [&lt; (strlen $a) (strlen $b) ])

Congrats, you now have a left-leaning pyramid and know all the basics of handling lists in CubeScript!

<h1 id="reading-command-prototypes">
Reading command prototypes

</h1>
As the documentation is woefully incomplete, you may be required to consult the actual source code from time to time. This little section is here to help you read and understand the command prototypes scattered throughout the code.
