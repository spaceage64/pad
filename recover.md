# Recovering Power Automate Desktop flows from OneDrive
## Quick guide on recovering PAD files that got corrupted by OneDrive

Power Automate Desktop stores any flows on OneDrive. Unfortunately, saving flows may occasionally  lead to OneDrive corrupting your files, leaving them unusable in PAD. In fact, the flows will disappear entirely from your overview. Not so great if you've put many hours into perfecting a flow.

Luckily, the now inaccessible files should still be accessible one OneDrive and may thus be recovered. Below I have written some quick instructions that should enable you to recover your corrupted flows.

When creating flows in Power Automate Desktop, the resulting files are stored in the associated OneDrive account under the path Apps\Power Automate Desktop For Windows.
Each flow is stored in two files, both using the same UUID for a name:
* A tiny .meta file, containing some metadata
* A larger file, without a file extension, containing the actual flow code

Each time a flow is saved, a new Ctag is generated and added to both files. The issue of corruption occurs when PAD is only able to change the Ctag for the larger file and not the metadata. In such an instance, a mismatch will occur between both files and PAD will consider the flow corrupted.

To resolve this problem, go through the following steps and it might just save you many hours of having to recreate your whole flow (I know it did so for me).

### Instructions
1. Open your OneDrive and go to Apps\Power Automate Desktop For Windows, based on the file sizes and modification dates, try to find out which of the files holds your corrupted flow
1. Take not of the file name of this file (UUID, will look something like 2f985a45-c75e-4add-b5c3-5ba334cd1f49)
1. Download and install [Fiddler Classic](https://www.telerik.com/download/fiddler), a network monitoring tool (a valid email addres is not required)
1. Start Fiddler Classic and navigate to Tools > Options > HTTPS, then check 'Decrypt HTTPS traffic'
1. Open Power Automate Desktop, sign into your account, and open the flows tab
1. In Fiddler, go to File > Capture Traffic if it wasn't already
1. In Power Automate Desktop, click ↻ Refresh to refresh your flows
1. In Fiddler, you should see a call, which you should double click to open
    Host `graph.microsoft.com`  
    URL  `//v1.0/me/drive/special/approot/children`
1. On the right side, choose the Inspectors tab at the top, and JSON in the viewer below
1. Within the JSON, there are a bunch of objects listed under 'value', each indicated with '{}' symbols, each of them holding a name property, like name=2f985a45-c75e-4add-b5c3-5ba334cd1f49
1. Go through the objects until you find the one with the name/UUID of your corrupted file, and within the same object look for a property looking something like cTag=aYzpBNzI1ODQ0NjD2M0FCMDD5ITIzMjQuMjg0, right click and copy-paste it to a temporary location
1. Go back to OneDrive and download the .meta file of your corrupted flow (eg 2f985a45-c75e-4add-b5c3-5ba334cd1f49.meta), open it in a text editor and copy the encoded contents
1. Go to [Base64 Decode](https://www.base64decode.org/) and paste your text, then click the Decode button
1. Some tiny JSON code will now appear, containing also a 'projectCTag', which you can now replace with the Ctag we copied from Fiddler a few steps earlier
1. Once the Ctag has been changed, copy the whole code, go to [Base64 Encode](https://www.base64encode.org/), paste, and click the Encode button
1. Copy the result into Notepad and save it using the same name as the .meta file you downloaded earlier (eg 2f985a45-c75e-4add-b5c3-5ba334cd1f49.meta)[^1]
1. Upload your saved file to OneDrive, replacing the older (corrupted) one
1. Once again, open PAD and click ↻ Refresh to refresh your flows

Now your corrupted flow should once again be visible.

It has personally saved me a lot of work, as my company vpn seems to occasionally be messing with OneDrive, causing the corruptions. Hopefully, this guide will benefit others in the same way!

[^1]: In case you have Windows set to hide file extensions, take extra care to make sure your file name ends with the .meta extension, not .meta.txt. In Notepad, in the Save As dialog, you can do so by selecting All Files in the drop down under the File name input field.
