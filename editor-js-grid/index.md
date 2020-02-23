---
title: How to make grid layout in Editor.js
description: Use editor-js-grid to design layout more flexibly.
---

<ol class="table-of-contents"></ol>

<!-- ディスプレイ広告 -->
<!-- textlint-disable -->

<ins class="adsbygoogle"
style="display:block"ｌｓ
data-ad-client="ca-pub-7008780049786244"
data-ad-slot="5063315418"
data-ad-format="auto"
data-full-width-responsive="true"></ins>

<!-- textlint-enable -->
<script>(adsbygoogle = window.adsbygoogle || []).push({});</script>

## Use Editor.js more flexibly

Editor.js is one of block editors.
The article data format is JSON not HTML, so it can manage article data more cleanly and formally.

Editor.js can't nest blocks to keep simplicity.
So it is useful for general articles that blogs, news and so on.
But difficult for special layout that landing page and so on.

Therefore, I published a grid layout plugin for Editor.js.

## Try it out!

[editor-js-grid - npm](https://www.npmjs.com/package/editor-js-grid)

![Demo](https://user-images.githubusercontent.com/7702653/74583580-b634d700-500b-11ea-8e9d-e81205dfafb2.gif)

[![Edit editor-js-grid-demo](https://codesandbox.io/static/img/play-codesandbox.svg)](https://codesandbox.io/s/editor-js-grid-demo-fbib3?fontsize=14&hidenavigation=1&theme=dark)

## Concepts

editor-js-grid is a reduced plugin that provides only grid design system.
editor-js-grid provides a minimum DOM that can edit plain text by default.
By providing DOM in yourself, it can provide image, video and other contents.

## Roadmap

The roadmap is open via [GitHub Projects](https://github.com/blue-hood/editor-js-grid/projects/1).

## Show your support

Give a ⭐️ if this project helped you!

<!-- textlint-disable japanese/sentence-length -->

<a class="github-button" href="https://github.com/blue-hood/editor-js-grid" data-icon="octicon-star" data-size="large" aria-label="Star blue-hood/editor-js-grid on GitHub">Star</a>

<!-- textlint-enable japanese/sentence-length -->
