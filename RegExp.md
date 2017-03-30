# Regular Expressions
________  
A **regular expression**, regex or regexp (sometimes called a rational expression) is, in theoretical computer science and formal language theory, a sequence of characters that define a search pattern. Usually this pattern is then used by string searching algorithms for "find" or "find and replace" operations on strings.
The concept arose in the 1950s when the American mathematician **Stephen Cole Kleene** formalized the description of a regular language. The concept came into common use with Unix text-processing utilities. Today, different syntaxes for writing regular expressions exist, one being the POSIX standard and another, widely used, being the Perl syntax.
Regular expressions are used in search engines, search and replace dialogs of word processors and text editors, in text processing utilities such as sed and AWK and in lexical analysis. Many programming languages provide regex capabilities, built-in, or via libraries.

## Creating a regular expression
A regular expression is a type of object. It can either be constructed with the RegExp constructor or written as a literal value by enclosing the pattern in forward slash (/) characters.
```javascript
var re1 = new RegExp("abc");
var re2 = /abc/;
```
Both of these regular expression objects represent the same pattern: an a character followed by a b followed by a c.
When using the RegExp constructor, the pattern is written as a normal string, so the usual rules apply for backslashes.
The second notation, where the pattern appears between slash characters, treats backslashes somewhat differently. First, since a forward slash ends the pattern, we need to put a backslash before any forward slash that we want to be part of the pattern. In addition, backslashes that aren’t part of special character codes (like \n) will be preserved, rather than ignored as they are in strings, and change the meaning of the pattern. Some characters, such as question marks and plus signs, have special meanings in regular expressions and must be preceded by a backslash if they are meant to represent the character itself.

## Testing for matches
Regular expression objects have a number of methods. The simplest one is test. If you pass it a string, it will return a Boolean telling you whether the string contains a match of the pattern in the expression.
```javascript
console.log(/abc/.test("abcde"));
// → true
console.log(/abc/.test("abxde"));
// → false
```

## Matching a set of characters
Finding out whether a string contains abc could just as well be done with a call to indexOf. Regular expressions allow us to go beyond that and express more complicated patterns.
Say we want to match any number. In a regular expression, putting a set of characters between square brackets makes that part of the expression match any of the characters between the brackets.
Both of the following expressions match all strings that contain a digit:
```javascript
console.log(/[0123456789]/.test("in 1992"));
// → true
console.log(/[0-9]/.test("in 1992"));
// → true
```
Within square brackets, a dash (-) between two characters can be used to indicate a range of characters, where the ordering is determined by the character’s Unicode number. Characters 0 to 9 sit right next to each other in this ordering (codes 48 to 57), so [0-9] covers all of them and matches any digit.
There are a number of common character groups that have their own built-in shortcuts. Digits are one of them: \d means the same thing as [0-9].
Special Characters | Significance
----|-----
\d | Any digit character
\w | An alphanumeric character (“word character”)
\s | Any whitespace character (space, tab, newline, and similar)
\D | A character that is not a digit
\W | A nonalphanumeric character
\S | A nonwhitespace character
. | Any character except for newline
So you could match a date and time format like 30-01-2003 15:20 with the following expression:
```javascript
var dateTime = /\d\d-\d\d-\d\d\d\d \d\d:\d\d/;
console.log(dateTime.test("30-01-2003 15:20"));
// → true
console.log(dateTime.test("30-jan-2003 15:20"));
// → false
```
These backslash codes can also be used inside square brackets. For example, [\d.] means any digit or a period character. But note that the period itself, when used between square brackets, loses its special meaning. The same goes for other special characters, such as +.
To invert a set of characters—that is, to express that you want to match any character except the ones in the set—you can write a caret (^) character after the opening bracket.
```javascript
var notBinary = /[^01]/;
console.log(notBinary.test("1100100010100110"));
// → false
console.log(notBinary.test("1100100010200110"));
// → true
```

## Repeating parts of a pattern
We now know how to match a single digit. What if we want to match a whole number—a sequence of one or more digits?
When you put a plus sign (+) after something in a regular expression, it indicates that the element may be repeated more than once. Thus, /\d+/ matches one or more digit characters.
```javascript
console.log(/'\d+'/.test("'123'"));
// → true
console.log(/'\d+'/.test("''"));
// → false
console.log(/'\d*'/.test("'123'"));
// → true
console.log(/'\d*'/.test("''"));
// → true
```
The star (*) has a similar meaning but also allows the pattern to match zero times. Something with a star after it never prevents a pattern from matching—it’ll just match zero instances if it can’t find any suitable text to match.
A question mark makes a part of a pattern “optional”, meaning it may occur zero or one time. In the following example, the u character is allowed to occur, but the pattern also matches when it is missing.
```javascript
var neighbor = /neighbou?r/;
console.log(neighbor.test("neighbour"));
// → true
console.log(neighbor.test("neighbor"));
// → true
```
To indicate that a pattern should occur a precise number of times, use curly braces. Putting {4} after an element, for example, requires it to occur exactly four times. It is also possible to specify a range this way: {2,4} means the element must occur at least twice and at most four times.

