---
sidebar_position: 2
description: API reference for genesis_tool_api.
slug: /api/genesis_tool_api
---

# `genesis_tool_api`

import APITable from '@site/src/components/APITable';

```python title="genesis_tool_api"
@ti.kernel
def genesis_tool_api(
    links_idx: ti.types.ndarray(),
    poss: ti.types.ndarray(),
    quats: ti.types.ndarray(),
    n_links: ti.i32,
    custom_init_q: ti.i32,
    init_q: ti.types.ndarray(),
    max_samples: ti.i32,
    max_solver_iters: ti.i32,

)
```

## Optional fields {#optional-fields}

### `links_idx` {#links_idx}

Path to your site favicon; must be a URL that can be used in link's href. For example, if your favicon is in `static/img/favicon.ico`:

```js title="API 1"
export default {
  favicon: '/img/favicon.ico',
};
```

### `links_idx` {#links_idx}

- Type: `boolean | undefined`

Allow to customize the presence/absence of a trailing slash at the end of URLs/links, and how static HTML files are generated:

- `undefined` (default): keeps URLs untouched, and emit `/docs/myDoc/index.html` for `/docs/myDoc.md`
- `true`: add trailing slashes to URLs/links, and emit `/docs/myDoc/index.html` for `/docs/myDoc.md`
- `false`: remove trailing slashes from URLs/links, and emit `/docs/myDoc.html` for `/docs/myDoc.md`

### `onBrokenAnchors` {#onBrokenAnchors}

- Type: `'ignore' | 'log' | 'warn' | 'throw'`

The behavior of Docusaurus when it detects any broken anchor declared with the `Heading` component of Docusaurus.

By default, it prints a warning, to let you know about your broken anchors.

### `onBrokenMarkdownLinks` {#onBrokenMarkdownLinks}

- Type: `'ignore' | 'log' | 'warn' | 'throw'`

The behavior of Docusaurus when it detects any broken Markdown link.

By default, it prints a warning, to let you know about your broken Markdown link.
