# CBOR Schema Working Group Website

---

## Overview

This repository contains the contents for the CBOR Schema Documentations
deployed at [https://cbor.ai](https://cbor.ai) by [cbor-schema.github.io](https://github.com/cbor-schema/cbor-schema.github.io).

The site is built using [Docusaurus 2](https://docusaurus.io/).

## Contributing

Contributing to the docs site is a great way to get involved with the CBOR Schema community!
Here's some things you need to know to get started.

### Contents

- All the doc text are located in the [docs](docs) directory; image files are put under
[static/img](static/img) directory where a sub-dir is strongly recommended if there are multiple
image files for a doc.
- The left sidebar of the page is controlled by [sidebars.js](sidebars.js).
- Style Guide can be found [here](style-guide.md). In addition, this repository
uses a series of style checking and formatting tools. See
[style-checker-notes.md](style-checker-notes.md) for more details and how to fix errors.
- Extensive docs for Docusaurus can be found [here](https://docusaurus.io/docs).

### Installation

```
$ yarn
```

### Local Development

```
$ yarn start
```

This command starts a local development server and opens up a browser window. Most changes are reflected live without having to restart the server.

### Build

```
$ yarn build
```

This command generates static content into the `build` directory and can be served using any static contents hosting service.

## License

The contents of this repository are [licensed under](./LICENSE) either the BSD 3-clause license *or* the Academic Free License v3.0.
