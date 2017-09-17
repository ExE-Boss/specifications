Gecko Elevated WebExtensions Specification — Version 0.1.2
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
| -----	| -----	| -----	|
| `"page": "<Page URL>"`	| Required if `"scripts"` is not present	| Defines the elevated background page.	|
| `"scripts": [...]`	| Required if `"page"` is not present	| Defines the scripts that are added to the generated elevated background page.	|
| `"optional": boolean`	| Optional, defaults to `false`	| Specifies if this is an optional or required elevated background page.	|
| `strict_min_version: "<minimum compatible version>"`	| Optional, only permitted if this is an optional elevated background page, defaults to the same value as this extension’s `strict_min_version`. | Specifies the minimum Firefox version that this optional background page can be used in.	|
| `strict_max_version: "<maximum compatible version>"`	| Optional, only permitted if this is an optional elevated background page, defaults to the same value as this extension’s `strict_max_version`. | Specifies the maximum Firefox version that this optional background page can be used in.	|

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

### Optional elevated background page

If the elevated background page is marked as optional, it is disabled by default
and the user is given a prompt during installation if this extension’s elevated
background page should be enabled or not.

Whether the optional elevated background page should be loaded or not is also
configurable from the extension’s options page in `about:addons`.

It is also possible for the optional elevated background page to have its own
separate version compatibility set by Mozilla or the extension author.

The optional elevated background page is always disabled if it is incompatible
with this version of Firefox.

API Changes in elevated background page
---------------------------------------

In the elevated background page, some APIs have less restrictions than when
called from the normal background page.

### `tabs` API

| Function	| Changes	|
| -----	| -----	|
| `create(...)`	| The `url` property is unrestricted.	|
| `update(...)`	| The `url` property is unrestricted.	|

### `windows` API

| Function	| Changes	|
| -----	| -----	|
| `create(...)`	| The `url` property is unrestricted.	|

Handle elevated background page breakage
----------------------------------------

Since the elevated background page has access to browser internals,
the potential for the extension to break due to removed functionality is high.

Extension breakage is determined by checking for thrown `Error`s caused
by the extension trying to call non-existent functions or accessing
non-existent properties.

If an extension is determined to be broken, which method to use is determined
based on the value of the `applications.gecko.elevated_background.optional`
key.

### Handle optional elevated background page breakage

In the case of optional elevated background page breakage, the user is given
a prompt to disable the optional elevated background page and report
the breakage to Mozilla (specifically the AMO moderation team), who will then
be able to adjust the optional elevated background page’s version compatibility
setting appropriately.

### Handle required elevated background page breakage

In the case of required elevated background page breakage, the user is given
a prompt to disable the extension and report the breakage to Mozilla
(specifically the AMO moderation team), who will then be able to adjust
the extensions’s version compatibility setting appropriately.
