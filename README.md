# peasy-image-go

[![Go Reference](https://pkg.go.dev/badge/github.com/peasytools/peasy-image-go.svg)](https://pkg.go.dev/github.com/peasytools/peasy-image-go)
[![Go Report Card](https://goreportcard.com/badge/github.com/peasytools/peasy-image-go)](https://goreportcard.com/report/github.com/peasytools/peasy-image-go)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/MIT)

Go client for the [PeasyImage](https://peasyimage.com) API — compress, resize, convert, and crop images. Built with `net/http`, `encoding/json`, and zero external dependencies.

Built from [PeasyImage](https://peasyimage.com), a comprehensive image processing toolkit offering free online tools for compressing, resizing, converting, and cropping images across all major formats. The site includes in-depth guides on image optimization for the web, format comparisons between WebP, AVIF, PNG, and JPEG, plus a glossary covering concepts from color spaces to alpha channels to EXIF metadata.

> **Try the interactive tools at [peasyimage.com](https://peasyimage.com)** — [Compress Image](https://peasyimage.com/image/compress-image/), [Resize Image](https://peasyimage.com/image/resize-image/), [Convert Image](https://peasyimage.com/image/convert-image/), [Crop Image](https://peasyimage.com/image/crop-image/), and more.

<p align="center">
  <img src="demo.gif" alt="peasy-image-go demo — image compress, resize, and convert tools in Go terminal" width="800">
</p>

## Table of Contents

- [Install](#install)
- [Quick Start](#quick-start)
- [What You Can Do](#what-you-can-do)
  - [Image Processing Operations](#image-processing-operations)
  - [Browse Reference Content](#browse-reference-content)
  - [Search and Discovery](#search-and-discovery)
- [API Client](#api-client)
  - [Available Methods](#available-methods)
- [Learn More About Image Tools](#learn-more-about-image-tools)
- [Also Available](#also-available)
- [Peasy Developer Tools](#peasy-developer-tools)
- [License](#license)

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

## What You Can Do

### Image Processing Operations

Digital images are stored in dozens of formats, each optimized for different use cases. JPEG uses lossy compression ideal for photographs, PNG supports lossless compression with alpha channel transparency, WebP (developed by Google) offers both lossy and lossless modes at 25-34% smaller file sizes than JPEG, and AVIF (based on the AV1 video codec) pushes compression efficiency even further. PeasyImage provides tools to compress, resize, convert, and crop images across all these formats.

| Operation | Slug | Description |
|-----------|------|-------------|
| Compress Image | `compress-image` | Reduce file size while preserving visual quality |
| Resize Image | `resize-image` | Scale dimensions with aspect ratio preservation |
| Convert Image | `convert-image` | Transform between PNG, JPEG, WebP, AVIF, and more |
| Crop Image | `crop-image` | Extract rectangular regions from images |

```go
// Retrieve the image compression tool and inspect its capabilities
client := peasyimage.New()
ctx := context.Background()

// Get detailed information about the compress tool
tool, err := client.GetTool(ctx, "compress-image")
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Tool: %s\n", tool.Name)         // Image compression tool name
fmt.Printf("Description: %s\n", tool.Description) // How compression works

// List all available image tools with pagination
tools, err := client.ListTools(ctx, &peasyimage.ListOptions{Page: 1, Limit: 20})
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Total image tools available: %d\n", tools.Count)
```

Learn more: [Compress Image Tool](https://peasyimage.com/image/compress-image/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/)

### Browse Reference Content

PeasyImage includes a detailed glossary of image processing and digital media terminology, plus educational guides on format selection and optimization workflows. The glossary covers concepts like WebP (Google's modern image format with superior compression), EXIF (Exchangeable Image File Format metadata embedded by cameras), lossy vs. lossless compression trade-offs, color spaces (sRGB, Adobe RGB, Display P3), and alpha channels for transparency support.

| Term | Description |
|------|-------------|
| [WebP](https://peasyimage.com/glossary/webp/) | Google's modern image format — lossy and lossless modes |
| [EXIF](https://peasyimage.com/glossary/exif/) | Exchangeable Image File Format — camera metadata standard |
| [Lossy Compression](https://peasyimage.com/glossary/lossy-compression/) | Compression that discards data to achieve smaller files |
| [Color Space](https://peasyimage.com/glossary/color-space/) | Mathematical model defining the range of representable colors |
| [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) | Transparency layer in PNG, WebP, and AVIF images |

```go
// Browse the image glossary for digital media terminology
glossary, err := client.ListGlossary(ctx, &peasyimage.ListOptions{
	Search: str("webp"), // Search for modern image format concepts
})
if err != nil {
	log.Fatal(err)
}
for _, term := range glossary.Results {
	fmt.Printf("%s: %s\n", term.Term, term.Definition)
}

// Read a guide on choosing the right image format
guide, err := client.GetGuide(ctx, "image-format-comparison")
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Guide: %s (Level: %s)\n", guide.Title, guide.AudienceLevel)
```

Learn more: [Image Glossary](https://peasyimage.com/glossary/) · [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/)

### Search and Discovery

The API supports full-text search across all content types — tools, glossary terms, guides, use cases, and format documentation. Search results are grouped by content type, making it easy to find the right tool or reference for any image processing workflow.

```go
// Search across all image content — tools, glossary, guides, and formats
results, err := client.Search(ctx, "convert webp", nil)
if err != nil {
	log.Fatal(err)
}
fmt.Printf("Found %d tools, %d glossary terms, %d guides\n",
	len(results.Results.Tools),
	len(results.Results.Glossary),
	len(results.Results.Guides),
)

// Discover format conversion paths — what can PNG convert to?
conversions, err := client.ListConversions(ctx, &peasyimage.ListConversionsOptions{
	Source: str("png"), // Find all formats PNG can be converted to
})
if err != nil {
	log.Fatal(err)
}
for _, c := range conversions.Results {
	fmt.Printf("%s → %s\n", c.SourceFormat, c.TargetFormat)
}
```

Learn more: [REST API Docs](https://peasyimage.com/developers/) · [All Image Tools](https://peasyimage.com/)

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

## Learn More About Image Tools

- **Tools**: [Compress Image](https://peasyimage.com/image/compress-image/) · [Resize Image](https://peasyimage.com/image/resize-image/) · [Convert Image](https://peasyimage.com/image/convert-image/) · [Crop Image](https://peasyimage.com/image/crop-image/) · [All Tools](https://peasyimage.com/)
- **Guides**: [Image Format Comparison](https://peasyimage.com/guides/image-format-comparison/) · [How to Compress Images for Web](https://peasyimage.com/guides/how-to-compress-images-for-web/) · [All Guides](https://peasyimage.com/guides/)
- **Glossary**: [WebP](https://peasyimage.com/glossary/webp/) · [EXIF](https://peasyimage.com/glossary/exif/) · [Lossy Compression](https://peasyimage.com/glossary/lossy-compression/) · [Color Space](https://peasyimage.com/glossary/color-space/) · [Alpha Channel](https://peasyimage.com/glossary/alpha-channel/) · [All Terms](https://peasyimage.com/glossary/)
- **Formats**: [All Formats](https://peasyimage.com/formats/)
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
