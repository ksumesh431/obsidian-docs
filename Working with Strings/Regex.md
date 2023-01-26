grep, sed, awk can use regex
java, py, js etc can use regex

---


`a*`  **Zero or more occurrences** of "a"
Example `fooa*bar` matches 
``` 
fooaaaabar
fooabar
foobar
fooaabar
```

<br/>

---
<br/>


`  .  ` the dot/ single wildcard represents any ONE character in a single position

Example `foo.bar`  matches
```
fooabar
fooxbar
foocbar
```
and not "foobar" or "fooxybar" or "baryfoo"

<br/>

---

<br/>

`  .*  `  for ANY CHARACTER having ZERO OR MORE OCCOURENCES 
<u>Example</u> ![](Images/Pasted%20image%2020230126150659.png)

<br/>

---
<br/>


`  \s  ` represents **whitespaces**
Can combine with *  like ` \s* ` for Zero or More OCCOURENCES of whitespaces

<u>Example</u>  ![](Images/Pasted%20image%2020230126150719.png)

<br/>

---
<br/>


` [skp] ` Square brackets/ Character class represent ONE OF THE CHARACTERS IN SQAURE BRACKETS - s, k or p

<u>Example</u> ![](Images/Pasted%20image%2020230126150742.png)



` [^abc] ` The ^ or caret symbol represents exclusion/negation **<u>ONLY INSIDE SQUARED BRACKETS</u>**. 

<u>Example</u>![](Images/Pasted%20image%2020230126150809.png)

<br/>

---
<br/>


` [a-c] ` Range that is one of the characters falling in the range given in square brackets
> **ASCII value of range starting character must be less than the range ending character**

<u>Example</u> ![](Images/Pasted%20image%2020230126150844.png)


<br/>

---
<br/>



> Combination of range and characters in a character class like  `[a-cx]` 

<u>Example</u> ![](Images/Pasted%20image%2020230126150917.png)



<br/>


---
<br/>

` ^ $ * . [ () \ `  Are special characters that must be escaped using `\`  if want to use as Literal in a string.

<u>Example</u> ![](Images/Pasted%20image%2020230126150940.png)

<br/>

## **<u>EXCEPTION</u>**

> Symbols like ` . `  doesn't need to be escaped inside of square brackets [ ]
>  Tho some symbols like ` ^ or - ` need to be escaped inside [ ] too as they have special meaning like Negations and Range inside of [ ]

![](Images/Pasted%20image%2020230126151038.png)

> Escaping the escape character ` \ ` 
![](Images/Pasted%20image%2020230126151101.png)

<br/>

---
<br/>


` ^x ` The Caret symbol is a placeholder that signifies Beginning of line. The regex pattern will match the strings that begin with "x".
> Works only outside the [ ]. Inside the [ ], it means the Negation symbol.

<u>Example</u> ![](Images/Pasted%20image%2020230126151128.png)

<br/>

---
<br/>

` x$ ` The Dollar symbol is a placeholder that signifies the end of a line. It must be placed at the end of the string too.

<u>Example</u>  ![](Images/Pasted%20image%2020230126151147.png)

> Combination of ` ^ and $ `  
> ![](Images/Pasted%20image%2020230126151206.png)