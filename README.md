## Hologram Documentation Source

This repository contains the Markdown sources that we use to generate the
[Hologram Documentation](https://hologram.io/docs/). If you spot an error or
omission in our docs, please submit a pull request or open an issue.

This repo has three subdirectories:

* **markdown/** -- The pages themselves. Includes guides, reference, and
  tutorial pages.
* **includes/** -- Small bits of Markdown content that can be included in
  multiple pages using a handlebars helper. Useful for commonly used disclaimers
  and instruction snippets.
* **json/** -- Table data in JSON format. Mainly used on the Downloads and Dash
  Datasheet pages.

The root directory also contains **apiary.apib**. This is the source for
our [HTTP API docs](http://docs.hologram.apiary.io/#), hosted on Apiary.
