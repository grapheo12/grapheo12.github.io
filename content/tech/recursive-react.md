+++
title = "Easy Form Management in React"
date = 2020-07-27

[taxonomies]
tags = ["javascript", "react", "redux"]
+++

Making a form in React and maintaining it can be painful.
Especially if the form is complicated, with lots of sections and subsections.
Each time a new change needs to be made,
one needs to copy-paste some `input` tags,
setup default values and configure the state of the form.
If this sounds like an easy thing to do,
wait till your client tells you to put
field `A` from sub-subsection `X` of subsection `Y` of section `Z`
inside subsection `P` of section `B`!
Also, this [copypasta](https://en.wikipedia.org/wiki/Copypasta)
pattern goes against the **DRY** (Don't Repeat Yourself) principle.
To get a feel for it, assume you have 100 copy-pasted components,
and you need to change the input type of each one of them.
Nightmare isn't it?

<!-- more -->

## Understanding the structure

Your whole HTML Webpage, is rendered in the form of a DOM Tree in the browser.

The form is a part of it.
The good thing about a Tree Data Structure is that,
each of the child nodes that arise from a root node,
is a tree in its own right.
Hence a form can be modeled as a tree
and so can be its subsections.

![From Form to Tree](/img/FormToGraph.png)

Now, if you compress the value of each input into a simple JS object
(which you can use as the state of the form)
you get another tree structure, the object-tree.

For the image above, the tree would look like:

```ts
{
    a: {
        input1: "value1",
        input2: "value2"
    },
    input3: "value3",
    b: {
        input4: "value4",
        c: {
            input5: "value5"
        }
    }
}
```

Notice that if you have a consistent component creation scheme,
(i.e., you create and style all your input fields in a similar way),
this JSON above is all the information you need to create the form.

## Enter Recursive React Components

Till now, we haven't seen any React magic happening.
Let's look into it now.
Algorithmically speaking (checkmate Competitive Programmers!),
if we do a [Depth First Traversal](https://www.geeksforgeeks.org/depth-first-search-or-dfs-for-a-graph/)
of the JSON object,
generate an appropriate input field for the leaf nodes
and generate section headers for the non-leaf nodes,
we are done.

Abstract pseudo-code for the above strategy is:

```ts

function generate_form(jsonData){
    for (key in jsonData){
        if (jsonData[key] is Object){
            generate_heading(key);
            generate_form(jsonData[key]);
            end_heading(key);
        }else{
            generate_input(name=key, defaultValue=jsonData[key]);
        }
    }
}
```

React Components are just JS functions.
This means that the above pseudo-code function can be
directly translated to a functional component.


## Wait! But how do I update states?

Well, there can be many approaches to this.
But please do not directly modify the state object
using references to its child nodes.

![Meme](/img/reactlaw.png)

One method that you can follow is this:

1. Create a [deep copy](https://stackoverflow.com/questions/184710/what-is-the-difference-between-a-deep-copy-and-a-shallow-copy) of your state.
2. Pass this deepcopy to the Recursive Form Component as a scapegoat.
3. Create a function which when called, simply sets the state
to a deep copy of the above mentioned deepcopy (yes, you need to do a copy of copy).
4. On change of any field in the form,
directly assign the new value to the concerned field in the deepcopy of step 2 and fire the function mentioned in step 3.

The component rendering cycle is as follows:

![Component Rendering Cycle](/img/reactflow.svg)

The reason why we are copying for the 2nd time
is that React does a shallow checking of the states to
decide whether to re-render a component.
Doing a deep copy, will definitely change the reference
of the variable passed.
This will force React to rerender.


## Talk is cheap ...

So, we are done with our [Requirement Analysis and Design](https://en.wikipedia.org/wiki/Waterfall_model),
let's dive into the code.

The code I'm going to share here is from a live project, albeit a little modified.

It uses Accordion, Form and Card Components of the [Material UI](https://material-ui.com/) library.
I am not exhaustively including the imports to keep the code brief and to the point.
Instead of using states, here I have used a Redux store for state management.

First, let me show you the recursive component.

{{ gist(src="grapheo12/c0f9d54989dd476272fe9d9bdf6f80b7" file="GenericForm.js") }}

The `schema` is used as the single source of truth for the rendering.
This becomes particularly useful when dealing with arrays (I omitted that part here.)
Here `setData` is the function described in step 3 above.
The functions `display` and `format` come in handy to decide whether to show and how to show an element respectively.
The function `keyToFieldName` converts camel case like `thisIsAField` to a space delimited phrase like `This Is A Field`.

Notice how for the leaf nodes, the value in schema defines the
type of the Input field to be used and also its default value.

Here is how you use the component:

{{ gist(src="grapheo12/c0f9d54989dd476272fe9d9bdf6f80b7" file="CustomForm.js") }}

Notice how we are using the `cloneDeep` function from `lodash` library to perform the deep copy.

`dispatch` and `mapStateToProps` are usual Redux jargon.
`city` is the actual data field on which the form is built.
`saveCity` is a function that wraps the data to be used
to set the `city` object inside a Redux action.
`defaultCity` is the default schema (obvious!).

## Result

![Final Result](/img/reactresult.png)

This is what it kinda looks like.
I think you can guess how `city` data structure looked like.

I am new to Redux.
Let me know in the comments if you found anything wrong here.
