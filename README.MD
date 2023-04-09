This document highlights a major limitation faced when creating dynamic table rows in LWC. By dynamic table rows, I am referring to a scenario where you are iterating over a collection with the intent of creating a <tr> for every item in the collection. 

Before we talk dynamic table rows, lets first establish a baseline understanding of a critical best practice when working with iterations in LWC. 

It is considered a best practice to use child components when iterating collections in LWC markup. For example:

```html
<!-- Best Practice -->
<template for:each={collection} for:item="element">
    <c-child-component key={element.Id} item={element}></c-child-component>
</template>
```
is the correct approach compared to

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