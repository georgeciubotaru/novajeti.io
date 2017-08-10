# Novajeti.io / Sass Styleguide

*A mostly reasonable approach to CSS and Sass*

## Table of Contents

1. [Terminology](#terminology)
    - [Rule Declaration](#rule-declaration)
    - [Selectors](#selectors)
    - [Properties](#properties)
1. [CSS](#css)
    - [Formatting](#formatting)
    - [Comments](#comments)
    - [ID Selectors](#id-selectors)
    - [JavaScript hooks](#javascript-hooks)
    - [Border](#border)
1. [Sass](#sass)
    - [Syntax](#syntax)
    - [Ordering](#ordering-of-property-declarations)
    - [Variables](#variables)
        - [Length Vars](#length-vars)
    - [Mixins](#mixins)
    - [Transparent value](#transparent)
    - [Property order](#property-order)
    - [Extend directive](#extend-directive)
    - [Nested selectors](#nested-selectors)

## Terminology

### Rule declaration

A “rule declaration” is the name given to a selector (or a group of selectors) with an accompanying group of properties. Here's an example:

```css
.listing 
{
  font-size: 18px;
  line-height: 1.2;
}
```

### Selectors

In a rule declaration, “selectors” are the bits that determine which elements in the DOM tree will be styled by the defined properties. Selectors can match HTML elements, as well as an element's class, ID, or any of its attributes. Here are some examples of selectors:

```css
.my-element-class 
{
  /* ... */
}

[aria-hidden] 
{
  /* ... */
}
```

### Properties

Finally, properties are what give the selected elements of a rule declaration their style. Properties are key-value pairs, and a rule declaration can contain one or more property declarations. Property declarations look like this:

```css
/* some selector */ 
{
  background: #f1f1f1;
  color: #333;
}
```

**[⬆ back to top](#table-of-contents)**

## CSS

### Formatting

* Use soft tabs (4 spaces) for indentation
* Prefer kebab-case over camelCasing in class names.
* Do not use ID selectors
* When using multiple selectors in a rule declaration, give each selector its own line.
* Put a line break before the opening brace `{` in rule declarations
* In properties, put a space after, but not before, the `:` character.
* Put closing braces `}` of rule declarations on a new line
* Put blank lines between rule declarations

**Bad**

```css
.avatar{
    border-radius:50%;
    border:2px solid white; }
.no, .nope, .not_good {
    // ...
}
#lol-no {
  // ...
}
```

**Good**

```css
.avatar 
{
    border-radius: 50%;
    border: 2px solid white;
}

.one,
.selector,
.per-line 
{
    // ...
}
```

### Comments

* Prefer line comments (`//` in Sass-land) to block comments.
* Prefer comments on their own line. Avoid end-of-line comments.
* Write detailed comments for code that isn't self-documenting:
  - Uses of z-index
  - Compatibility or browser-specific hacks

### ID selectors

While it is possible to select elements by ID in CSS, it should generally be considered an anti-pattern. ID selectors introduce an unnecessarily high level of [specificity](https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity) to your rule declarations, and they are not reusable.

For more on this subject, read [CSS Wizardry's article](http://csswizardry.com/2014/07/hacks-for-dealing-with-specificity/) on dealing with specificity.

### JavaScript hooks

Avoid binding to the same class in both your CSS and JavaScript. Conflating the two often leads to, at a minimum, time wasted during refactoring when a developer must cross-reference each class they are changing, and at its worst, developers being afraid to make changes for fear of breaking functionality.

We recommend creating JavaScript-specific classes to bind to, prefixed with `.js-`:

```html
<button class="btn btn-primary js-request-to-book">Request to Book</button>
```

### Border

Use `0` instead of `none` to specify that a style has no border.

**Bad**

```css
.foo {
  border: none;
}
```

**Good**

```css
.foo 
{
    border: 0;
}
```
**[⬆ back to top](#table-of-contents)**

## Sass

### Syntax

* Use the `.scss` syntax, never the original `.sass` syntax
* Order your regular CSS and `@include` declarations logically (see below)

### Ordering of property declarations

1. Property declarations

    List all standard property declarations, anything that isn't an `@include` or a nested selector.

    ```scss
    .btn-green 
    {
       background: green;
       font-weight: bold;
       // ...
    }
    ```

2. `@include` declarations

    Grouping `@include`s at the end makes it easier to read the entire selector.

    ```scss
    .btn-green 
    {
       background: green;
       font-weight: bold;
       @include transition(background 0.5s ease);
       // ...
    }
    ```

3. Nested selectors

    Nested selectors, _if necessary_, go last, and nothing goes after them. Add whitespace between your rule declarations and nested selectors, as well as between adjacent nested selectors. Apply the same guidelines as above to your nested selectors.

    ```scss
    .btn 
    {
       background: green;
       font-weight: bold;
       @include transition(background 0.5s ease);

      .icon
      {
          margin-right: 10px;
      }
    }
    ```

### Variables

Prefer dash-cased variable names (e.g. `$my-variable`) over camelCased or snake_cased variable names. It is acceptable to prefix variable names that are intended to be used only within the same file with an underscore (e.g. `$_my-variable`).

### Length vars

**Disabled by default**

Prefer length literals (numbers with units) to be used only in
variable declarations. They should be referred to via variables,
or calculations using variables, everywhere else.

**Bad: literal length**
```scss
div {
  width: 100px;
}
```

**Good: refer to length by variable name**
```scss
$column-width: 100px;

...

div 
{
    width: $column-width;
}
```

Most lengths in stylesheets are related to and dependant on other lengths.
By only using variables you are forced to name these lengths and relate them
to each other. This linter does not catch uses of percentages as those size
relationships are already present. It encourages calculations:
e.g `-$input-height` or `$input-height - $input-line-weight * 2`.

Defining length directly in properties usually leads to future magic-number
detective work. For example, if you ever want to change the height of all form
inputs then you'll have to update the heights and widths and margins of many
things in a number of places, and finding all those places can be difficult if
you use the same length for other things, or you have values that are related
to other values (e.g. the height minus the border width). A simple find/
replace may not always work.

A better approach is to use global variables like `$standard-input-height` and
refer to this variable everywhere you want to use it. This makes it easy to
update the color, as you only need change it in one place. It is also more
intention-revealing, as seeing the name `$input-height` is more descriptive
than `40px` or `1.5em`.

**Limitations**
- This doesn't catch `width: $button-width + 2` where the `2` is implicitly
`2px`. SASS is too clever here.
- This doesn't flag length literals that are percentages: 1. because that
  already expresses how lengths are related to each other, and 2. because
  percentages are also used for non-length values. (e.g. `fade-out(black, 70%)`)
### Mixins

Mixins should be used to DRY up your code, add clarity, or abstract complexity--in much the same way as well-named functions. Mixins that accept no arguments can be useful for this, but note that if you are not compressing your payload (e.g. gzip), this may contribute to unnecessary code duplication in the resulting styles.

### Transparent
Use transparent `@function` instead transparent value to work around IE bug

### Property order
Please read [Style guide](https://github.com/georgeciubotaru/novajeti.io/blob/master/scss/skeleton/_style-guide.scss) 

### Extend directive

`@extend` should be avoided because it has unintuitive and potentially dangerous behavior, especially when used with nested selectors. Even extending top-level placeholder selectors can cause problems if the order of selectors ends up changing later (e.g. if they are in other files and the order the files are loaded shifts). Gzipping should handle most of the savings you would have gained by using `@extend`, and you can DRY up your stylesheets nicely with mixins.

### Nested selectors

**Do not nest selectors more than three levels deep!**

```scss
.page-container {
  .content {
    .profile {
      // STOP!
    }
  }
}
```

When selectors become this long, you're likely writing CSS that is:

* Strongly coupled to the HTML (fragile) *—OR—*
* Overly specific (powerful) *—OR—*
* Not reusable


Again: **never nest ID selectors!**

If you must use an ID selector in the first place (and you should really try not to), they should never be nested. If you find yourself doing this, you need to revisit your markup, or figure out why such strong specificity is needed. If you are writing well formed HTML and CSS, you should **never** need to do this.

**[⬆ back to top](#table-of-contents)**