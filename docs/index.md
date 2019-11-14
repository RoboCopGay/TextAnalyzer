# TextAnalizer

The TextAnalizer is a simple ultility for text analitics, your especiality is translate some text to other text.

## Install

Verify if you `$PATH` variable has the directory `$HOME/.local/bin`, why is the local where the `pip` will save the executable files, if not take a next command and reload your desktop session to load the setting.

> I'm not talking to you reboot your machine, just logout and login again.

```sh
$ echo 'export PATH="$PATH:$HOME/.local/bin"' >> .xprofile
```

And is just run the next command and done!

```sh
$ pip install git+http://github.com/RoboCopGay/TextAnalizer --user && getter -h > /dev/null && if [[ "$?"==0 ]];then echo 'TextAnalizer are installed!!';fi
```

# Getting started

Before of you to begin with `TextAnalizer`, make sure if you know [REGEX](https://en.wikipedia.org/wiki/Regular_expression).

Changing `"foo" to "bar"` on the text `"foo is bar"`:

> This is the basic syntax of one element: `'<pattern>: <replace>'`, but do'nt forget the space between `:` and `<replace>`

```sh
$ echo "foo bar"| translater 'foo: bar'

bar bar
```

Changing word before `bar` to `foo`:

```sh
$ echo "thingbar, somethingbar" | translater "'\\w{1,}bar': foobar"
```

> The `\\` is needed because of quote dont accept the `\`.

Changing `"foo<complement>"` to `"<complement>bar"`.

```sh
$ echo "foosomething"| translater '"foo%complement&": "%complement&bar"'

somethingbar
```

Variables like the `%complement&` search on text for the `\w{1,}` pattern, but if you want search for others patterns is just use a `:` to change it.

> In this example i will use: `%complement:[abc]{1,3}&` that will search from 1 to 3 characters that need be "a", "b" or "c".

```sh
$ echo "foosomething
fooabcd
foobcaz
"  | translater '"foo%complement:&": "%complement&bar"'

foosomething
abcbar
bcabar
```

> Look that `foosomething` did remain equal, because "foo" doesn't have any of the letters in the list.

With the `translater` you too can search in a file, is just use the `-f <file>`:

> file.txt

```
name Jack
old 23
```

```sh
$ translater -f file.txt '"%key& %value&": "the %key& has the %value& value."'

the name has the Jack value.
the old has the 23 value.
```

And you too can use a file as `pattern` using the `-c` opition:

> file.txt

```
hi, I am Brazilian.
```

> conf.yml

```yaml
hi: Oi
I: eu
am: sou
Brazilian: Brasileiro
```

```sh
$ translater -f file.txt -c conf.yml

Oi, eu sou Brasileiro.
```

## The configuration file

The configuration file is basicaly a [yaml](http://yaml.org), and can be used for `translater`, `getter` or some python code that use the TextAnalizer library.

```yaml
'my name is %name&': 'hello %name&!'
hi: hello
```

Before of more nothing, the double quot (`"`) on yaml can interpret symbols like `\n` or `\t`, because of this if you use him, take care with the regex, to ignore this problems use the single quot (`'`).

Exists basicaly 3 forms of to declare a element:

```yaml
# short form
<pattern>: <replace>

# long forms
<pattern>:
    name: <name>
    replace: <replace>
    end: <end>
    childs:
        - <child1>
        - <child2>
        - ...
        - <childN>

<name>:
    pattern: <pattern>
    end: <end>
    childs:
        - <child1>
        - <child2>
        - ...
        - <childN>
    local:
        <pattern1>: <replace1>
        <pattern2>: <replace2>
        ...
        <patternN>: <replaceN>
        keywords:
            <pattern1>: <replace1>
            <pattern2>: <replace2>
            ...
            <patternN>: <replaceN>
```

### pattern:

The key `pattern` is the query to search on texts, it have 4 forms of declaration:

```yaml
<pattern>: ...

<name>:
    pattern: <pattern>
    ...

<name>:
    ptn: <pattern>

<name>:
    p: <pattern>
```

In the `pattern` value you need put a simple text or a regular expression:

```yaml
foo: ...

'.{0,}@.{0,}': ...
```

The diferencial of TextAnalizer is the variable declarations:

```yaml
"%<variable>&": ...     # variables for default has "\w{1,}" as regex
"%<variable>:<regex>&": ... # variable with custom regex
```

### replace

The key `replace` has the power of replace what did found by `pattern`, it have 4 forms of declaration:

```yaml
<pattern>: <replace>

<pattern>:
    rpl: <replace>
    ...

<pattern>:
    r: <replace>
    ...
```

The `replace` can show the variables:

```yaml
"%<var>:<re>&": "%<var>&"
```

And it too can execute python commands expresseds on pattern:

```yaml
'%number_of_letter:\d{1,}': ''
```

### name:

The `name` key is to reference the element to other elements and it have 4 forms of declaration:

```yaml
<pattern>:
    name: <name>
    ...

<pattern>:
    nam: <name>
    ...

<pattern>:
    n: <name>
    ...

<name>:
    ...
```

### end:

The `end` key is used to delimit the action area:

```yaml
<pattern>:
    name: endPhrase
    replace: <replace>

<pattern>:
    name: phrase
    end: endPhrase
```

Or if you do'nt want create a secondary element:

```yaml
<pattern>:
    end:
        pattern: <pattern>
        replace: <replace>
```