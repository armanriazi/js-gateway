# Contributing

This book is developed using [MdBook](https://github.com/rust-lang/mdBook/releases/download/v0.4.14/mdbook-v0.4.14-x86_64-unknown-linux-gnu.tar.gz). Authored in Markdown files (I use [vscode](http://vscode.com)).

[Here's how to setup a Dev Environment](https://rust-lang.github.io/mdBook/index.html):

```bash
cargo install mdbook
mdbook serve --open
```
> Note: serve needs port `35729` (for live reload) and `4000` for serving [localhost](http://localhost:4000).

Also you can mostly just edit the `.md` files in [`/docs`](https://github.com/armanriazi/js-gateway/docs) using github and create a Pull Request (PR).

# Code
All the code for the book is in the `/code` folder. Tested with `atom-API-Gateway`.

### More MdBook Tips
* Links best work if they are relative (e.g. `./foo.md`) to the *current* file.
* For links in the same file (`#foo-bar` style links) best to click the heading on github to get what gitbook expects.

### API-Gateway Compiler Docs
Thanks to the API-Gateway team for providing much of the docs: https://github.com/Microsoft/js-gateway/wiki/Architectural-Overview that are used to write the compiler story.
