# What is this?

A DSL for Lua 5.2+ that lets you write Lua code that resembles HTML. When
you're done, you can render the tree it builds to actual HTML!

# Example

```lua
local html = require('html')

local document = html {
  html.head {
    html.style [[
      .red {
        background: #800;
      }
    ]]
  },
  html.body {
    html.p 'hello world',
    html.ul {
      html.li 'foo',
      html.li 'bar',
      html.li {class='red', 'baz'}
    }
  }
}

-- write html to stdout
print(document:render())
```

# How does this abomination work?

Lua supports an alternate function call syntax for unary functions that take
either string literals or table constructors:

```lua
-- these are equivalent:
print 'hello world'
print('hello world')

-- these are also equivalent:
f {a=1, b=3, c=5}
f({a=1, b=3, c=5})
```

(Ab)using this this feature, we can bend Lua to do some crazy things.

# Reference
## `html` module
### `html.<element>(data)`

Substitute `<element>` for any valid [HTML5
element](http://www.w3.org/TR/html5/). When `data` is a table, its dictionary
elements are used as attributes and its sequence elements are used as HTML
elements and text. When `data` is a string, it is used as the only child of the
resulting element. `data` can also be omitted to obtain an element with no
children or attributes. The return value is an `element`.

#### Example

```lua
(html.p()):render()
-- <p></p>

(html.p 'hello world'):render()
-- <p>hello world</p>

(html.ul {class='list', html.li 'a', html.li 'b'}):render()
-- <ul class="list"><li>a</li><li>b</li></ul>
```

### `html.rendermixed(t)`

Accepts a sequence table of either strings or HTML elements, and passes the
strings through untouched while rendering the HTML elements to strings. The
result is a concatenated string with the strings and rendered elements
combined.

#### Example

```lua
html.rendermixed {'hello ', html.span 'world'}
-- hello <span>world</span>
```

### `html(t)`

The HTML module is callable. It is equivalent to calling `html.html` to create
an `<html>` element.

#### Example

```lua
(html {html.body {html.p 'hello world'}}):render()
-- <html><body><p>hello world</p></body></html>
```

## `element`s
### `element:render()`

Converts the element to an HTML string.

#### Example

```lua
(html.p 'hello world'):render()
-- <p>hello world</p>
```

### `element:appendchild(data)`

Appends `data` to the element. `data` should be either an `element` or a
`string`.

#### Example

```lua
local para = html.p()
para:appendchild(html.span 'hello')
para:appendchild(' world')
para:render()
-- <p><span>hello</span> world</p>
```

### `element:addattr(name, value)`

Adds an attribute to the element.

#### Example

```lua
local para = html.p 'hello world'
para:addattr('class', 'shiny')
para:render()
-- <p class="shiny">hello world</p>
```

### `element:opentag()`

Renders the opening tag for this element, which includes attributes.

#### Example

```lua
local para = html.p {class='shiny', 'hello world'}
para:opentag()
-- <p class="shiny">
```

### `element:closetag()`

Renders the closing tag for this element, **regardless of whether or not it is
a void element**.

#### Example

```lua
local para = html.p {class='shiny', 'hello world'}
para:closetag()
-- </p>
```

### `html.element.type(t)`

Returns the string `'element'` if `t` is a `table` whose metatable is
`html.element`. Otherwise, returns `nil`.

# License
Licensed under the Apache License 2.0. Refer to the `LICENSE` file for more
information.
