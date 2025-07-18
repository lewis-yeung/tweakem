<div align="center">

# Tweak things in Windows <!-- omit in toc -->

</div>

## - CONTENTS - <!-- omit in toc -->

- [Replace the default emoji font in Windows 11](#replace-the-default-emoji-font-in-windows-11)

## Replace the default emoji font in Windows 11

> [!note]
> This tweak has been tested on Windows 11 23H2 (22631.3737) with [**Segoe UI Emoji**][segoe-ui-emoji] font **v1.51** (the default system emoji font) and [**Twemoji font**][twemoji] in [**glyf**][glyf] [**COLR**][colr] **v0** format (can be [downloaded from GitHub releases][twemoji-glyf-colr0-ttf]). It should also work on higher versions.

<details><summary>Steps:</summary>

1. Make sure that you have properly installed [**Python**][python-downloads] 3.9 or higher.

2. Make sure that you have properly installed [**fontTools**][fonttools]. If not, you can install it via [pip][pypi-fonttools], [uv][uv], [pipx][pipx], etc. Examples:

   - Install via pip:

   ```
   pip install fonttools
   ```

   - Install via uv:

   ```
   uv tool install fonttools
   ```

   - Install via pipx:

   ```
   pipx install fonttools
   ```

   After the installation, you can run the following command to make sure that the executable `ttx.exe` is in your `PATH`:

   ```
   where.exe ttx.exe
   ```

   The command should output the full path of `ttx.exe`, otherwise the fontTools may not have been installed correctly.

3. Prepare a TTF file of emoji font in COLR format. For example, you can download [Twemoji font in glyf COLR v0 format][twemoji-glyf-colr0-ttf] from the nightly release of [this GitHub repo][twemoji-colr-font].

4. Start a **PowerShell** session **AS ADMINISTRATOR** and follow the sub-steps (run commands one by one):
   1) Set the current working location to the temporary directory:

      ```powershell
      Set-Location $env:Temp
      ```

   2) Dump the `name` table from the original TTF file of Segoe UI Emoji font to a temporary file `name.ttx`:

      ```powershell
      ttx.exe -t name -o name.ttx "$env:SystemRoot\Fonts\seguiemj.ttf"
      ```

   3) Compile a new Segoe UI Emoji font:

      ```powershell
      ttx.exe -m "<path-to-new-emoji-ttf>" -o "<path-to-output-ttf>" name.ttx
      ```

      where `<path-to-output-ttf>` represents the full path to the newly compiled Segoe UI Emoji font (TTF file), and `<path-to-new-emoji-ttf>` represents the full path to the emoji font (TTF file) that you have prepared.

   4) Delete the temporary file `name.ttx`:

      ```powershell
      Remove-Item name.ttx
      ```

   5) Copy the newly compiled Segoe UI Emoji font file to the system font directory:

      ```powershell
      Copy-Item "<path-to-output-ttf>" "$env:SystemRoot\Fonts\_seguiemj.ttf"
      ```

   6) Modify the registry value:

      ```powershell
      reg.exe add 'HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Fonts' /v 'Segoe UI Emoji (TrueType)' /d '_seguiemj.ttf' /f
      ```

5. Now the new emoji font should take effect immediately in newly started programs. If not, just log out and log back in the system.

</details>

[segoe-ui-emoji]: https://learn.microsoft.com/en-us/typography/font-list/segoe-ui-emoji
[twemoji]: https://github.com/jdecked/twemoji
[glyf]: https://learn.microsoft.com/en-us/typography/opentype/spec/glyf
[colr]: https://learn.microsoft.com/en-us/typography/opentype/spec/colr
[twemoji-colr-font]: https://github.com/Nerixyz/twemoji-colr-font
[twemoji-glyf-colr0-ttf]: https://github.com/Nerixyz/twemoji-colr-font/releases/download/nightly-build/Twemoji_GlyfColr0.ttf
[python-downloads]: https://www.python.org/downloads/
[fonttools]: https://fonttools.readthedocs.io/
[pypi-fonttools]: https://pypi.org/project/fonttools/
[uv]: https://docs.astral.sh/uv/
[pipx]: https://pipx.pypa.io/