## Grouping subexpressions
To use an operator like * or + on more than one element at a time, you can use parentheses. A part of a regular expression that is enclosed in parentheses counts as a single element as far as the operators following it are concerned.
```javascript
var cartoonCrying = /boo+(hoo+)+/i;
console.log(cartoonCrying.test("Boohoooohoohooo"));
// → true
```
The first and second + characters apply only to the second o in boo and hoo, respectively. The third + applies to the whole group (hoo+), matching one or more sequences like that.
The i at the end of the expression in the previous example makes this regular expression case insensitive, allowing it to match the uppercase B in the input string, even though the pattern is itself all lowercase.

## Matches and groups
The test method is the absolute simplest way to match a regular expression. It tells you only whether it matched and nothing else. Regular expressions also have an exec (execute) method that will return null if no match was found and return an object with information about the match otherwise.
```javascript
var match = /\d+/.exec("one two 100");
console.log(match);
// → ["100"]
console.log(match.index);
// → 8
```
An object returned from exec has an index property that tells us where in the string the successful match begins. Other than that, the object looks like (and in fact is) an array of strings, whose first element is the string that was matched—in the previous example, this is the sequence of digits that we were looking for.
String values have a match method that behaves similarly.
```javascript
console.log("one two 100".match(/\d+/));
// → ["100"]
```
When the regular expression contains subexpressions grouped with parentheses, the text that matched those groups will also show up in the array. The whole match is always the first element. The next element is the part matched by the first group (the one whose opening parenthesis comes first in the expression), then the second group, and so on.
```javascript
var quotedText = /'([^']*)'/;
console.log(quotedText.exec("she said 'hello'"));
// → ["'hello'", "hello"]
```
When a group does not end up being matched at all (for example, when followed by a question mark), its position in the output array will hold undefined. Similarly, when a group is matched multiple times, only the last match ends up in the array.
```javascript
console.log(/bad(ly)?/.exec("bad"));
// → ["bad", undefined]
console.log(/(\d)+/.exec("123"));
// → ["123", "3"]
```
Groups can be useful for extracting parts of a string. If we don’t just want to verify whether a string contains a date but also extract it and construct an object that represents it, we can wrap parentheses around the digit patterns and directly pick the date out of the result of exec.

## The date type
JavaScript has a standard object type for representing dates—or rather, points in time. It is called Date. If you simply create a date object using new, you get the current date and time.
```javascript
console.log(new Date());
// → Wed Dec 04 2013 14:24:57 GMT+0100 (CET)
You can also create an object for a specific time.
console.log(new Date(2009, 11, 9));
// → Wed Dec 09 2009 00:00:00 GMT+0100 (CET)
console.log(new Date(2009, 11, 9, 12, 59, 59, 999));
// → Wed Dec 09 2009 12:59:59 GMT+0100 (CET)
```
JavaScript uses a convention where month numbers start at zero (so December is 11), yet day numbers start at one. This is confusing and silly. Be careful.
The getTime method on a date object returns this number. It is big, as you can imagine.
```javascript
console.log(new Date(2013, 11, 19).getTime());
// → 1387407600000
console.log(new Date(1387407600000));
// → Thu Dec 19 2013 00:00:00 GMT+0100 (CET)
```
If you give the Date constructor a single argument, that argument is treated as such a millisecond count. You can get the current millisecond count by creating a new Date object and calling getTime on it but also by calling the Date.now function.
Date objects provide methods like getFullYear, getMonth, getDate, getHours, getMinutes, and getSeconds to extract their components. There’s also getYear, which gives you a rather useless two-digit year value (such as 93 or 14).
Putting parentheses around the parts of the expression that we are interested in, we can now easily create a date object from a string.
```javascript
function findDate(string) {
  var dateTime = /(\d{1,2})-(\d{1,2})-(\d{4})/;
  var match = dateTime.exec(string);
  return new Date(Number(match[3]),
                  Number(match[2]) - 1,
                  Number(match[1]));
}
console.log(findDate("30-1-2003"));
// → Thu Jan 30 2003 00:00:00 GMT+0100 (CET)
```

