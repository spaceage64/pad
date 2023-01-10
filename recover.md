# Recovering Power Automate Desktop flows from OneDrive
## Quick guide on recovering PAD files that got corrupted by OneDrive

Power Automate Desktop stores any flows on OneDrive. Unfortunately, saving flows may occassionally lead to OneDrive corrupting your files, leaving them unusable in PAD. In fact, the flows will disappear entiely from your overview. Not so great if you've put many hours into perfecting a flow.

Luckily, the now inaccessible files should still be accessible one OneDrive and may thus be recovered. Below I have written some quick instructions that should enable to recover your corrupted flows.

When creating flows in Power Automate Desktop, the resulting files are stored in the associated OneDrive account under the path Apps\Power Automate Desktop For Windows.
Each flow is stored in two files, both using the same UUID for a name:
* A tiny .meta file, containing some metadata
* A larger file, without a file extension, containing the actual flow code

Each time a flow is saved, a new Ctag is generated and added to both files. The issue of coruption occurs when PAD is only able to change the Ctag for the larger file and not the metadata. In such an instance, a mismatch will occur between both files and PAD will consider the flow corrupted.

To resolve this problem, go through the following steps and it might just save you many hours of having to recreate your whole flow (I know it did so for me).

### Instructions
1. Open your flow in Power Automate Desktop
1. For all your subflows, select all actions (Ctrl + A), copy (Ctrl + C), and paste in Notepad++[^1] (Ctrl + V)
1. Delete all actions from the subflows so that they are all empty (don't remove the subflows themselves)
1. Open the UI elements pane on the right, then click ⋮ Menu > Remove unused UI elements
1. Save a copy of your empty flow via File > Save as...
1. Close the flow editor (just to be sure caching will not cause issues)
1. Reopen your saved copy
1. For each subflow, run the following regex find and replace commands (Ctrl + H, set search mode to regular expression) in Notepad++
    * Regex find `\\"ScreenShot\\": \\"(.*?)",\\r\\n(\s+)\\"ElementTypeName\\":`  
    Replace with `\\"ScreenShot\\": null,\\r\\n($2)\\"ElementTypeName\\":`
    * Regex find `\\"ScreenshotPath\\": \\"(.*?)\\"\\r\\n`  
    Replace with `\\"ScreenshotPath\\": null\\r\\n`
1. For all subflows, select all code (Ctrl + A), copy (Ctrl + C), and paste in the Power Automate flow editor (Ctrl + V)
1. Save your flow

You should now find that the flow editor no longer displays UI images, and that your flow files should be significantly smaller. For me, this resulted in my file being reduced in size from 8.36 MB to merely 188 KB.

[^1]: Theoretically, you should be able to use any text editor with regex support. Nevertheless, when using VSCode, Power Automate Desktop would not accept the resulting code, and I'm not yet sure what was causing this issue. Notepad++ should work in any case.
