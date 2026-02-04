# Blog Generator

A fast, type-safe static blog generator written in Rust.

## Features

- **Multi-threaded processing** via Rayon
- **Automatic image optimization** to WebP with caching
- **XSS prevention** via validated newtypes (`Tag`, `HtmlSafe`)
- **CSS inlining** to eliminate render-blocking
- **LCP preloading** for first images
- **Semantic error handling** with recovery strategies

## Usage

```bash
cd generator
cargo run --release
```

## Project Structure

```
src/
├── main.rs      # Build orchestration
├── lib.rs       # Module exports
├── config.rs    # Typed configuration
├── error.rs     # BuildError enum
├── image.rs     # WebP optimization + cache
├── parser.rs    # Markdown → HTML
├── renderer.rs  # HTML templates
└── types/
    ├── tag.rs       # Validated tag
    └── html_safe.rs # XSS-safe wrapper
```

## Configuration

```rust
let config = Config::new()
    .content_dir("../content")
    .public_dir("../public")
    .max_image_width(1200)
    .timezone_offset(8)      // GMT+8
    .brand_name("CODE A DUCK")
    .inline_css(true);       // Eliminates render-blocking
```

## Build Pipeline

```
Phase 1 (IO)     → Discover .md files
Phase 2 (CPU/∥)  → Parse metadata + extract tags
Phase 3 (CPU/∥)  → Render HTML + optimize images
Phase 4 (IO)     → Write index + tag pages
```

## Error Handling

| Error Type | Recovery |
|------------|----------|
| `ParseFailed` | Skip file, continue |
| `ImageOptFailed` | Use original image |
| `OutputNotWritable` | Abort build |
| `NoValidPosts` | Abort build |

## Dependencies

- `pulldown-cmark` - Markdown parsing
- `chrono` - Date handling
- `image` - Image processing
- `rayon` - Parallelism
- `thiserror` - Error types

## License

MIT
