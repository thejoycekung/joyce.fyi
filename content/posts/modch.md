---
title: "Building a chmod calculator in Go"
date: 2020-09-25T21:36:28-04:00
categories:
- tutorial
tags:
- linux
- go
---

One of the things that really irked me as I started to learn more about Linux was using the `chmod` command.

When I first learned the `chmod` command in school, we learned to explicity declare the permissions:
```sh
$ chmod a+x file.txt
```

However, whenever running through tutorials or, really, anything that involved `chmod`, I found that most people like to use the numeric (octal) format instead[^1]:
```sh
$ chmod 777 file.txt
```

[^1]: Later on (like - really later on, this year, actually) I discovered that passing `-v`, or `--verbose` would show what the explicit permissions are.
    ```sh
    $ chmod -v 555 file.txt
    mode of 'file.txt' changed from 0777 (rwxrwxrwx) to 0555 (r-xr-xr-x)
    ```

I knew there was some kind of math involved with `chmod`, so I decided it would be fun to try [writing a `chmod` calculator in Go](https://github.com/thejoycekung/tinkering/tree/master/modch), a language I started learning this summer.

But before I could actually write it, I had to understand how the `chmod` permissions worked. (If you're only interested in the Go, [skip ahead](#coding-it-up).)

# Understanding Unix permissions[^2]

[^2]: Julia Evans also has [an amazing zine about Unix permissions](https://twitter.com/b0rk/status/982641594305273856?lang=en). She's also much more succinct than I.

File permissions can be represented using 9 characters. For example:
```sh
r-x---rwx
```

Since it's 9 characters long, we can easily split it into 3 roles: user, group, and others.
* `user`: the owner of the file
* `group`: the group the file is assigned to
* `others`: users that fall into neither of `user` or `group`

```sh
(user) (group) (others)
 r-x     ---     rwx
```

Within each role, we then have 3 available slots for actions[^3]:
1. `r`: read
2. `w`: write
3 `x`: execute, or
- `-`: which means you *can't* perform an action

[^3]: There are also the `setgid`/`setuid`/`sticky` actions; however, I'm not going to go into those for now.

So in our example above, we have:
- `user` (owner) may `r`ead and e`x`ecute the file, but *not* `w`rite
- `group` cannot `r`ead/`w`rite/e`x`ecute the file
- `others` may `r`ead, `w`rite, *and* e`x`ecute the file.

## Converting permissions to numbers
Looking at our example again:
```sh
r-x---rwx
```

We know that each slot has only two possibilities: either a letter (`r`/`w`/`x`, depending on which slot it is) or a hyphen (`-`).

Instead of using letters and hyphens then, we can use *binary* to show whether an action is allowed for a particular role. For each disallowed action (indicated by the `-` character), we can set it as a `0` in binary. Otherwise, we can set a `1`.

Let's convert our example permissions:
```sh
r-x---rwx
101000111
```

"Great," you say. "[101000111 converted to decimal is 327](https://www.mathwarehouse.com/solved-problems/conversions/convert-101000111-from-binary-to-decimal), so that's our magic number, right? We're done!"

*Not quite.*

We want to be able to understand the permissions each role has a little better. `327` tells us what all the permissions are, but it's not very useful for seeing at a glance what each role's permissions are.

Let's try converting the binary for each role instead:
```sh
r-x --- rwx
101 000 111
  5   0   7
```

Now, by converting the number role by role, we know each digit will tell us what permissions each role has, with no guessing. If a role has all permissions, it will always have the digit `7`, for example.

Coincidentally, converting the number role by role is actually the same as converting it into octal, since there are 3 digits in a role (= 8 possibilities, from 0 to 7). If we run 101000111 through a binary → octal conversion, we can see that [it is in fact, 507](https://www.mathwarehouse.com/solved-problems/conversions/convert-101000111-from-binary-to-octal).

# Coding it up
We now know how to properly convert from octal permissions to "symbolic" permissions (the letters) and vice versa.

From octal to symbols:
1. Convert the octal number to a binary string
2. Parse the binary string, putting the correct "action" character (i.e. `r`/`w`/`x`) if the current character is a `1`, or a `-` if it's a `0`

To put this into code, we can take advantage of Go's packages, like [`strconv`](https://golang.org/pkg/strconv/). `strconv` offers a couple very useful functions that we can leverage:
* `ParseInt`
* `FormatInt`

So to convert from octals to permissions, we can use [`ParseInt`](https://golang.org/pkg/strconv/#ParseInt) to interpret a command-line arg as an octal number: 
```go
octal_num, err := strconv.ParseInt(os.Args[1], 8, 0)
```

Then, we can use [`FormatInt`](https://golang.org/pkg/strconv/#FormatInt) to format that number as a binary string[^4]:
```go
binary_str := strconv.FormatInt(octal_num, 2)
```

[^4]: For extra safety, you may need to pad the beginning of the string with 0s. I did so like this:
    ```go
    if len(binary_str) < 9 {
        padding := 9 - len(binary_str)
        for i := 0; i < padding; i++ {
            binary_str = "0" + binary_str
        }
    }
    ```

Finally, we can iterate through the binary string and construct our symbolic permissions, using a `for` loop and a `switch` statement:
```go
var perms string
for i := 0; i < 9; i++ {
    switch {
    case binary_str[i] == '0':
        perms += "-"
    case i%3 == 0:
        perms += "r"
    case i%3 == 1:
        perms += "w"
    case i%3 == 2:
        perms += "x"
    default:
        return "", errors.New("unable to parse octal")
    }
}
```

And that's it! When we print out `perms`, we will have the symbolic permissions that the "magic" octal number represents.

# Moving Forward
So far, I've [coded up](https://github.com/thejoycekung/tinkering/tree/master/modch) taking the octal permissions and converting it to symbols, as well as taking the symbols and converting them to octals.

This was a great way to get me familiar with manipulating strings and numbers of different bases in Go, and there's probably (? definitely?) more Go-ish ways to do it[^5].

[^5]: Yes, I know I should read [Effective Go](https://golang.org/doc/effective_go.html), but it's very long and I feel like I should always be taking notes whenever I try reading it...

In the future, I want to take some time to:
- Understand how the `setgid`/`setuid`/`sticky` bits work! 
  - I kind of know how the `sticky` bit works (only certain people can delete `sticky` files), but not the other two
- Understand how Go tests work. 
  - There's a finite number of permutations for the permissions, which means theoretically I can write tests for all of them.
  - I had some experience with `go test` when practicing on [Exercism](https://exercism.io/), but I haven't had too much time to go back to that so far[^6].

But for now I'm just happy I have a proper way of translating `chmod` numbers into readable permissions.

[^6]: That being said, the Exercism Go track is one of the best-mentored tracks on the site, and I would highly encourage you to work through it.
