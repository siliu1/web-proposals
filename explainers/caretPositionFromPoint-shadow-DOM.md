### document.caretPositionFromPoint in shadow DOM scenario

#### Background

The [document.caretPositionFromPoint](https://www.w3.org/TR/cssom-view-1/#dom-document-caretpositionfrompoint) returns
a [CaretPosition](https://www.w3.org/TR/cssom-view-1/#the-caretposition-interface) which represents the caret position
indicating current text insertion point. However, the spec is vague about the behavior in shadow DOM scenario.

#### Proposal
We should introduce another argument `shadowRoots` which is the list of shadow roots that the API can pierce into. The
API will look like:

```js
document.caretPositionFromPoint(x, y, shadowRoots)
```

The proposed change to the API maintains the boundary of closed shadow tree unless the shador root is provided to the API.
