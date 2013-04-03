title: Agenda
class: big
build_lists: true

Topics for today:

- Functions and scopes
- Type coercion
- Truthy and falsey values
- Prototypes

---

title: Functions and Scopes
class: title-slide

---

title: Function Syntax

Two ways to declare functions.

<pre class="prettyprint" data-lang="javascript" contenteditable>
function foo () {
  return 1;
}

var foo = function () {
  return 1;
}
</pre>

---

title: Anonymous Functions

<pre class="prettyprint" data-lang="javascript" contenteditable>
function bar () {
  return function () {
    return 1;
  }
}
</pre>

---

title: Centered content
content_class: flexbox vcenter

This content should be centered!
