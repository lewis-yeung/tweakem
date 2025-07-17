<div align="center">

# Tweak things in PowerShell <!-- omit in toc -->

</div>

## - CONTENTS - <!-- omit in toc -->

- [Keep each command item unique in PSReadLine history file](#keep-each-command-item-unique-in-psreadline-history-file)

## Keep each command item unique in PSReadLine history file

<details>

> [!note]
> This tweak has been tested with **PowerShell v7.3.6** and **[PSReadLine][psreadline] v2.3.1-beta1**. It should also work with later versions but **NOT WITH OLDER ONES** (can even cause loss of command history).

Steps:

1. Run the following command to update PSReadLine in PowerShell. (**VERY IMPORTANT!**)

   ```powershell
   Update-Module PSReadLine -AllowPrerelease
   ```

2. Add the following code to your PowerShell [profile][powershell-profile]:

   ```powershell
   Set-PSReadLineOption -AddToHistoryHandler {
     # A hack to keep each item unique in the history file.
     param([string]$itemNew)
     $historyFile = (Get-PSReadLineOption).HistorySavePath
     if (!(Test-Path $historyFile)) {
       return $true
     }
     $items = [ordered]@{}
     $sb = [System.Text.StringBuilder]::new()
     Get-Content $historyFile | ForEach-Object {
       if ($_.Length -eq 0) {
         return
       }
       [void]$sb.Append($_)
       if ($_.EndsWith('`')) {
         [void]$sb.Append("`n")
         return
       }
       $item = $sb.ToString()
       if ($items.Contains($item)) {
         $items.Remove($item)
       }
       $items.Add($item, $null)
       [void]$sb.Clear()
     }
     if ($items.Contains($itemNew)) {
       $items.Remove($itemNew)
     }
     $items.Keys | Out-File $historyFile -Force
     return $true
   }
   ```

3. Restart PowerShell.

</details>

[psreadline]: https://github.com/PowerShell/PSReadLine
[powershell-profile]: https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_profiles
