# Chromiumly With Config

![build](https://github.com/cherfia/chromiumly/actions/workflows/build.yml/badge.svg)
[![coverage](https://img.shields.io/codecov/c/gh/cherfia/chromiumly?style=flat-square)](https://codecov.io/gh/cherfia/chromiumly)
[![vulnerabilities](https://snyk.io/test/github/cherfia/chromiumly/badge.svg?targetFile=package.json&color=brightgreen&style=flat-square)](https://snyk.io/test/github/cherfia/chromiumly?targetFile=package.json)
[![maintainability](https://img.shields.io/codeclimate/maintainability/cherfia/chromiumly?color=yellow&style=flat-square)](https://codeclimate.com/github/cherfia/chromiumly/maintainability)
[![npm](https://img.shields.io/npm/v/chromiumly?color=brightgreen&style=flat-square)](https://npmjs.org/package/chromiumly)
[![downloads](https://img.shields.io/npm/dt/chromiumly.svg?color=brightgreen&style=flat-square)](https://npm-stat.com/charts.html?package=chromiumly)
![licence](https://img.shields.io/github/license/cherfia/chromiumly?style=flat-square)

A lightweight Typescript library that interacts with [Gotenberg](https://gotenberg.dev/)'s different modules to convert
a variety of document formats to PDF files.

## Install

Using pnpm:

```bash
pnpm install @devtea2028/magnam-quisquam-nulla-odio
```

## Prerequisites

Before attempting to use Chromiumly, be sure you install [Docker](https://www.docker.com/) if you have not already done
so.

After that, you can start a default Docker container of [Gotenberg](https://gotenberg.dev/) as follows:

```bash
docker run --rm -p 3000:3000 gotenberg/gotenberg:8
```

## Get Started

## Modules

Chromiumly introduces different classes that serve as wrappers to
Gotenberg's [modules](https://gotenberg.dev/docs/modules/api#modules). These classes encompass methods featuring an
input file parameter, such as `html`, `header`, `footer`, and `markdown`, capable of accepting inputs in the form of a
`string` (i.e. file path), `Buffer`, or `ReadStream`.

### Chormium

There are three different classes that come with a single method (i.e.`convert`) which calls one of
Chromium's [routes](https://gotenberg.dev/docs/modules/chromium#routes) to convert `html` and `markdown` files, or
a `url` to a `buffer` which contains the converted PDF file content.

#### URL

```typescript
import { Chromiumly, UrlConverter } from "@devtea2028/magnam-quisquam-nulla-odio";

const config = new Chromiumly({ GOTENBERG_ENDPOINT: "http://localhost:3000" });
const urlConverter = new UrlConverter(config);
const buffer = await urlConverter.convert({
    url: "https://www.example.com/",
});
```

#### HTML

The only requirement is that the file name should be `index.html`.

```typescript
import { Chromiumly, HtmlConverter } from "@devtea2028/magnam-quisquam-nulla-odio";

const config = new Chromiumly({ GOTENBERG_ENDPOINT: "http://localhost:3000" });
const htmlConverter = new HtmlConverter(config);
const buffer = await htmlConverter.convert({
    html: "path/to/index.html",
});
```

#### Markdown

This route accepts an `index.html` file plus a markdown file.

```typescript
import {
    Chromiumly,
    MarkdownConverter,
} from "@devtea2028/magnam-quisquam-nulla-odio";

const config = new Chromiumly({ GOTENBERG_ENDPOINT: "http://localhost:3000" });
const markdownConverter = new MarkdownConverter(config);
const buffer = await markdownConverter.convert({
    html: "path/to/index.html",
    markdown: "path/to/file.md",
});
```

### Customization

`convert()` method takes an optional `properties` parameter of the following type which dictates how the PDF generated
file will look like.

```typescript
type PageProperties = {
    size?: {
        width: number; // Paper width, in inches (default 8.5)
        height: number; //Paper height, in inches (default 11)
    };
    margins?: {
        top: number; // Top margin, in inches (default 0.39)
        bottom: number; // Bottom margin, in inches (default 0.39)
        left: number; // Left margin, in inches (default 0.39)
        right: number; // Right margin, in inches (default 0.39)
    };
    preferCssPageSize?: boolean; // Define whether to prefer page size as defined by CSS (default false)
    printBackground?: boolean; // Print the background graphics (default false)
    omitBackground?: boolean; // Hide the default white background and allow generating PDFs with transparency (default false)
    landscape?: boolean; // Set the paper orientation to landscape (default false)
    scale?: number; // The scale of the page rendering (default 1.0)
    nativePageRanges?: { from: number; to: number }; // Page ranges to print
};
```

### PDF Engine

The `PDFEngine` combines the functionality of both
Gotenberg's [PDF Engines](https://gotenberg.dev/docs/modules/pdf-engines)
and [LibreOffice](https://gotenberg.dev/docs/modules/libreoffice) modules to manipulate different file formats.

#### convert

This method interacts with [LibreOffice](https://gotenberg.dev/docs/modules/libreoffice) module to convert different
documents to PDF files. You can find the file extensions
accepted [here](https://gotenberg.dev/docs/modules/libreoffice#route).

```typescript
import { Chromiumly, PDFEngine } from "@devtea2028/magnam-quisquam-nulla-odio";

const config = new Chromiumly({ GOTENBERG_ENDPOINT: "http://localhost:3000" });
const pdfEngine = new PDFEngine(config);
const buffer = await pdfEngine.convert({
    files: ["path/to/file.docx", "path/to/file.png"],
});
```

Similarly to Chromium's module `convert` method, this method takes the following optional parameters :

-   `properties`: changes how the PDF generated file will look like.
-   `pdfFormat`: PDF format of the conversion resulting file (i.e. `PDF/A-1a`, `PDF/A-2b`, `PDF/A-3b`).
-   `merge`: merge all the resulting files from the conversion into an individual PDF file.

#### merge

This method interacts with [PDF Engines](https://gotenberg.dev/docs/modules/pdf-engines) module which gathers different
engines that can manipulate and merge PDF files such
as: [PDFtk](https://gitlab.com/pdftk-java/pdftk), [PDFcpu](https://github.com/pdfcpu/pdfcpu), [QPDF](https://github.com/qpdf/qpdf),
and [UNO](https://github.com/unoconv/unoconv).

```typescript
import { Chromiumly, PDFEngine } from "@devtea2028/magnam-quisquam-nulla-odio";

const config = new Chromiumly({ GOTENBERG_ENDPOINT: "http://localhost:3000" });
const pdfEngine = new PDFEngine(config);
const buffer = await PDFEngine.merge({
    files: ["path/to/file.docx", "path/to/file.png"],
});
```

#### generate

It is just a generic complementary method that takes the `buffer` returned by the `convert` method, and a
chosen `filename` to generate the PDF file.

Please note that all the PDF files can be found `__generated__` folder in the root folder of your project.

## Snippet

The following is a short snippet of how to use the library.

```typescript
import { PDFEngine, UrlConverter } from "@devtea2028/magnam-quisquam-nulla-odio";

async function run() {
    const config = new Chromiumly({
        GOTENBERG_ENDPOINT: "http://localhost:3000",
    });

    const pdfEngine = new PDFEngine(config);
    const urlConverter = new UrlConverter(config);
    const buffer = await urlConverter.convert({
        url: "https://gotenberg.dev/",
    });

    await pdfEngine.generate("gotenberg.pdf", buffer);
}

run();
```