## Word and string boundaries
Unfortunately, findDate will also happily extract the nonsensical date 00-1-3000 from the string "100-1-30000". A match may happen anywhere in the string, so in this case, it’ll just start at the second character and end at the second-to-last character.
If we want to enforce that the match must span the whole string, we can add the markers ^ and $. The caret matches the start of the input string, while the dollar sign matches the end. So, /^\d+$/ matches a string consisting entirely of one or more digits, /^!/ matches any string that starts with an exclamation mark, and /x^/ does not match any string (there cannot be an x before the start of the string).
If, on the other hand, we just want to make sure the date starts and ends on a word boundary, we can use the marker \b. A word boundary can be the start or end of the string or any point in the string that has a word character (as in \w) on one side and a nonword character on the other.
```javascript
console.log(/cat/.test("concatenate"));
// → true
console.log(/\bcat\b/.test("concatenate"));
// → false
```

## Choice patterns
Say we want to know whether a piece of text contains not only a number but a number followed by one of the words pig, cow, or chicken, or any of their plural forms.
We could write three regular expressions and test them in turn, but there is a nicer way. The pipe character (|) denotes a choice between the pattern to its left and the pattern to its right. So I can say this:
```javascript
var animalCount = /\b\d+ (pig|cow|chicken)s?\b/;
console.log(animalCount.test("15 pigs"));
// → true
console.log(animalCount.test("15 pigchickens"));
// → false
```
Parentheses can be used to limit the part of the pattern that the pipe operator applies to, and you can put multiple such operators next to each other to express a choice between more than two patterns.

## The replace method
String values have a replace method, which can be used to replace part of the string with another string.
```javascript
console.log("papa".replace("p", "m"));
// → mapa
```javascript
The first argument can also be a regular expression, in which case the first match of the regular expression is replaced. When a g option (for global) is added to the regular expression, all matches in the string will be replaced, not just the first.
```javascript
console.log("Borobudur".replace(/[ou]/, "a"));
// → Barobudur
console.log("Borobudur".replace(/[ou]/g, "a"));
// → Barabadar
```
It would have been sensible if the choice between replacing one match or all matches was made through an additional argument to replace or by providing a different method, replaceAll. But for some unfortunate reason, the choice relies on a property of the regular expression instead.
The real power of using regular expressions with replace comes from the fact that we can refer back to matched groups in the replacement string. For example, say we have a big string containing the names of people, one name per line, in the format Lastname, Firstname. If we want to swap these names and remove the comma to get a simple Firstname Lastname format, we can use the following code:
```javascript
console.log(
  "Hopper, Grace\nMcCarthy, John\nRitchie, Dennis"
    .replace(/([\w ]+), ([\w ]+)/g, "$2 $1"));
// → Grace Hopper
//   John McCarthy
//   Dennis Ritchie
```
The $1 and $2 in the replacement string refer to the parenthesized groups in the pattern. $1 is replaced by the text that matched against the first group, $2 by the second, and so on, up to $9. The whole match can be referred to with $&.
It is also possible to pass a function, rather than a string, as the second argument to replace. For each replacement, the function will be called with the matched groups (as well as the whole match) as arguments, and its return value will be inserted into the new string.

## Greed
It isn’t hard to use replace to write a function that removes all comments from a piece of JavaScript code. Here is a first attempt:
```javascript
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*\*\//g, "");
}
console.log(stripComments("1 + /* 2 */3"));
// → 1 + 3
console.log(stripComments("x = 10;// ten!"));
// → x = 10;
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1  1
```
The part before the or operator simply matches two slash characters followed by any number of non-newline characters. The part for multiline comments is more involved. We use [^] (any character that is not in the empty set of characters) as a way to match any character. We cannot just use a dot here because block comments can continue on a new line, and dots do not match the newline character.
But the output of the previous example appears to have gone wrong. Why?
The [^]* part of the expression, as I described in the section on backtracking, will first match as much as it can. If that causes the next part of the pattern to fail, the matcher moves back one character and tries again from there. In the example, the matcher first tries to match the whole rest of the string and then moves back from there. It will find an occurrence of */ after going back four characters and match that. This is not what we wanted—the intention was to match a single comment, not to go all the way to the end of the code and find the end of the last block comment.
Because of this behavior, we say the repetition operators (+, *, ?, and {}) are greedy, meaning they match as much as they can and backtrack from there. If you put a question mark after them (+?, *?, ??, {}?), they become nongreedy and start by matching as little as possible, matching more only when the remaining pattern does not fit the smaller match.
And that is exactly what we want in this case. By having the star match the smallest stretch of characters that brings us to a */, we consume one block comment and nothing more.
```javascript
function stripComments(code) {
  return code.replace(/\/\/.*|\/\*[^]*?\*\//g, "");
}
console.log(stripComments("1 /* a */+/* b */ 1"));
// → 1 + 1
```
A lot of bugs in regular expression programs can be traced to unintentionally using a greedy operator where a nongreedy one would work better. When using a repetition operator, consider the nongreedy variant first.
Dynamically creating RegExp objects
There are cases where you might not know the exact pattern you need to match against when you are writing your code. Say you want to look for the user’s name in a piece of text and enclose it in underscore characters to make it stand out. Since you will know the name only once the program is actually running, you can’t use the slash-based notation.

