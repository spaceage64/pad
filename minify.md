# Minifying Power Automate Desktop flows
## Quick guide on removing embedded images from PAD files

When creating flows in Power Automate Desktop, the resulting files are stored in the associated OneDrive account under the path Apps\Power Automate Desktop For Windows.
Each flow is stored in two files, both using the same UUID for a name:
* A tiny .meta file, containing some metadata
* A larger file, without a file extension, containing the actual flow code

You may have noticed that flow file sizes can grow rapidly, especially when one uses a lot of UI elements. The reason for this behavior is that PAD stores png images for all UI elements in use (or leftovers that were previously used). The code itself barely takes up any space at all. Because flows always have to be downloaded from OneDrive before running or editing them, large file sizes can become quite inconvenient, slowing down your work.

To resolve this problem, I have written some regular expressions to easily identify the images embedded in the flow code, replacing them with null values. The resulting code will be significantly smaller and work just as well, only the UI images will now be mising from the flow editor.

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
