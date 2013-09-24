# CSS Guidelines

Based heavily on Harry Roberts' [CSS Guidelines](https://github.com/csswizardry/CSS-Guidelines) and some of Nicolas Gallagher's [Idiomatic CSS](https://github.com/necolas/idiomatic-css).

---

In working on large, long running projects with dozens of developers, it is important that we all work in a unified way in order to, among other things:

* Keep stylesheets maintainable
* Keep code transparent and readable
* Keep stylesheets scalable

There are a variety of techniques we must employ in order to satisfy these goals.

The first part of this document will deal with methodology, project structure and mindframe when writing and architecting CSS, the second part will deal with documentation, syntax, formatting and CSS anatomy.

## TOC

* [Methodology](#methodology)
  * [SMACSS/OOCSS/BEM](#smacssoocssbem)
  * [Normalising](#normalising)
  * [Naming Conventions](#naming-conventions)
    * [BEM](#bem)
    * [JavaScript Hooks](#javascript-hooks)
  * [Structure](#structure)
    * [Settings](#settings)
    * [Includes](#includes)
    * [Elements](#elements)
    * [Global Components](#global-components)
    * [Components](#components)
  * [Creating New Components](#creating-new-components)
* [Documentation/UI Style Guide](#documentationui-style-guide)
  * [Comment Types](#comment-types)
  * [Headings](#headings)
  * [Description](#description)
  * [Examples](#examples)
* [Sass/CSS Style Guide](#sasscss-style-guide)
  * [General Principles](#general-principles)
  * [Whitespace](#whitespace)
  * [Format](#format)
    * [Declaration order](#declaration-order)
    * [Exceptions and slight deviations](#exceptions-and-slight-deviations)
    * [Sass: additional format considerations](#sass-additional-format-considerations)
  * [Layout](#layout)
  * [Sizing](#sizing)
  * [Media Queries](#media-queries)
  * [Feature Detection](#feature-detection)
  * [Shorthand](#shorthand)
  * [IDs](#ids)
  * [Selectors](#selectors)
    * [Over Qualified Selectors](#over-qualified-selectors)
    * [Selector performance](#selector-performance)
  * [CSS Selector Intent](#css-selector-intent)
  * [!important](#important)
  * [Magic Numbers and Absolutes](#magic-numbers-and-absolutes)
  * [Conditional stylesheets](#conditional-stylesheets)
  * [Debugging](#debugging)
  * [Sass](#sass)
* [Development Environment](#development-environment)
  * [Grunt](#grunt)


## Methodology

### SMACSS/OOCSS/BEM

All these methodologies/style guides are all trying to achieve a similar goal; bring abstractions to CSS to allow us build more scalable and maintainable projects. Because they all have great ideas and pros and cons to each, I don't stick rigidly to one idea and take a little from each of them.

A common trait throughout these methodologies is to break down CSS projects into modules/components. I have taken a similar approach and have the following structure:

* **Settings** (Sass variables, nothing included in CSS output)
* **Includes** (Sass plug-ins, functions, mixins, etc. Again, nothing included in CSS output)
* **Elements** (Anything that is a standard HTML tag, no classes!)
* **Global Components** (Components that act more like helpers or maybe be used together to allow greater flexibility, e.g. A grid system or layout helpers)
* **Components** (Self-contained design patterns, e.g. Button or Menu)

### Normalising

Rather than a complete CSS reset (which can have adverse effects) we can normalise our CSS in the *Elements* section. To ensure all elements are accounted for we pull in the styles from [Normalize.css](http://necolas.github.io/normalize.css/), break them into partials and add the rest of our element customisations. This saves overriding reset styles and when combined with Sass variables get get a new site up and going quickly.

### Naming conventions

After much research and testing of naming schemes, I have found the most flexible, readable and organised to be [BEM.](http://bem.info/).

#### BEM

BEM stands for Block-Element-Modifier. Even though BEM is more that just a naming convention, this is the biggest takeaway I have gotten from their methodology.

Say you need to create an alert component with a title and body. The component(block) would be the alert itself:

```css
.alert {
  background-colour: #ddd;
}
```

We then want to define our title and body (elements), we use `__`:

```css
.alert__title {
  font-size: 1.2em;
}

.alert__title,
.alert__content {
  padding: .25em .5em;
}
```

Now that we have that, maybe we want another type of alert for errors (modifier), we use `--`:

```css
.alert--danger {
  background-color: #cc0000;
}
```

If you need to change an element of a modifier, rather than making a longer class names (e.g. `.alert--danger__title`), nest the rules using Sass.

```scss
.alert--danger {
  .alert__title {
    background-color: #eee;
  }
}
```

To find out more about this (and possibly a slightly different approach than mine, see [MindBEMding – getting your head ’round BEM syntax](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)).

NOTE: Only use a single `-` for breaking up a class name, don't use camel case or underscores for this.

#### JavaScript hooks

**Never use a CSS _styling_ class as a JavaScript hook.** Attaching JS behaviour to a styling class means that we can never have one without the other.

If you need to bind to some markup use a JS specific CSS class. This is simply a class namespaced with `.js-`, e.g. `.js-toggle`, `.js-drag-and-drop`. This means that we can attach both JS and CSS to classes in our markup but there will never be any troublesome overlap.

```html
<th class="is-sortable  js-is-sortable">
</th>
```

The above markup holds two classes; one to which you can attach some styling for sortable table columns and another which allows you to add the sorting functionality.

### Structure (Sass)

Since moving to Sass I have started sharding my stylesheets out into lots of tiny includes. I have one stylesheet(style.scss) that is only Sass includes, which is what gets compiled into one CSS file with Sass. By taking this approach this also creates our Table of Contents for us.

The structure of imports in style.scss should be as follows:

* [Settings (Global)](#settings-global)
* [Includes](#includes)
* [Elements](#elements)
* [Global Components](#global-components)
* [Components](#components)

#### Settings (Global)

This is where any global variables get defined. I usually break this up into a least 2 sections:

##### Theme variables

Theme variables are used to keep consistency amongst the projects 'atmosphere'. This usually includes fonts, colors and default CSS3 properties. Variable groups should keep a common prefix, as to be more predictable in their use. e.g. all color variables should be prefixed with `$color-`.

##### Vendor Variables

When using plug-ins such as Compass and Breakpoint they provide default variables which are usually best to set. Unfortunately a lot of these have their own naming conventions so stick to these when needed. e.g. Compass includes the variables `$base-font-size` and `$base-line-height` when working with vertical rhythm.

#### Includes

Anything in the include section should have no actual CSS output. This is where vendor plug-ins can be imported and custom Sass mixins and functions are included.

#### Elements

Anything that is a HTML tag should be styled here, no classes should be included. Utilise variables as much as possible in this section as changing a few variables can greatly change the atmosphere of the design, making it easier to reuse as much code as possible.

#### Global Components

These are components that are more like helpers and tend to be reused a lot. Some examples are:

* Grid systems
* Text modifiers
* Layout modifiers

As these are reused a lot and to keep them distinct from normal components, I like to keep the prefix for each as short as possible. A grid system for example:

```css
.g {

}

.g__item {

}

.g--tight {

}
```

To ensure easy find ability, the partial file for this component would be named _g-grid.scss. Also, ensure any objects or abstractions are very vaguely named (e.g. `.list--slat`, `.media`) to allow for greater reuse.

#### Components

Components are where most customisations would be done. Examples of components could be:

* Buttons
* Alerts
* Tabs
* Heroes

## Documentation

A big part of keeping styles maintainable is documentation. This doesn't just mean basic code comments but documenting what each component does and how to use it. By doing this it enables other developer to find components they need without having to create new styles for every new thing.

This is all done by pulling out certain types of comments and compiling a live style guide.

### Comment Types

There 2 different types of comments we should use. 

For anything that belongs in the style guide use simple CSS block comment with a line break at the beginning and end. With these comments you can use markdown to create structure.

```css
/*

# Style Guide

Style guide comment

*/
```
For comments specifically about the code, use silent Sass comments. These will be striped out of the style guide and will only be visible to those viewing the Sass code base.

```scss
// Code comment
```

### Headings

As markdown headings within the style guide comments will be used to create the style guide menu, take note to use the right heading levels.

Only one heading 1 should be for the title of the style guide and heading 2's used for the section headings (e.g. Global Components). Each partial/component should therefore start with a heading 3 and any component modifiers use a heading 4.

### Description

When describing a component try and explain the context of how the component should be used and anything else that may help developers implementing the module.

### Examples

Each component should have an example of how to use it in the style guide. This can be done using markdown code fences.

    /*
    ```example

    <a class="button">Button</a>

    ```
    */

Use the example keyword to show a rendered example as well as the code that created it.

## CSS/Sass Style Guide

No matter the document, we must always try and keep a common formatting. This means consistent commenting, consistent syntax and consistent naming.

All code in any code-base should look like a single person typed it, even when many people are contributing to it.

### General Principles

Limit your stylesheets to a maximum 80 character width where possible. Exceptions may be gradient syntax and URLs in comments. That’s fine, there’s nothing we can do about that.

If in doubt when deciding upon a style use existing, common patterns.

### Whitespace

Only one style should exist across the entire source of your code-base. Always be consistent in your use of whitespace. Use whitespace to improve readability.

* Never mix spaces and tabs for indentation.
* I prefer two (2) space indents over tabs and write multi-line CSS. Keeping all properties **and** selectors on separate lines.

Tip: configure your editor to "show invisibles" or to automatically remove end-of-line whitespace.

### Format

The chosen code format must ensure that code is: easy to read; easy to clearly comment; minimizes the chance of accidentally introducing errors; and results in useful diffs and blames.

* Use one discrete selector per line in multi-selector rulesets.
* Include a single space before the opening brace of a ruleset.
* Include one declaration per line in a declaration block.
* Use one level of indentation for each declaration.
* Include a single space after the colon of a declaration.
* Use lowercase and shorthand hex values, e.g., `#aaa`.
* Use double quotes consistently, e.g., `content: ""`.
* Quote attribute values in selectors, e.g., `input[type="checkbox"]`.
* _Where allowed_, avoid specifying units for zero-values, e.g., `margin: 0`.
* Include a space after each comma in comma-separated property or function values.
* Include a semi-colon at the end of the last declaration in a declaration block.
* Place the closing brace of a ruleset in the same column as the first character of the ruleset.
* Separate each ruleset by a blank line.

```css
.selector-1,
.selector-2,
.selector-3[type="text"] {
  box-sizing: border-box;
  display: block;
  font-family: helvetica, arial, sans-serif;
  color: #333;
  background: #fff;
  background: linear-gradient(#fff, rgba(0, 0, 0, .8));
}

.selector-a,
.selector-b {
  padding: 1em;
}
```

#### Declaration order

If declarations are to be consistently ordered, it should be in accordance with a single, simple principle.

Smaller teams may prefer to cluster related properties (e.g. positioning and box-model) together.

```scss
.selector {
  // Positioning
  position: absolute;
  z-index: 10;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;

  // Display & Box Model
  display: inline-block;
  overflow: hidden;
  box-sizing: border-box;
  width: 10em;
  height: 10em;
  padding: 1em;
  border: 5px solid #333;
  margin: 1em;

  // Other
  background: #000;
  color: #fff;
  font-family: sans-serif;
  font-size: 1em;
  text-align: right;
}
```

NOTE: A tool such as CSS Comb can help with this. There is a Sublime Plug-in that makes it even easier.

#### Exceptions and slight deviations

Long, comma-separated property values - such as collections of gradients or shadows - can be arranged across multiple lines in an effort to improve readability and produce more useful diffs. There are various formats that could be used; one example is shown below.

```css
.selector {
  background-image:
    linear-gradient(#fff, #ccc),
    linear-gradient(#f3c, #4ec);
  box-shadow:
    1px 1px 1px #000,
    2px 2px 1px 1px #ccc inset;
}
```

#### Sass: additional format considerations

Different CSS preprocessors have different features, functionality, and syntax. Your conventions should be extended to accommodate the particularities of any preprocessor in use. The following guidelines are in reference to Sass.

* Limit nesting to 1 level deep. Reassess any nesting more than 2 levels deep. This prevents overly-specific CSS selectors.
* Always place `@extend` statements on the first lines of a declaration block.
* Where possible, group `@include` statements at the top of a declaration block, after any `@extend` statements.
* Prefix custom functions with `x-` or another namespace. This helps to avoid any potential to confuse your function with a native CSS function, or to clash with functions from libraries.

```scss
.selector-1 {
  @extend .other-rule;
  @include clearfix();
  @include box-sizing(border-box);
  width: x-grid-unit(1);
  // other declarations
}
```

### Layout

All components you build should be left totally free of widths; they should always remain fluid and their widths should be governed by a parent/grid system.

Heights should almost never be be applied to elements. Heights should only be applied to things which had dimensions *before* they entered the site (i.e. images and sprites). Never ever set heights on `p`s, `ul`s, `div`s, anything. You can often achieve the desired effect with `line-height` which is far more flexible.

Grid systems should be thought of as shelves. They contain content but are not content in themselves. You put up your shelves then fill them with your stuff. By setting up our grids separately to our components you can move components around a lot more easily than if they had dimensions applied to them; this makes our front-ends a lot more adaptable and quick to work with.

You should never apply any styles to a grid item, they are for layout purposes only. Apply styling to content _inside_ a grid item. Never, under *any* circumstances, apply box-model properties to a grid item.

### Sizing

I use a combination of methods for sizing UIs. Percentages, pixels, ems and nothing at all.

Grid systems should, ideally, be set in percentages. Because I use grid systems to govern widths of columns and pages, I can leave components totally free of any dimensions (as discussed above). With `box-sizing: border-box` though we are able to set the padding in ems and have it subtract from the percentages and not overflow.

Font sizes I set in ems using Compass' vertical rhythm module. As long as you only apply fonts to specific elements that need them and not to containers, you should not not have any adverse effects. It saves having to have fallback pixels in < IE8 and Opera Mini and allows us to proportionally resize components if necessary by changing the font size of the parent.

Once I set a baseline with Compass using $base-font-size and $base-line-height I can then use Compass' `rhythm()` function for all all em based sizing. For example:

```scss
.alert {
  margin-bottom: rhythm(1);
  padding: rhythm(.5);
}
```

By doing this I am setting all measurement relative to the baseline height. So `rhythm(1)` is equal to 1 line-height. This keeps us thinking relatively but keeps consistency amongst our components.

By sizing basically everything with percentages and ems we are able to resize our entire UI at different screen sizes just by changing the body font size.

I only use pixels for items whose dimensions were defined before the came into the site. This includes things like images and sprites whose dimensions are inherently set absolutely in pixels. Also borders and shadows (which values regularly are as small as 1px wide), are set in pixels as 1px in ems can sometimes be reduced to nothing.

### Media Queries

This is almost entirely handled with Sass and a Compass plug-in called Breakpoint. I set various breakpoint variables in the settings partial and reuse them throughout the project.

When add breakpoints to a component, start with the smaller screen styles and always nest the breakpoints at the end of the current selector.

```scss
.hero {
  padding: rhythm(.5);
  @include breakpoint($gte-large) {
    padding: rhythm(1);
  }
}

.hero__title {
  @include adjust-font-size-to(20px);
  @include breakpoint($gte-large) {
    @include adjust-font-size-to(24px);
  }
}
```

### Feature Detection

By using modernizr we can enhance the users experience depending on their device features. Similar to media queries, always nest these at the end of a selector.

```scss
.header__logo {
  background-image: url("../img/logo.png");
  .svg & {
    background-image: url("../img/logo.svg");
  }
}
```

Here we use Sass' parent selector, as `.svg` is added to the `html` element by modernizr. This would output:

```css
.header__logo {
  background-image: url("../img/logo.png");
}
.svg .header__logo {
  background-image: url("../img/logo.svg");
}
```

### Shorthand

It might be tempting to use declarations like `background: red;` but in doing so what you are actually saying is ‘I want no image to scroll, aligned top-left, repeating X and Y, and a background colour of red’. Nine times out of ten this won’t cause any issues but that one time it does is annoying enough to warrant not using such shorthand. Instead use `background-color: red;`.

Similarly, declarations like `margin: 0;` are nice and short, but
**be explicit**. If you actually only really want to affect the margin on  the bottom of an element then it is more appropriate to use `margin-bottom: 0;`.

Be explicit in which properties you set and take care to not inadvertently unset others with shorthand. E.g. if you only want to remove the bottom margin on an element then there is no sense in setting all margins to zero with `margin:0;`.

Shorthand is good, but easily misused.

### IDs

A quick note on IDs in CSS before we dive into selectors in general.

**NEVER use IDs in CSS.**

They can be used in your markup for JS and fragment identifiers but use only classes for styling. You don’t want to see a single ID in any stylesheets!

Classes come with the benefit of being reusable (even if we don’t want to, we can) and they have a nice, low specificity. Specificity is one of the quickest ways to run into difficulties in projects and keeping it low at all times is imperative. An ID is **255** times more specific than a class, so never ever use them in CSS _ever_.

### Selectors

Keep selectors short, efficient and portable.

Heavily location-based selectors are bad for a number of reasons. For example, take `.sidebar h3 span{}`. This selector is too location-based and thus we cannot move that `span` outside of a `h3` outside of `.sidebar` and maintain styling.

Selectors which are too long also introduce performance issues; the more checks in a selector (e.g. `.sidebar h3 span` has three checks, `.content ul p a` has four), the more work the browser has to do.

Make sure styles aren’t dependent on location where possible, and make sure selectors are nice and short.

Selectors as a whole should be kept short (e.g. one class deep) but the class names themselves should be as long as they need to be. A class of `.user-avatar` is far nicer than `.usr-avt`.

**Remember:** classes are neither semantic or insemantic; they are sensible or insensible! Stop stressing about ‘semantic’ class names and pick something sensible and futureproof.

#### Over qualified selectors

As discussed above, qualified selectors are bad news.

An over-qualified selector is one like `div.promo`. You could probably get the same effect from just using `.promo`. Of course sometimes you will _want_ to qualify a class with an element (e.g. if you have a generic `.error` class that needs to look different when applied to different elements (e.g. `.error{ color:red; }` `div.error{ padding:14px; }`)), but generally avoid it where possible.

Another example of an over-qualified selector might be `ul.nav li a{}`. As above, we can instantly drop the `ul` and because we know `.nav` is a list, we therefore know that any `a` _must_ be in an `li`, so we can get `ul.nav li a{}` down to just `.nav a{}`.

#### Selector performance

Whilst it is true that browsers will only ever keep getting faster at rendering CSS, efficiency is something you could do to keep an eye on. Short, unnested selectors, not using the universal (`*{}`) selector as the key selector, and avoiding more complex CSS3 selectors should help circumvent these problems.

### CSS selector intent

Instead of using selectors to drill down the DOM to an element, it is often best to put a class on the element you explicitly want to style. Let’s take a specific example with a selector like `.header ul{}`…

Let’s imagine that `ul` is indeed the main navigation for our website. It lives in the header as you might expect and is currently the only `ul` in there; `.header ul{}` will work, but it’s not ideal or advisable. It’s not very future proof and certainly not explicit enough. As soon as we add another `ul` to that header it will adopt the styling of our main nav and the the chances are it won’t want to. This means we either have to refactor a lot of code _or_ undo a
lot of styling on subsequent `ul`s in that `.header` to remove the effects of the far reaching selector.

Your selector’s intent must match that of your reason for styling something; ask yourself **‘am I selecting this because it’s a `ul` inside of `.header` or because it is my site’s main nav?’**. The answer to this will determine your selector.

Make sure your key selector is never an element/type selector or
object/abstraction class. You never really want to see selectors like
`.sidebar ul{}` or `.footer .media{}` in our theme stylesheets.

Be explicit; target the element you want to affect, not its parent. Never assume that markup won’t change. **Write selectors that target what you want, not what happens to be there already.**

For a full write up please see this article
[Shoot to kill; CSS selector intent](http://csswizardry.com/2012/07/shoot-to-kill-css-selector-intent/)

### !important

It is okay to use `!important` on Global Components (helper classes) only. To add `!important` preemptively is fine, e.g. `.error{ color: red !important }`, as you know you will **always** want this rule to take precedence.

Using `!important` reactively, e.g. to get yourself out of nasty specificity situations, is not advised. Rework your CSS and try to combat these issues by refactoring your selectors. Keeping your selectors short and avoiding IDs will help out here massively.

### Magic numbers and absolutes

A magic number is a number which is used because ‘it just works’. These are bad because they rarely work for any real reason and are not usually very futureproof or flexible/forgiving. They tend to fix symptoms and not problems.

For example, using `.dropdown-nav li:hover ul{ top:37px; }` to move a dropdown to the bottom of the nav on hover is bad, as 37px is a magic number. 37px only works here because in this particular scenario the `.dropdown-nav` happens to be 37px tall.

Instead you should use `.dropdown-nav li:hover ul{ top:100%; }` which means no matter how tall the `.dropdown-nav` gets, the dropdown will always sit 100% from the top.

Every time you hard code a number think twice; if you can avoid it by using keywords or ‘aliases’ (i.e. `top:100%` to mean ‘all the way from the top’) or _even better_ no measurements at all then you probably should.

Every hard-coded measurement you set is a commitment you might not necessarily want to keep.

## Conditional stylesheets

IE stylesheets can, by and large, be totally avoided. The only time an IE stylesheet may be required is to circumvent blatant lack of support (e.g. PNG fixes, Media queries).

As a general rule, all layout and box-model rules can and _will_ work without an IE stylesheet if you refactor and rework your CSS. This means you never want to see `<!--[if IE 7]> element{ margin-left:-9px; } < ![endif]-->` or other such CSS that is clearly using arbitrary styling to just ‘make stuff work’.

Currently I only use an IE stylesheet when <= IE8 support is needed, and this is done by giving IE8 and below it's own stylesheet striped of all media queries by using one variable at the start of style-old-ie.scss. This was newer browsers don't get any old IE cruft and we don't need to do any polyfills for mediaqueries in <= IE8.

### Debugging

If you run into a CSS problem **take code away before you start adding more** in a bid to fix it. The problem exists in CSS that is already written, more CSS isn’t the right answer!

Delete chunks of markup and CSS until your problem goes away, then you can determine which part of the code the problem lies in.

It can be tempting to put an `overflow:hidden;` on something to hide the effects of a layout quirk, but overflow was probably never the problem; **fix the problem, not its symptoms.**

### Sass

Sass is my preprocessor of choice. **Use it wisely.** Use Sass to make your CSS more powerful but avoid nesting like the plague! Nest only when it would actually be necessary in vanilla CSS, e.g.

```css
.header{}
.header .site-nav{}
.header .site-nav li{}
.header .site-nav li a{}
```

Would be wholly unnecessary in normal CSS, so the following would be **bad**
Sass:

```scss
.header{
    .site-nav{
        li{
            a{}
        }
    }
}
```

If you were to Sass this up you’d write it as:

```scss
.header{}
.site-nav{
    li{}
    a{}
}
```

## Development Environment

### Grunt

Instead of using a GUI tool like Codekit, Hammer or Mixture for preprocessing and builds, I use Grunt.

Grunt keeps all the configuration with the project and is cross platform. It makes it relatively easy for someone new to the team able to pull down the repo install the Grunt dependencies and start building the project in exactly the same way you have been already.

It can handle Preprocessing Sass and Compass, concatenating and minifying JavaScript, building icon fonts and basically anything else you want to throw at it.

Once installed, to see what tasks are available just run `grunt -h` on the command line.
