# VueFuseSearchField

Props:
* `list`: the list of objects to search inside
* `search_keys`: the keys of objects to search inside
* `defaultAll`: show all by default? (usually false)
* `placeholder`: what to show inside search field
* `element_key`: unique identifier for an element inside `list`, function. f.e.: `(d) => d.gkz`

Events:
* `selected`, with the selected list element as the only parameter ($event)

Usage:
