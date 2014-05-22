Ruby Regular Expressions
========================

Introduction
------------

Regular expressions are basic ruby classes, and are represented between
/slashes/. They usually appear coupled to the match operator ~. The match
operator returns an integer containing the position of the match inside the
string. When there is no match, the result is nil.

```ruby
# The match operator returns either an integer or nil.
>> /abc/ =~ "abc"
=> 0

# the regexp does not have to stay on the left.
# ruby evaluates nil as false, so we can write:
>> puts 'match'if "abc" =~ /abc/
match
=> nil

# of course, case matters.
>> /word/ =~ "Word"
=> nil
```

Special Characters
------------------
note: TODO: add tabular.vim

| Character | meaning |
| --------- | ------- | 
| ^         | Beginning of the string.|
| $         | End of the string.|
| \d        | Any Digit character.|
| \D        | Any non-digit character.|
| \s        | Whitespace character|
| \S        | Any other character but a whitespace.|
| \w        | A “word character”: [A-Za-z0-9_]|
| \W        | Any character except a word character.|
| . (dot)   | matches any character.|
| \*        | matches zero or more occurrences of the character preceding it.|
| + | matches one or more occurrences of the character preceding it.|
| ? | matches zero or one occurrences of the character preceding it.|
| [0123456789] | brackets define character classes. Inside brackets, characters like .+* are not special.|
| [0-9][a-f] | The dash can be used to represent ranges.|
| \ | escapes a character.|
| [^abc] | Inside brackets, ^ causes the set to contain all characters except the ones listed.|
| (abc) | Parentheses define groups (see how it works on the Grouping section)|
| \| | A vertical bar provides alternatives (see how it works on the Alternatives section)|

Grouping
--------

 Parentheses define groups. Special characters at the right of the group apply
 to the whole group. Special characters inside the group will apply only to the
 chacacter it precedes:

```ruby
# here we match one a followed by zero or more b's one or more times.
>> /(ab*)+/ =~ "aababbabbb"
=> 0
```

Alternatives
------------
 A Vertical bar divides the regular expressions in two, in a way that if either
 one of the sub-expressions match, then the whole expression will match.

```ruby
# This regular expression does not mean “Match either 'Fine birds ate.' or 'Fine cows ate.'”
# It actually matches either a string beginning with "Fine birds" or one ending in "cows ate."
>> /^Fine birds|cows ate\.$/ =~ "Fine birds ate seeds."
=> 0
 
# This regular expression matches only the two alternate sentences,
# not the infinite number of possibilities the previous example’s regexp does.
 >> /^Fine (birds|cows) ate\.$/ =~ "Fine birds ate seeds."
 => nil
 >> /^Fine (birds|cows) ate\.$/ =~ "Fine birds ate."
 => 0
```

Breaking strings apart
----------------------
```ruby
# the match method returns a matchdata object if it matches, otherwise it returns nil.
# we can pull information from that object.
>> re = /(\w+), (\w+), or (\w+)/
=> /(\w+), (\w+), or (\w+)/
>> s = 'Without a Bob, ox, or bin!'
=> "Without a Bob, ox, or bin!"
>> match = re.match(s)
=> #<MatchData "Bob, ox, or bin" 1:"Bob" 2:"ox" 3:"bin">

# a MatchData is indexable. the first position contains the entire match
>> match[0]
=> "Bob, ox, or bin"

# each of the other indexes contains the matched groups
>> match[1]
=> "Bob"

# groups are often used to pull apart strings and construct new ones.
>> "#{match[3]} and #{match[1]}"
=> "bin and Bob"

# pre_match returns any portion of the string before the part that matched.
>> match.pre_match
=> "Without a "

# post_match returns any portion of the string after the part that matched.
>> match.post_match
=> "!"
# The plus and star special characters are greedy: they match as many characters as they can.
# Expect that to catch you by surprise sometimes.
>> str = "a bee in my bonnet"
=> "a bee in my bonnet"
>> /a.*b/.match(str)[0]
=> "a bee in my b"

# we can change this behavior making plus and star match as few characters as they can
# by suffixing them with a ques- tion mark.
>> /a.*?b/.match(str)[0]
=> "a b"

# You can use a regular expression to slice a string.
# The result is the first substring that matches the regular expression.
>> "has 5 and 3"[/\d+/]
=> "5"
```

Variables Behind the Scenes
---------------------------

Both =~ and match set some variables. All begin with $. Each parenthesized
group gets its own number, from $1 up through $9. You might expect $0 to name
the entire string that matched, but it’s already used for something else: the
name of the program being executed. To workaround this, $& is the equivalent of
match[0]. $‘ + $' are used to get the string before and after the match.

```ruby
# using the same re and s from previoue example.
>> re =~ s
=> 10
>> [$1, $2, $3, $&, $`, $']
=> ["Bob", "ox", "bin", "Bob, ox, or bin", "Without a ", "!"]
 
# This might be useful in situations like this:
 if name =~/(._), (.+)/
     name = "#{$2} #{$1}"
     end
```

Regular Expression Options
--------------------------
 We can set a few options to make our lives easier.

```ruby
# Normally, the period in a regular expression does not match the end-of-line character.
# Therefore, .* or .+ matches won’t span lines.
>> /a.*b/ =~ "az\nzb"
=> nil
 
# Adding the m (multiline) option makes a period match end-of-line characters,
# so the regular expression match can span lines.
>> /a.*b/m =~ "az\nzb"
=> 0

# This is a far too annoying way to do a case- insensitive match.
>> /[cC][aA][tT]/ =~ "Cat"
=> 0

# The i (insensitive) option is a better way.
>> /cat/i =~ "Cat"
=> 0
```
