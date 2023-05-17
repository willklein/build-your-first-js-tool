---
# try also 'default' to start simple
theme: default

# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: wood-tools.jpg
# apply any windi css classes to the current slide
# class: "text-center"
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# page transition
# transition: slide-left
# use UnoCSS
css: unocss
---

# Start Building Your Own JavaScript Tools

## Will Klein



<!--
Today, I want you to start building our own javascript tools.

my name is will klein, and I'll show you how.
-->

---
layout: image
image: bulb.jpg
---

# What if...

<!--
what if we could get started with building our own tools? where would we begin?
what if the tools were specific to our project, uniquely helpful and tailored to our needs?
what if we could learn how to do this in the next 20 minutes, learning what we need to know and how to take our next step?
-->

---
layout: image
image: bulb.jpg
---

# What if...
# in 20 minutes

<!--
in the next 20 minutes... (list)
-->

---
layout: image
image: bulb.jpg
class: darken
---

# What if...
# in 20 minutes

- write a linting rule

<!--

let's write our first linting rule
-->


---
layout: image
image: bulb.jpg
class: darken
---

# What if...
# in 20 minutes

- write a linting rule
- write a code mod

<!--
write our first code mod
-->


---
layout: image
image: bulb.jpg
class: darken
---

# What if...
# in 20 minutes

- write a linting rule
- write a code mod
- explore at an AST

<!--
look at our first AST and build our understanding of them
-->


---
layout: image
image: bulb.jpg
class: darken
---

# What if...
# in 20 minutes

- write a linting rule
- write a code mod
- explore at an AST
- build an understanding of our tools

<!--
learn the fundamentals of how our tools work
-->

---
layout: image
image: puzzle.jpg
class: darken
---

# A problem that repeats itself

- common mistakes
- communicating best practices

<!--
let's dive in. i see a common thread across any project i work on. different developers are making the same mistake, and we have preferred ways of doing things, while it's hard to communicate that to everyone else on our project, or using our library. preventing mistakes _and_ sharing best practices are great reasons to look to tools like linters, and in particular, ESLint

not only can we use ESLint's existing rules to handle many problems, but it has a rich ecosystem of plugins specific to different areas. And... we can write our own rules and plugins, which I'll show you how.
-->

---
layout: image
image: puzzle.jpg
class: darken
---

## ESLint rules are great for...

- problems or mistakes that keep coming up
- creating a better feedback loop
- automating code review feedback
- sharing best practices / preventing anti-patterns

<!--
ESLint rules are great for...

- problems or mistakes that keep coming up
- creating a better feedback loop
- automating code review feedback
- sharing best practices / preventing anti-patterns
-->
---
layout: image
image: puzzle.jpg
class: darken
---

## ESLint rules can be...
- for any project - your app, your library, or a library someone else wrote
- written by anyone!

<!--
Rules can be for any project, maybe just for your app, helping developers on your immediate team or others in a shared codebase

If you maintain a library, they can help anyone using your library

And anyone can write them, not just project maintainers or expert developers

-->

---
layout: image
image: puzzle.jpg
class: darken
---

# let's write our first rule together

<!--
alright, let's write our first rule together.

I'm going to share a little about what I work on and problem I'd like to solve where I think an ESLint rule would be really helpful
-->

---
layout: image
image: canvas-homepage.png
---


<!--
Right now I work on a design system called Canvas and we provide a component library for UI developers to use. We invest a lot into making our components accessible and useful across a wide range of uses cases.
-->

---
layout: image
image: canvas-labs.png

---


<!-- As part of that effort, we maintain three different packages for our components: Main, Preview, and Labs. We usually build new components in Labs, and as we prove out the API, refine design, and make sure accessibility is perfected, we'll promote something in Labs to Preview, and from Preview to Main.
-->

---
layout: image
image: path.jpg
---


<!-- this presents a somewhat unique challenge though: we have some old components, usually in Main, that are going to be replaced by newer ones in Labs or Preview. When a developer is choosing between the two versions, which way do they go?

Sometimes, we really want to tell developers to use the one in Labs, we already plan to promote it to Main in an upcoming release. the component is ready to use and has some benefits we don't want our users to miss out on.

but how do we do we communicate this? We do what we can with our docs, and sending out announcements, but the best way is if we can tell them as they write their code.
-->

---

# astexplorer.net

<!--
I'm pulling up an online tool that I use to test out new rules. Usually I'll develop with a test suite, writing lint rules is a good use case for test-driven development. But this tool is great to explore the problem, get a first draft, and often, that's enough.

