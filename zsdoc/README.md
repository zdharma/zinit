# Code documentation

Here is `Asciidoc` code documentation generated using [Zshelldoc](https://github.com/zdharma/zshelldoc).
There are `4` Zinit's source files, the main one is [zinit.zsh](zinit.zsh.adoc). The documentation
lists all functions, interactions between them, their comments and features used.

Github allows to directly view `Asciidoc` documents:
 * [zinit.zsh](zinit.zsh.adoc) – always loaded, in `.zshrc` ([pdf](http://zdharma.org/zinit/zinit.zsh.pdf))
 * [zinit-side.zsh](zinit-side.zsh.adoc) – common functions, loaded by `*-install` and `*-autoload` scripts ([pdf](http://zdharma.org/zinit/zinit-side.zsh.pdf))
 * [zinit-install.zsh](zinit-install.zsh.adoc) – functions used only when installing a plugin or snippet ([pdf](http://zdharma.org/zinit/zinit-install.zsh.pdf))
 * [zinit-autoload.zsh](zinit-autoload.zsh.adoc) – functions used only in interactive `Zinit` invocations ([pdf](http://zdharma.org/zinit/zinit-autoload.zsh.pdf))

# PDFs, man pages, etc.

Formats other than `Asciidoc` can be produced by using provided Makefile. For example, issuing
`make pdf` will create and populate a new directory `pdf` (requires `asciidoctor`, install with
`gem install asciidoctor-pdf --pre`). `make man` will create man pages (requires package `asciidoc`,
uses its command `a2x`, which is quite slow).
