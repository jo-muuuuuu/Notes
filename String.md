## String

### String.prototype.slice()
slice() extracts the text from one string and returns a new string.

```
const str = "helloworld";
```

slice() extracts up to but not including indexEnd. For example, str.slice(2,5) extracts the fifth character through the eighth character (characters indexed 2, 3, and 4):
```
console.log(str.slice(2, 5));
// Output: llo
```

If indexStart >= str.length, an empty string is returned.
```
console.log(str.slice(1, 1));
// Output:                                                   (empty)

console.log(str.slice(2, 1));
// Output:                                                   (empty)
```

If indexStart < 0, the index is counted from the end of the string. More formally, in this case, the substring starts at max(indexStart + str.length, 0).
```
console.log(str.slice(-3));
// Output: rld

console,log(str.slice(-20));
//Output: helloworld
```

If indexStart is omitted, undefined, or cannot be converted to a number, it's treated as 0.
```
console.log(str.slice(undefined, 5));
// Output: hello
```

If indexEnd is omitted or undefined, or if indexEnd >= str.length, slice() extracts to the end of the string.
```
console.log(str.slice(5));
// Output: world
```

If indexEnd < 0, the index is counted from the end of the string. More formally, in this case, the substring ends at max(indexEnd + str.length, 0).
```
console.log(str.slice(-3, -1));
// Output: rl
```

If indexEnd <= indexStart after normalizing negative values (i.e., indexEnd represents a character that's before indexStart), an empty string is returned.
```
console.log(str.slice(-3, -5));
// Output:                                                   (empty)

console,log(str.slice(5, 3));
// Output:                                                   (empty)
```
