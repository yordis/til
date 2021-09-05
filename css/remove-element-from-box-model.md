# Remove Element from Box Model

Sometimes you want to remove elements from participating in the Box Model. For
example, in NextJS most of the time we want to ignore the mounting point
`#__next`.

```css
#__next {
    display: contents;
}
```