This is ASTExplorer.net. There are a few things going on here but I'll explain how each part works. On the left it has two editors, a place for the code we're working with on the top left, and an editor for writing ESLint rules and code mods on the bottom left. On the right, we have an AST viewer above, and an output window below.

In the header, we can also configure the parser and the Transform type, which is the tool we want to experiment with. For our first example we'll use the the Babel parser and eslint v8 for the transform.

besides giving us a really useful development environment, this ast viewer in the top right is amazing
-->

---

# astexplorer.net

<!--
AST is an acronym for abstract syntax tree. If you didn't study computer science at a university, the terminology for an AST might seem intimidating. I promise you though, it's just a tree, and if you've worked with the DOM for a web page, you know how a tree works. An AST just has the syntax for our code stored in it.

We can explore this tree in AST Explorer. It's also worth mentioning that the tree is basically a JavaScript object, there's really nothing new here.
-->

---

# astexplorer.net

<!--
Like the `<html>` tag in a web page is at the top of the DOM tree, the top of an AST is the `Program`. It has child nodes, and those child nodes have children, and so on.

There are a properties on each node that tell us their type. This is going to be really important for looking up nodes we want to check for patterns. We will be telling ESLint the node type we care about, and it will pass all nodes of that type into a function we define.

There are also some properties to describe each node, depending on what type of node it is.

Looking at this gives us everything we need to detect patterns, which is what an ESLint rule is doing.

Let's look at some sample code that includes imports for our components
-->

---

# astexplorer.net

<!--
Here's what I'm thinking, we have a segmented control component that exists in both main and our preview package.

if we look at whenever it's imported, and if it's imported from the main package, we tell the user about the preferred version in the Preview pacakge.

LIVE CODE

- the boilerplate example
- the create function
- context.report
- what node do we care about?
- isCanvasKitMainPackage = node.source.value.includes("@workday/canvas-kit-react");
- containsComponentImport = node.specifiers.find((specifier) => specifier.imported.name === "SegmentedControl");
-->

---
layout: image
image: package.jpg
---

<!--
From here, to package up the rule and get it ready for consumers,

 I'd write out a test suite, and add support for other duplicate components and recommend what is preferred for each. There also the option to implement a fixer in ESLint, which is essentially a code mod.


but there's another tool we'll look at that's dedicated to that purpose. Let's look at code mods next.
-->


---
layout: image
image: paper.jpg
---

# A codemod automates a code refactor or change in syntax

<!--
A codemod automates a code refactor or change in syntax
-->

---
layout: image
image: paper.jpg
class: darken
---

# Codemods can be written by...

- maintainers of a library
- users of a library
- any developer that has a bunch of changes to apply across a codebase

<!--
they can be written by...

- maintainers of a library
- users of a library
- any developer that has a bunch of changes to apply across a codebase
-->
---
layout: image
image: paper.jpg
class: darken
---

# Codemods are great for...

- improving how quickly consumers can upgrade
- making hundreds or even thousands of small repetitive changes

<!--
codemods are great for

- improving how quickly consumers can upgrade - say to a new major version that has breaking changes - if they don't need to manually change their code, and can just run a script, that's pretty easy

they're also great for

- making hundreds or even thousands of small repetitive changes
	- this would be mundane work and you would hate your life in the process
	- I'd rather spend an hour writing code for a codemod than hand editing hundreds of files, which can take longer, and easily result in mistakes


in particular, if you're maintaining a library, it's something that could be really valuable to provide to users
-->
---
layout: image
image: paper.jpg
class: darken
---

# My first codemod experience

<!--
let me share with you my first experience with a codemod

Back in 2018 I worked on a product team and we were changing our library for CSS in JS and styled components. We were moving from Glamorous to Emotion, and while considering our options, and the effort required, we found a code mod from another speaker here, Tejas Kumar.
-->

---
layout: image
image: tejas.png
---

<!--
Tejas is fantastic and so is his work. His code mod was perfect for helping us migrate.

We ran it on our codebase and it handled most of the effort
-->
---
layout: image
image: canvas-codemod.png
---
<!--
Fast forward to 2021, and on my design system team, we were releasing a major version of our library and we had some major changes to our APIs.

So we decided to start including code mods to make it easier for teams to upgrade.

-->
---
layout: image
image: jscodeshift.png
---
<!--
The most popular tool for writing code mods is jscodeshift.

Let's jump back into AST Explorer and look at an example.
-->

---

# astexplorer.net

