# peasy-image-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-image-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-image-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-image-go)](https://goreportcard.com/report/github.com/peasytools/peasy-image-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Go client for the [PeasyImage](https://peasyimage.com) API — image resize, crop, convert, and compress. Zero dependencies beyond the Go standard library.

Built from [PeasyImage](https://peasyimage.com), a comprehensive image processing toolkit offering free online tools for resizing, cropping, converting, and compressing images across all major formats with detailed format guides and glossary.

> **Try the interactive tools at [peasyimage.com](https://peasyimage.com)** — [Image Tools](https://peasyimage.com/), [Image Glossary](https://peasyimage.com/glossary/), [Image Guides](https://peasyimage.com/guides/)

## Install

```bash
go get github.com/peasytools/peasy-image-go
```

Requires Go 1.21+.

## Quick Start

```go
package main

import (
	"context"
	"fmt"
	"log"

	peasyimage "github.com/peasytools/peasy-image-go"
)

func main() {
	client := peasyimage.New()
	ctx := context.Background()

	// List available image tools
	tools, err := client.ListTools(ctx, nil)
	if err != nil {
		log.Fatal(err)
	}
	for _, t := range tools.Results {
		fmt.Printf("%s: %s\n", t.Name, t.Description)
	}
}
```

## API Client

The client wraps the [PeasyImage REST API](https://peasyimage.com/developers/) with typed Go structs and zero external dependencies.

```go
client := peasyimage.New()
// Or with a custom base URL:
// client := peasyimage.New(peasyimage.WithBaseURL("https://custom.example.com"))
ctx := context.Background()

// List tools with pagination
tools, _ := client.ListTools(ctx, &peasyimage.ListOptions{Page: 1, Limit: 10})

// Get a specific tool by slug
tool, _ := client.GetTool(ctx, "image-resize")
fmt.Println(tool.Name, tool.Description)

// Search across all content
results, _ := client.Search(ctx, "convert webp", nil)
fmt.Printf("Found %d tools\n", len(results.Results.Tools))

// Browse the glossary
glossary, _ := client.ListGlossary(ctx, &peasyimage.ListOptions{Search: str("aspect-ratio")})
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Discover guides
guides, _ := client.ListGuides(ctx, &peasyimage.ListGuidesOptions{Category: str("optimization")})
for _, g := range guides.Results {
	fmt.Printf("%s (%s)\n", g.Title, g.AudienceLevel)
}

// List file format conversions
conversions, _ := client.ListConversions(ctx, &peasyimage.ListConversionsOptions{Source: str("png")})

// Get format details
format, _ := client.GetFormat(ctx, "webp")
fmt.Printf("%s (%s): %s\n", format.Name, format.Extension, format.MimeType)
```

Helper for optional string parameters:

```go
func str(s string) *string { return &s }
```

### Available Methods

| Method | Description |
|--------|-------------|
| `ListTools(ctx, opts)` | List tools (paginated, filterable) |
| `GetTool(ctx, slug)` | Get tool by slug |
| `ListCategories(ctx, opts)` | List tool categories |
| `ListFormats(ctx, opts)` | List file formats |
| `GetFormat(ctx, slug)` | Get format by slug |
| `ListConversions(ctx, opts)` | List format conversions |
| `ListGlossary(ctx, opts)` | List glossary terms |
| `GetGlossaryTerm(ctx, slug)` | Get glossary term |
| `ListGuides(ctx, opts)` | List guides |
| `GetGuide(ctx, slug)` | Get guide by slug |
| `ListUseCases(ctx, opts)` | List use cases |
| `Search(ctx, query, limit)` | Search across all content |
| `ListSites(ctx)` | List Peasy sites |
| `OpenAPISpec(ctx)` | Get OpenAPI specification |

Full API documentation at [peasyimage.com/developers/](https://peasyimage.com/developers/).
OpenAPI 3.1.0 spec: [peasyimage.com/api/openapi.json](https://peasyimage.com/api/openapi.json).

## Learn More

- **Tools**: [Image Compress](https://peasyimage.com/image/compress-image/) · [Image Resize](https://peasyimage.com/image/resize-image/) · [Image Convert](https://peasyimage.com/image/convert-image/) · [All Tools](https://peasyimage.com/)
- **Guides**: [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [All Guides](https://peasyimage.com/guides/)
- **Glossary**: [AVIF](https://peasyimage.com/glossary/avif/) · [Color Grading](https://peasyimage.com/glossary/color-grading-image/) · [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) · [All Terms](https://peasyimage.com/glossary/)
- **Formats**: [PNG](https://peasyimage.com/formats/png/) · [WebP](https://peasyimage.com/formats/webp/) · [All Formats](https://peasyimage.com/formats/)
- **API**: [REST API Docs](https://peasyimage.com/developers/) · [OpenAPI Spec](https://peasyimage.com/api/openapi.json)

## Also Available

| Language | Package | Install |
|----------|---------|---------|
| **Python** | [peasy-image](https://pypi.org/project/peasy-image/) | `pip install "peasy-image[all]"` |
| **TypeScript** | [peasy-image](https://www.npmjs.com/package/peasy-image) | `npm install peasy-image` |
| **Rust** | [peasy-image](https://crates.io/crates/peasy-image) | `cargo add peasy-image` |
| **Ruby** | [peasy-image](https://rubygems.org/gems/peasy-image) | `gem install peasy-image` |

## Peasy Developer Tools

Part of the [Peasy Tools](https://peasytools.com) open-source developer ecosystem.

| Package | PyPI | npm | Go | Description |
|---------|------|-----|----|-------------|
| peasy-pdf | [PyPI](https://pypi.org/project/peasy-pdf/) | [npm](https://www.npmjs.com/package/peasy-pdf) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-pdf-go) | PDF merge, split, rotate, compress — [peasypdf.com](https://peasypdf.com) |
| **peasy-image** | [PyPI](https://pypi.org/project/peasy-image/) | [npm](https://www.npmjs.com/package/peasy-image) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-image-go) | **Image resize, crop, convert, compress — [peasyimage.com](https://peasyimage.com)** |
| peasy-audio | [PyPI](https://pypi.org/project/peasy-audio/) | [npm](https://www.npmjs.com/package/peasy-audio) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-audio-go) | Audio trim, merge, convert, normalize — [peasyaudio.com](https://peasyaudio.com) |
| peasy-video | [PyPI](https://pypi.org/project/peasy-video/) | [npm](https://www.npmjs.com/package/peasy-video) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-video-go) | Video trim, resize, thumbnails, GIF — [peasyvideo.com](https://peasyvideo.com) |
| peasy-css | [PyPI](https://pypi.org/project/peasy-css/) | [npm](https://www.npmjs.com/package/peasy-css) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-css-go) | CSS minify, format, analyze — [peasycss.com](https://peasycss.com) |
| peasy-compress | [PyPI](https://pypi.org/project/peasy-compress/) | [npm](https://www.npmjs.com/package/peasy-compress) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-compress-go) | ZIP, TAR, gzip compression — [peasytools.com](https://peasytools.com) |
| peasy-document | [PyPI](https://pypi.org/project/peasy-document/) | [npm](https://www.npmjs.com/package/peasy-document) | [Go](https://pkg.go.dev/github.com/peasytools/peasy-document-go) | Markdown, HTML, CSV, JSON conversion — [peasyformats.com](https://peasyformats.com) |
| peasytext | [PyPI](https://pypi.org/project/peasytext/) | [npm](https://www.npmjs.com/package/peasytext) | [Go](https://pkg.go.dev/github.com/peasytools/peasytext-go) | Text case conversion, slugify, word count — [peasytext.com](https://peasytext.com) |

## License

MIT