##The search method
The indexOf method on strings cannot be called with a regular expression. But there is another method, search, which does expect a regular expression. Like indexOf, it returns the first index on which the expression was found, or -1 when it wasn’t found.
```javascript
console.log("  word".search(/\S/));
// → 2
console.log("    ".search(/\S/));
// → -1
```
Unfortunately, there is no way to indicate that the match should start at a given offset (like we can with the second argument to indexOf), which would often be useful.

## The lastIndex property
The exec method similarly does not provide a convenient way to start searching from a given position in the string. But it does provide an inconvenient way.
Regular expression objects have properties. One such property is source, which contains the string that expression was created from. Another property is lastIndex, which controls, in some limited circumstances, where the next match will start.
Those circumstances are that the regular expression must have the global (g) option enabled, and the match must happen through the exec method. Again, a more sane solution would have been to just allow an extra argument to be passed to exec, but sanity is not a defining characteristic of JavaScript’s regular expression interface.
```javascript
var pattern = /y/g;
pattern.lastIndex = 3;
var match = pattern.exec("xyzzy");
console.log(match.index);
// → 4
console.log(pattern.lastIndex);
// → 5
```
If the match was successful, the call to exec automatically updates the lastIndex property to point after the match. If no match was found, lastIndex is set back to zero, which is also the value it has in a newly constructed regular expression object.
When using a global regular expression value for multiple exec calls, these automatic updates to the lastIndex property can cause problems. Your regular expression might be accidentally starting at an index that was left over from a previous call.
```javascript
var digit = /\d/g;
console.log(digit.exec("here it is: 1"));
// → ["1"]
console.log(digit.exec("and now: 1"));
// → null
```
Another interesting effect of the global option is that it changes the way the match method on strings works. When called with a global expression, instead of returning an array similar to that returned by exec, match will find all matches of the pattern in the string and return an array containing the matched strings.

## Looping over matches
A common pattern is to scan through all occurrences of a pattern in a string, in a way that gives us access to the match object in the loop body, by using lastIndex and exec.
```javascript
var input = "A string with 3 numbers in it... 42 and 88.";
var number = /\b(\d+)\b/g;
var match;
while (match = number.exec(input))
  console.log("Found", match[1], "at", match.index);
// → Found 3 at 14
//   Found 42 at 33
//   Found 88 at 40
```
This makes use of the fact that the value of an assignment expression (=) is the assigned value. So by using match = number.exec(input) as the condition in the while statement, we perform the match at the start of each iteration, save its result in a variable, and stop looping when no more matches are found.

## Summary
Regular expressions are objects that represent patterns in strings. They use their own syntax to express these patterns.
Expression | Meaning
----- | -----
/abc/ | A sequence of characters
/[abc]/ | Any character from a set of characters
/[^abc]/ | Any character not in a set of characters
/[0-9]/ | Any character in a range of characters
/x+/ | One or more occurrences of the pattern x
/x+?/ | One or more occurrences, nongreedy
/x*/ | Zero or more occurrences
/x?/ | Zero or one occurrence
/x{2,4}/ | Between two and four occurrences
/(abc)/ | A group
/a\|b\|c/ | Any one of several patterns
/\d/ | Any digit character
/\w/ | An alphanumeric character (“word character”)
/\s/ | Any whitespace character
/./ | Any character except newlines
/\b/ | A word boundary
/^/ | Start of input
/$/ | End of input
A regular expression has a method test to test whether a given string matches it. It also has an exec method that, when a match is found, returns an array containing all matched groups. Such an array has an index property that indicates where the match started.
Strings have a match method to match them against a regular expression and a search method to search for one, returning only the starting position of the match. Their replace method can replace matches of a pattern with a replacement string. Alternatively, you can pass a function to replace, which will be used to build up a replacement string based on the match text and matched groups.
Regular expressions can have **options**, which are written after the closing slash. The i option makes the match case insensitive, while the g option makes the expression global, which, among other things, causes the replace method to replace all instances instead of just the first.
The RegExp constructor can be used to create a regular expression value from a string.
Regular expressions are a sharp tool with an awkward handle. They simplify some tasks tremendously but can quickly become unmanageable when applied to complex problems. Part of knowing how to use them is resisting the urge to try to shoehorn things that they cannot sanely express into them.
