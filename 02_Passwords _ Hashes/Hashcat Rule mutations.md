<div class="page"><div id="dw__toc" class="dw__toc"><div aria-expanded="true"><div class="li">Rule-based Attack</div></div></div>

## Description

<div class="level2">

The rule-based attack (-a 0 with one or more -r rules files) is one of the most complicated of all the attack modes. The reason for this is very simple. The rule-based attack is like a **programming language** designed for password candidate generation. It has functions to modify, cut or extend words and has conditional operators to skip some, etc. That makes it the most flexible, accurate and efficient attack.

</div>

## Why not stick to regular expressions

<div class="level2">

Why re-invent the wheel? Simple answer: regular expressions are too slow. Typically we have to generate **1.000.000.000** (or more) fresh password candidates in less than **10 ms** before hashing algorithms start to become idle, and then again and again, second after second. Just take a look at your GPU speed display to get an idea of it.

</div>

## Compatibility with other rule engines

<div class="level2">

The rule-engine in [Hashcat](/wiki/doku.php?id=hashcat "hashcat") was written so that all functions that share the same letter-name are 100% compatible to [John the Ripper](http://www.openwall.com/john) and [PasswordsPro](http://www.insidepro.com/eng/passwordspro.shtml) rules and vice versa. Later we started to introduce some of our own functions that are not compatible. But these functions got their own letter-names to avoid conflicts.

</div>

## Implemented compatible functions

<div class="level2">

The following functions are 100% compatible to John the Ripper and PasswordsPro. (However, note that while the \*functions\* are compatible, their “arguments” (the character sets that can be passed to them) may differ, which means that one cannot just take a list of complex John the Ripper rulesets and run them with hashcat.)

<div class="table sectionedit6">

| Name | Function | Description | Example Rule | Input Word | Output Word | Note |
| --- | --- | --- | --- | --- | --- | --- |
| Nothing | :   | Do nothing (passthrough) | :   | p@ssW0rd | p@ssW0rd |     |
| Lowercase | l   | Lowercase all letters | l   | p@ssW0rd | p@ssw0rd |     |
| Uppercase | u   | Uppercase all letters | u   | p@ssW0rd | P@SSW0RD |     |
| Capitalize | c   | Capitalize the first letter and lower the rest | c   | p@ssW0rd | P@ssw0rd |     |
| Invert Capitalize | C   | Lowercase first found character, uppercase the rest | C   | p@ssW0rd | p@SSW0RD |     |
| Toggle Case | t   | Toggle the case of all characters in word. | t   | p@ssW0rd | P@SSw0RD |     |
| Toggle @ | TN  | Toggle the case of characters at position N | T3  | p@ssW0rd | p@sSW0rd | *   |
| Reverse | r   | Reverse the entire word | r   | p@ssW0rd | dr0Wss@p |     |
| Duplicate | d   | Duplicate entire word | d   | p@ssW0rd | p@ssW0rdp@ssW0rd |     |
| Duplicate N | pN  | Append duplicated word N times | p2  | p@ssW0rd | p@ssW0rdp@ssW0rdp@ssW0rd |     |
| Reflect | f   | Duplicate word reversed | f   | p@ssW0rd | p@ssW0rddr0Wss@p |     |
| Rotate Left | {   | Rotate the word left. | {   | p@ssW0rd | @ssW0rdp |     |
| Rotate Right | }   | Rotate the word right | }   | p@ssW0rd | dp@ssW0r |     |
| Append Character | $X  | Append character X to end | $1$2 | p@ssW0rd | p@ssW0rd12 |     |
| Prepend Character | ^X  | Prepend character X to front | ^2^1 | p@ssW0rd | 12p@ssW0rd |     |
| Truncate left | \[  | Delete first character | \[  | p@ssW0rd | @ssW0rd |     |
| Truncate right | \]  | Delete last character | \]  | p@ssW0rd | p@ssW0r |     |
| Delete @ N | DN  | Delete character at position N | D3  | p@ssW0rd | p@sW0rd | *   |
| Extract range | xNM | Extract M characters, starting at position N | x04 | p@ssW0rd | p@ss | \* # |
| Omit range | ONM | Delete M characters, starting at position N | O12 | p@ssW0rd | psW0rd | *   |
| Insert @ N | iNX | Insert character X at position N | i4! | p@ssW0rd | p@ss!W0rd | *   |
| Overwrite @ N | oNX | Overwrite character at position N with X | o3$ | p@ssW0rd | p@s$W0rd | *   |
| Truncate @ N | 'N  | Truncate word at position N | '6  | p@ssW0rd | p@ssW0 | *   |
| Replace | sXY | Replace all instances of X with Y | ss$ | p@ssW0rd | p@$$W0rd |     |
| Purge | @X  | Purge all instances of X | @s  | p@ssW0rd | p@W0rd |     |
| Duplicate first N | zN  | Duplicate first character N times | z2  | p@ssW0rd | ppp@ssW0rd |     |
| Duplicate last N | ZN  | Duplicate last character N times | Z2  | p@ssW0rd | p@ssW0rddd |     |
| Duplicate all | q   | Duplicate every character | q   | p@ssW0rd | pp@@ssssWW00rrdd |     |
| Extract memory | XNMI | Insert substring of length M starting from position N of word saved to memory at position I | lMX428 | p@ssW0rd | p@ssw0rdw0 | +   |
| Append memory | 4   | Append the word saved to memory to current word | uMl4 | p@ssW0rd | p@ssw0rdP@SSW0RD | +   |
| Prepend memory | 6   | Prepend the word saved to memory to current word | rMr6 | p@ssW0rd | dr0Wss@pp@ssW0rd | +   |
| Memorize | M   | Memorize current word | lMuX084 | p@ssW0rd | P@SSp@ssw0rdW0RD | +   |

</div>

- <div class="li">\* Indicates that N starts at 0. For character positions other than 0-9 use A-Z (A=10)</div>
- <div class="li">\+ Indicates that this rule is implemented in hashcat-legacy (non-OpenCL CPU) only.</div>
- <div class="li">\# Changed in oclHashcat v1.37 → v1.38 and hashcat v0.51 → v0.52</div>

</div>

## Rules used to reject plains

<div class="level2"><div class="table sectionedit8">

| Name | Function | Description | Example Rule | Note |
| --- | --- | --- | --- | --- |
| Reject less | <N  | Reject plains if their length is greater than N | <G  | *   |
| Reject greater | >N  | Reject plains if their length is less than N | >8  | *   |
| Reject equal | _N  | Reject plains of length not equal to N | _7  | *   |
| Reject contain | !X  | Reject plains which contain char X | !z  |     |
| Reject not contain | /X  | Reject plains which do not contain char X | /e  |     |
| Reject equal first | (X  | Reject plains which do not start with X | (h  |     |
| Reject equal last | )X  | Reject plains which do not end with X | )t  |     |
| Reject equal at | =NX | Reject plains which do not have char X at position N | =1a | *   |
| Reject contains | %NX | Reject plains which contain char X less than N times | %2a | *   |
| Reject contains | Q   | Reject plains where the memory saved matches current word | rMrQ | e.g. for palindrome |

</div>

Note: Reject rules only work either with hashcat-legacy, or when using “-j” or “-k” with hashcat. They will not work as regular rules (in a rule file) with hashcat.

- <div class="li">\* Indicates that N starts at 0. For character positions other than 0-9 use A-Z (A=10)</div>

</div>

## Implemented specific functions

<div class="level2">

The following functions are not available in John the Ripper and/or PasswordsPro:

<div class="table sectionedit10">

| Name | Function | Description | Example Rule | Input Word | Output Word | Note |
| --- | --- | --- | --- | --- | --- | --- |
| Swap front | k   | Swap first two characters | k   | p@ssW0rd | @pssW0rd |     |
| Swap back | K   | Swap last two characters | K   | p@ssW0rd | p@ssW0dr |     |
| Swap @ N | *NM | Swap character at position N with character at position M | *34 | p@ssW0rd | p@sWs0rd | *   |
| Bitwise shift left | LN  | Bitwise shift left character @ N | L2  | p@ssW0rd | p@æsW0rd | *   |
| Bitwise shift right | RN  | Bitwise shift right character @ N | R2  | p@ssW0rd | p@9sW0rd | *   |
| Ascii increment | +N  | Increment character @ N by 1 ascii value | +2  | p@ssW0rd | p@tsW0rd | *   |
| Ascii decrement | -N  | Decrement character @ N by 1 ascii value | -1  | p@ssW0rd | p?ssW0rd | *   |
| Replace N + 1 | .N  | Replace character @ N with value at @ N plus 1 | .1  | p@ssW0rd | psssW0rd | *   |
| Replace N - 1 | ,N  | Replace character @ N with value at @ N minus 1 | ,1  | p@ssW0rd | ppssW0rd | *   |
| Duplicate block front | yN  | Duplicate first N characters | y2  | p@ssW0rd | p@p@ssW0rd | *   |
| Duplicate block back | YN  | Duplicate last N characters | Y2  | p@ssW0rd | p@ssW0rdrd | *   |
| Title | E   | Lower case the whole line, then upper case the first letter and every letter after a space | E   | p@ssW0rd w0rld | P@ssw0rd W0rld | +   |
| Title w/separator | eX  | Lower case the whole line, then upper case the first letter and every letter after a custom separator character | e-  | p@ssW0rd-w0rld | P@ssw0rd-W0rld | +   |
| Title w/Nth separator | 3NX | Upper case the letter after the Nth instance of a char | 30- | pass-word | pass-Word | *   |

</div>

- <div class="li">\* Indicates that N starts at 0. For character positions other than 0-9 use A-Z (A=10)</div>
- <div class="li">\+ Only in JtR?</div>
- <div class="li">\# In beta or not yet released</div>

</div>

## Using hex bytes in rules

<div class="level2">

You can use '\\xNN' syntax to use hex bytes in rules.

<pre class="code">\> echo "Penguin" | ./hashcat.exe -j '$\\x64' --stdout
Penguind</pre>

This is useful for non-printable <abbr title="American Standard Code for Information Interchange">ASCII</abbr> characters, multibyte work, etc.

</div>

## Writing rules

<div class="level2">

The most important thing in writing rules is knowing what you want to write. That typically means you have to analyze dozens of plaintext passwords, maybe from a customer, to see a pattern. For example, a common pattern is that people append a digit to their passwords to improve its strength. So we have two “parameters”:

- <div class="li">We want to *append* something.</div>
- <div class="li">The value we want to append is a *digit*.</div>

If we take a look at the function overview we see that we can append something using the '$' function. So, for example, if we want to add a “1” to our password, we write a rule that looks like this.

<pre class="code">$1</pre>

Simple enough. But what if we want to do all numbers 1 - 9? Well thats what we call a [Hybrid attack](/wiki/doku.php?id=hybrid_attack "hybrid_attack"), just take a look at this page.

Also note:

- <div class="li">White spaces are ignored as long as they are not used as a parameter. That enables formating of our “source code” a bit.</div>
- <div class="li">To comment out some text, it has to start with a “#” char.</div>

</div>

## Generating rules

<div class="level2">

There are two ways to automatically “generate” rules.

- <div class="li">Using the maskprocessor, we have a dedicated article on this topic here: [Using maskprocessor to create rules](/wiki/doku.php?id=rules_with_maskprocessor "rules_with_maskprocessor").</div>
- <div class="li">Using random rules, see below.</div>

</div>

## Random rules

<div class="level2">

This is a very unique hashcat feature. With hashcat you can generate random rules on the fly to be used for that session. This is a good thing if you are out of ideas on what to do next when you have already tried all your rules on all your dictionaries. There are three configuration parameters:

Tells hashcat to generate NUM rules to be applied to each attempt:

<pre class="code">--generate-rules=NUM</pre>

Specifies the number of functions that should be used (minimum to maximum range):

<pre class="code">--generate-rules-func-min=NUM 
--generate-rules-func-max=NUM</pre>

You can also specify the pool of functions that the rule engine will select from:

<pre class="code">--generate-rules-func-sel=\[function list\]</pre>

… such as:

<pre class="code">--generate-rules-func-sel=ioTlc</pre>

This number can be unlimited but large numbers are not recommended. When used in conjunction with –g, any rule outside of this setting will be ignored.

For example, it could randomly generate the rules “l r”, “l ^f”, and “sa@”, these are all valid rules to be used. However, “l ^f sa@ r $3” would be ignored as it contains 5 functions. Default: min=1 max=4

You can mix your rules from a rules-file with randomly generated rules. For example, if your rules-file contains 200 rules and you use -g 500, it will generate 300 additional rules.

</div>

## Saving matched rules

<div class="level2">

This becomes handy especially in combination with the rules generator but also for statistical analysis of your rule sets.

To save any rule that generated a matched password use these switches:

<pre class="code">--debug-mode=1 --debug-file=matched.rule</pre>

This will save the matched rule on every match, so the resulting rule file might contain many duplicate rules.

At high rates of cracking per second, this may slow down cracking a little bit. At lower rates of cracking per second, the impact is probably negligible.

</div>

## Debugging rules

<div class="level2">

With hashcat we can debug our rules easily. That means we can verify that the rule we wrote actually does what we want it to do. All you need to use is the --stdout switch and omit the hashlist.

Here is an example:

Create simple dictionary:

<pre class="code">$ echo WORd > word</pre>

Generate a simple rule. The “c” rule capitalizes the first letter and lower-cases the rest.

<pre class="code">$ echo c > rule</pre>

And thats how we see the generated debug output:

<pre class="code">$ ./hashcat-cli64.bin -r rule --stdout word
Word</pre>

This “feature” is also a very fast password candidate generator. That means that if we have some external program that supports reading from stdin we can feed it with our output.

</div>

## Using 'p' (Nth instance of a character) with positional rules

<div class="level2">

The 'p' character position code (see Github [here](https://github.com/hashcat/hashcat/issues/1035)) memorizes the position of the Nth instance of a character, and makes that position available for use in rules that work with positions within the string. 'p' becomes available automatically when using '%' (reject plains unless they have at least X occurrences of a specific character) and '/' (reject plains that do not contain a specific character).

If you only want to operate on the first instance of a character, you can use %1s, or simply use '/':

<pre class="code">$ echo -n 'Odessa77' | hashcat --stdout -j '%1s Dp ip$'
Ode$sa77
$ echo -n 'Odessa77' | hashcat --stdout -j '/s Dp ip$'
Ode$sa77</pre>

If you only want to operate on the second instance:

<pre class="code">$ echo -n 'Odessa77' | hashcat --stdout -j '%2s Dp ip$'
Odes$a77</pre>

… etc. Unlike the 's' rule, which replaces **all** instances of a character, this allows selection of which instance of a character to replace.

You can also use this functionality to replace one character with multiple characters:

<pre class="code">$ echo -n 'p@ssW0rd' | hashcat --stdout -j '%1W DpM ip/ ip\ ip/ ip\\'
p@ss\\/\\/0rd</pre>

Note that rules that use the 'p' character position code only works using the '-j' or '-k' command-line options.

In the examples below, the '%2s' portion of the rule means “reject plains unless they have at least two 's' characters:, and the 'p' then means “operate on the position represented by the second 's' character”.

<div class="table sectionedit18">

| Name | Function | Description | Example Rule | Input Word | Output Word | Note |
| --- | --- | --- | --- | --- | --- | --- |
| Toggle @ | Tp  | Toggle the case of characters at position p | %2s Tp | p@s.sW0rd | p@s.SW0rd |     |
| Delete @ p | Dp  | Deletes character at position p | %2s Dp | p@s.sW0rd | p@s.W0rd |     |
| Extract range | xpM | Extracts M characters, starting at position p | %2s xp4 | p@s.sW0rd | sW0r |     |
| Omit range | OpM | Deletes M characters, starting at position p | %2s Op2 | p@s.sW0rd | p@s.0rd |     |
| Insert @ p | ipX | Inserts character X at position p | %2s ip! | p@s.sW0rd | p@s.!sW0rd |     |
| Overwrite @ p | opX | Overwrites character at position p with X | %2s op$ | p@s.sW0rd | p@s.$W0rd |     |
| Truncate @ p | 'p  | Truncate word at position p | %2s 'p | p@s.sW0rd | p@s. |     |
| Extract memory | XpMI | Insert substring of length M starting from position p of word saved to memory at position I | %2s lMXp28 | p@s.sW0rd | p@s.sw0rswd |     |
| Swap @ p | *pM | Swaps character at position p with character at position M | %2s *p4 | p@s.sW0rd | p@s.sW0rd |     |
| Bitwise shift left | Lp  | Bitwise shift left character @ p | %2s Lp | p@s.sW0rd | p@s.▒W0rd |     |
| Bitwise shift right | Rp  | Bitwise shift right character @ p | %2s Rp | p@s.sW0rd | p@s.9W0rd |     |
| <abbr title="American Standard Code for Information Interchange">ASCII</abbr> increment | +p  | Increment character @ p by 1 <abbr title="American Standard Code for Information Interchange">ASCII</abbr> value | %2s +p | p@s.sW0rd | p@s.tW0rd |     |
| <abbr title="American Standard Code for Information Interchange">ASCII</abbr> decrement | -p  | Decrement character @ p by 1 <abbr title="American Standard Code for Information Interchange">ASCII</abbr> value | %2s -p | p@s.sW0rd | p@s.rW0rd |     |
| Replace p + 1 | .p  | Replaces character @ p with value at @ p plus 1 | %2s .p | p@s.sW0rd | p@s.WW0rd |     |
| Replace p - 1 | ,p  | Replaces character @ p with value at @ p minus 1 | %2s ,p | p@s.sW0rd | p@s..W0rd |     |

</div>

See rules/ folder in hashcat-legacy or [hashcat](/wiki/doku.php?id=hashcat "hashcat") for examples.

</div>

## Multi-rules

<div class="level2">

With release of old [oclHashcat-plus](/wiki/doku.php?id=oclhashcat_plus "oclhashcat_plus") v0.07 a complete new feature in the rule-based cracking world was added.

Instead of just giving one -r parameter and a file, you can now add as many -r's as you want.

They are not executed in a sequence!

Each rule of each rule-file is combined with each rule of each rule-file. This way you can easily cook your own attack mode.

<pre class="code">$ cat 123.rule
$1
$2
$3</pre>

$ cat abc.rule
$a $b
$c

$ hashcat --stdout -r 123.rule -r abc.rule wordlist
hashcat1a
hashcat2a
hashcat3a
hashcat1b
hashcat2b
hashcat3b
hashcat1c
hashcat2c
hashcat3c

Because the total number of generated rules is the product of all lists, stacking multiple large lists can quickly exceed available memory. But a few well-chosen rules can be stacked to great effect.

</div>

## Supported by

<div class="level2">

This attack is currently supported by:

- <div class="li">[hashcat](/wiki/doku.php?id=hashcat "hashcat")</div>
- <div class="li">hashcat-legacy</div>

</div>

## Limitations

<div class="level2">

With hashcat, the number of functions of a single rule line and the overall number of functions of multi-rules is limited to 31.

</div>

</div>