<!--
revisiting my previous example, we often promote components from our Labs and Preview packages to main.

Let's look at an example where we want to update the import package.

Let's say we're promoting the Segmented Control to main in our next release. In AST Explorer, let's change the transform to jscodeshift

again selecting a transform gives us an example with helpful boilerplate, this one just reverses string identifiers

the premise is a little different than ESLint: we are returning source that we modify in some way, if we need to.

we can find elements by a node type, and we have ways to update nodes as needed

if i'm being honest, a good code mod is a step up in effort from writing an ESLint rule. still, let's walk through the basics so you have an idea where to begin

-->
---

# astexplorer.net

<!--
LIVE CODE

as we're doing this, don't worry about the specific syntax, i just want to give you an idea of how this works

first we need to change what type of node we're looking for in the find

next let's setup some variables



```
const oldImport = "@workday/canvas-kit-preview-react/segmented-control";
const newImport = "@workday/canvas-kit-react/segmented-control";

    .find(j.ImportDeclaration)
    .filter((path) => path.node.source.value === oldImport)
    .forEach(path => {
      j(path).replaceWith(
        j.importDeclaration(path.node.specifiers, j.stringLiteral(newImport))
      );
    })
```
-->
---

# astexplorer.net

<!--
LIVE CODE


again, this is a little bit simplified for the sake of a quick example (switch to deck)

-->
---
layout: image
image: patch.jpg
class: darken
---
# Codemods are not always simple

<!--

I've heard this from lots of folks I talk to, and even seen some of the challenges with my team. It's not always easy to write good code mods that work in all use cases.

- codemods can be non-trivial to write
- and it's easy to accidentally target code we didn't intend to modify - we will need good tests
-->
---
layout: image
image: patch.jpg
class: darken
---

# Codemods benefit from...

- extracting utilities to tackle common patterns in your domain
- having a set of test cases that you always apply to a given type of codemod
- using CodeshiftCommunity.com!

<!--
Work around this by:
- extracting utilities to tackle common patterns in our domain
- having a set of test case that you always apply to a given type of code mod
- look at codeshiftcommunity.com! - it's a community project that offers really great examples, best practices, and some helpers you can import
-->
---
layout: image
image: bump.jpg
---

# The value of codemods

<!--
This month we released a new major version with breaking changes. We had several component APIs change. and while that might normally bother our users, instead, this was one of our reactions:
-->
---
layout: image
image: bump.jpg
class: darken
---

# The value of codemods

### "Hey team! Long time listener first time caller.

Just wanted to parachute in and mention how **AWESOME the v9 codemod** was to use! Made upgrading so, **so easy.**

You all _are amazing_!! Above and beyond!! 🚀🔥"

-- Simon Savage

<!--
Hey team! Long time listener first time caller.

Just wanted to parachute in and mention how AWESOME the v9 codemod was to use! Made upgrading so, so easy.

I've never met Simon but he carries a lot of respect in our community.

This was really nice to see, and kind of typical when you're automating all the breaking changes.
-->
---
layout: image
image: bulbs.jpg
class: darken
---

# The value of building your own tools


<!--
We're still only scratching the surface of what we can do with building our own tools. Whenever you do this, you're improving the developer experience for yourself and every other developer in your community. The impact can be massive, maybe 10x, maybe 1000x.

More importantly, the satisfaction is immmeasurable. Developers appreciate when their work becomes easier. Building tools that solve a problem is what our whole career is about. Doing this for each other is one of my favorite ways to work.
-->
---
layout: image
image: bulbs.jpg
class: darken
---

# Learn more and practice

### ESLint.org
<br />

### CodeshiftCommunity.com

<br />

### ASTsAreAwesome.com

<!--
I hope you have some ideas now about how to get started with building your own JavaScript tools.

To learn more about working with ESLint, I recommend their docs at eslint.org.
If you're working with code mods, the best place to go is the CodeshiftCommunity website

And for a quick review of everything we talked about, and links to even more resources, check out a site I built: astsareawesome.com
-->

---
layout: image-right
image: thank-you.jpg
---
<br />
<br /><br /><br /><br />

### <mdi-web /> willklein.co

### <mdi-email-outline /> will@willklein.co

### <mdi-twitter /> @willklein_

<style>
  h3 {
    color: #fff;
  }
</style>

<!--
Thank you all for listening, and please send me your questions, ideas, and thoughts on this topic anytime!
-->

---
layout: end
---

Built with:
- Sli.dev
- ASTExplorer.net
- VS Code
- Unsplash