---
layout: post
category : Computation
tags : [markdown, hack]
---
{% include JB/setup %}


[Markdown-here](http://markdown-here.com) is a great extension to enable markdown style format in email. However, the add-on available for [Postbox](http://www.postbox-inc.com) does not support preference setting, which means that you can't enable "TeX Mathematical Formulae Support" in the preference page. The way to solve this is to hard code the add-on installation file to enable Tex support. Below is the detailed steps:

* The markdown-here add-on for Postbox (markdown_here-2.10.0-fx.xpi) can be downloaded [here](https://addons.mozilla.org/en-US/thunderbird/addon/markdown-here/). 
* Change the extension of the .xpi file to .zip.
* Do not unzip the file; try to use software that can change files inside the zip folder directly. The one I used is [BBEdit](http://www.barebones.com/products/bbedit/). 
* Locate the file ~/Downloads/markdown_here-2.10.0-fx.zip/common/markdown-render.js.
* Change line 85 ```math: userprefs['math-enabled'] ? mathify : null,``` to ```math: mathify ```.
* Save changes and change extension of markdown_here-2.10.0-fx back to .xpi. 
* Install add-on in Postbox and now formulas in markdown formatted emails will get rendered. 
