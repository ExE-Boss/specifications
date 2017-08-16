Gecko Elevated WebExtensions Specification — Version 0.1.0
==========================================================

This document describes the Gecko Elevated WebExtensions specification.

Gecko Elevated WebExtensions are a non-standard Gecko specific extension
to the WebExtensions specification allowing WebExtension authors to do things
for which there are no WebExtension APIs.

Purpose of Gecko Elevated WebExtensions
---------------------------------------

Gecko Elevated WebExtensions are intended to allow Mozilla Firefox to retain
its status as the browser with the most powerful extension API.

Developers should use WebExtension APIs wherever possible even when writing
Gecko Elevated WebExtensions to minimize the amount of code that can break
between Firefox releases, because unlike before, Mozilla will no longer
be keeping legacy code around, requiring Gecko Elevated WebExtension
developers to keep pace with Mozilla.

`manifest.json` keys
--------------------

Gecko Elevated WebExtensions are denoted by having the
`applications.gecko.elevated_background` key.

### The `applications.gecko.elevated_background` key

This key is a JSON object containing the following keys:

| Key	| Required	| Details	|
| ----	| ----	| ----	|
| `"page": "<Page URL>"`	| Required if `"scripts"` is not present	| Defines the elevated background page.	|
| `"scripts": [...]`	| Required if `"page"` is not present	| Defines the scripts that are added to the generated elevated background page.	|

The elevated background page
----------------------------

The elevated background page is a special background page in that it has all
the supported [Host](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#Host_permissions)
and [API](https://developer.mozilla.org/en-US/Add-ons/WebExtensions/manifest.json/permissions#API_permissions)
permissions regardless of what is defined in the `"permissions"` key.

This is because the elevated background page has unrestricted access to all
the browser internals, just like [Legacy Bootstrapped Extensions](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/Bootstrapped_extensions),
and as such can just call the underlying implementation of the WebExtension
APIs.

Note that non-elevated background pages from other extensions can’t message
elevated background directly, and must instead send the messages using the
normal background page as a proxy.

Elevated background pages from other elevated extensions can message each other
using WebExtension APIs for the same reason as to why elevated background pages
have access to all WebExtension APIs.
