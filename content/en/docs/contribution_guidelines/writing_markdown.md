---
title: Write with Markdown
date: 2019-07-30T00:00:00.000Z
description: |
  Tips on writing and editing using Markdown.
---
This docs-as-code implementation uses the [Hugo](https://gohugo.io/) static site generator and the Google [Docsy theme](https://www.docsy.dev/) to build this website from the Markdown content files. Hugo uses [Blackfriday](https://github.com/russross/blackfriday) to parse Markdown..

## Editor recommendations

If you are editing Markdown locally, we recommend using VSCode with the `markdownlint` extension.

## Tips and tricks

Here are some tips and tricks for Markdown. Feel free to add your own!

### Nested lists

The Blackfriday Markdown parser has some known issues with nested lists. To avoid these, follow these guidelines and always preview any nested lists on Netlify or in a local Hugo server.

* Always use 4 spaces to indent nested lists. Ensure that tabs are set to 4 spaces in your editor.
* Do not specify a language for fenced code blocks within a nested list.

### Code samples

* Use 3 or more backticks to open and close a code sample block. You can specify a language after the first set of backticks to easily apply syntax highlighting.
    For example:
  ````
        ```md
        # This is a H1

        This is some text

        ## This is a H2

        This is more text
        ```
  ````
    Renders as:
  ```md
  # This is a H1

  This is some text

  ## This is a H2

  This is more text
  ```
* Use single backticks to apply code or monospace formatting to `inline` text.

{{% alert title="Caution" color="warning" %}}
The Zoomin plugin used when publishing docs to production <https://docs.axway.com> is very sensitive to `<`, `>`, and `\` characters. Ensure that these characters are always enclosed in backticks to have them processed as code instead of as HTML tags.
{{% /alert %}}

## Markdown extensions

 Hugo and Docsy provide some useful extensions to standard Markdown that you can use to simplify editing and formatting.

### Notes or alerts

Use an alert shortcode for any text that you want to draw attention to. For example:

```go-html-template
{{%/* alert title="Note" */%}}
This is a note.
{{%/* /alert */%}}
```

Renders to:

{{% alert title="Note" %}}
This is a note.
{{% /alert %}}

See <https://www.docsy.dev/docs/adding-content/shortcodes/#alert> for more details.

### YouTube video links

To display a YouTube video preview on a page, use the following Hugo shortcode. You only need to specify the ID of the video.

```go-html-template
{{</* youtube QTpB3e2DZBg */>}}
```

Renders to:

{{< youtube QTpB3e2DZBg >}}

See <https://gohugo.io/content-management/shortcodes/#youtube> for more details.

<!-- Tables TODO-->
