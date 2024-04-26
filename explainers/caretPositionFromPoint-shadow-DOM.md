### document.caretPositionFromPoint in shadow DOM scenario

#### Use case/developer need
It's necessary for web developers to have a method to determine the caret position within shadow roots. This allows for indentifying node
and offset within the shadow root so that range/selection creation is possible in shadow root at specific point.

With the proposal, web developer can get caret position inside shadow root like:
```js
var caret_position = document.caretPositionFromPoint(x, y, shadowRoot1);
// caret_position.offsetNode is inside shadowRoot1
// caret_position.offset is the offset within offsetNode
// caret_position.getClientRect() returns a DOMRect which would be the caret bounds in shadowRoot1.
```

#### Background

The [document.caretPositionFromPoint](https://www.w3.org/TR/cssom-view-1/#ref-for-dom-document-caretpositionfrompoint-1) API returns a
[CaretPosition](https://www.w3.org/TR/cssom-view-1/#the-caretposition-interface) which represents the caret position indicating current
text insertion point. However, the current [spec](https://www.w3.org/TR/cssom-view-1/#dom-document-caretpositionfrompoint) doesn't
specifically define if and how shadow DOM scenarios should be supported. We want to update the API and spec to support shadow DOM cases
in a well defined way. 

#### Proposal
Similar to [Selection.getComposedRanges](https://w3c.github.io/selection-api/#ref-for-dom-selection-getcomposedranges-1) API, We should
introduce another argument `shadowRoots` which is the list of shadow roots that the API can pierce into. The API will look like:

```js
document.caretPositionFromPoint(double x, double y, ShadowRoot... shadowRoots)
```

the returned caret position will only be in the shadow root if it's provided in `shadowRoots` argument. Otherwise, the caret position will
be in the shadow host's parent at the offset of the shadow host.

The proposed change to the API maintains the boundary of closed shadow tree unless the shadow root is provided to the API.

#### Alternative considered
- Do not let this API pierce into shadow tree. Instead, put this API on `DocumentOrShadowRoot`.

The con to this approach is that the usage might be cumbersome to get a position inside nested shadow roots (need to call the API on each
level of shadow root).
