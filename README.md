# fm

A keyboard-driven terminal file manager built on `fzf`, `eza`, and Kitty's
image protocol. Icons, cached thumbnails, a full-screen gallery grid, a
trash browser with undo history, ripgrep content search, and more.

## Demo

![fm demo](docs/demo.gif)

## Install

    git clone https://github.com/eHab-codeX/fm.git ~/.local/share/fm
    ln -sf ~/.local/share/fm/fm ~/.local/bin/fm

## Requirements

- bash >= 5
- `fzf`, `eza`, `trash-cli`, `ripgrep`
- Optional: `kitten` (Kitty), `bat`, `magick` (ImageMagick), `fd`,
  `glow` or `mdcat`, `pdftoppm`, `ffmpeg`/`ffmpegthumbnailer`

Press `F1` inside `fm` for the full key map.

## License

MIT
