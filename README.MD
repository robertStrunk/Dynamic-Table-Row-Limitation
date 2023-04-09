### This document highlights a major limitation faced when creating dynamic table rows in LWC.

By dynamic table rows, I am referring to a scenario where you are iterating over a collection with the intent of creating a `<tr>` for every item in the collection. 

Before we talk dynamic table rows, let's first establish a baseline understanding of a critical best practice when working with iterations in LWC. 

It is considered a best practice to use child components when iterating collections in LWC markup.
For example:

```html
<!-- Best Practice -->
<template for:each={collection} for:item="element">
    <c-child-component
        key={element.Id}
        item={element}>
    </c-child-component>
</template>
```
is the correct approach compared to:

```html
<!-- ANTI PATTERN -->
<template for:each={collection} for:item="element">
    <div key={element.Id}>
        <p>{element.title}</p>
        <p>{element.body}</p>
    </div>
</template>
```

A problem arises, however, when you are dealing with html tables. 
For example:

```html
<table>
    <thead>
        <th>Column 1</th>
        <th>Column 2</th>
    </thead>
    <tbody>
        <template for:each={collection} for:item="element">
            <c-child-component
                key={element.Id}
                item={element}>
            </c-child-component>
        </template>
    </tbody>
</table>
```
Even though the above is following best practices, the output is not well-formed HTML and does not get handled by the browser properly.
```html
<!-- Resulting HTML -->
<table>
    <thead>
        <th>Column 1</th>
        <th>Column 2</th>
    </thead>
    <tbody>
        <c-child-component>
            <tr>
                <td>some_text_here</td>
                <td>some_text_here</td>
            </tr>
        </c-child-component>
        <c-child-component>
            <tr>
                <td>some_text_here</td>
                <td>some_text_here</td>
            </tr>
        </c-child-component>
    </tbody>
</table>
```
Notice the `<c-child-component>` elements are the children of the `tbody` element. That is not part of the [html table specification](https://html.spec.whatwg.org/multipage/tables.html#the-table-element) and thus is not handled by the browser in the way we expect (no matter how bad we really want it to :laughing:).

The cause is related to how the LWC engine performs rendering and is outside the scope of this topic. 

For basic use cases, this is an easy problem to solve. All you need to do is modify the `HTMLElement.style.display` property to be `table-row` as shown below:

```css
/* childComponent.css */
:host {
    display: table-row;
}
```

The above css rule notifies the browser to treat the `<c-child-component>` elements as standard `<tr>` elements.

That solution, however, does not work for recursive scenarios. For more info on that, please refer to my [stack exchange post](https://salesforce.stackexchange.com/questions/399873/lwc-table-with-dynamic-rows-from-tree-data).
