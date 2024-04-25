### document.caretPositionFromPoint in shadow DOM scenario

#### Background

The [document.caretPositionFromPoint](https://www.w3.org/TR/cssom-view-1/#ref-for-dom-document-caretpositionfrompoint-1) API returns a
[CaretPosition](https://www.w3.org/TR/cssom-view-1/#the-caretposition-interface) which represents the caret position indicating current
text insertion point. However, current [spec](https://www.w3.org/TR/cssom-view-1/#dom-document-caretpositionfrompoint) is vague about
the behavior in shadow DOM scenario. Gecko is the only engine that implemented this API as of today. But Gecko's behavior in shadow DOM
violates the boundary of closed shadow tree by piercing into any shadow tree regardless of the mode of the tree.

We want to specify the behavior in shadow DOM to create better web compatability and interoprability of this API.

#### Proposal
We should introduce another argument `shadowRoots` which is the list of shadow roots that the API can pierce into. The
API will look like:

```js
document.caretPositionFromPoint(double x, double y, ShadowRoot... shadowRoots)
```

the returned caret position will only be in the shadow root if it's provided in `shadowRoots` argument. Otherwise, tie caret position will be
in the shadow host's parent at the offset of the shadow host.

The proposed change to the API maintains the boundary of closed shadow tree unless the shadow root is provided to the API.

#### Alternative considered
- Do not let this API pierce into shadow tree. Instead, put this API on `DocumentOrShadowRoot`.

The con to this approach is that the usage might be cumbersome to get a position inside nested shadow roots (need to call the API on each level of shadow root).

#### Use case/developer need
Web developer need to have a way to get a caret position inside shadow roots.

With the proposal, web developer can get caret position inside shadow root like:
```js
var caret_position = document.caretPositionFromPoint(x, y, shadowRoot1);
```