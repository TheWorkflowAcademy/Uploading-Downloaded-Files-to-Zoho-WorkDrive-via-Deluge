# Uploading-downloaded-files-to-Zoho-WorkDrive-via-Deluge
A Deluge script for uploading downloaded files to Zoho WorkDrive via Deluge

## Core Idea
The [zoho.workdrive.uploadFile](https://www.zoho.com/deluge/help/workdrive/upload-file.html) Deluge integration task lets you upload any files to Zoho WorkDrive but one of its mandatory parameters is <file_name>. For a successful upload, WorkDrive demands the full file name including the extension (eg; Picture.jpg/Contract.pdf). Without the extension, the uploaded file will be corrupted and cannot be opened/viewed in WorkDrive. Thankfully, Zoho has a function called [getFileName()](https://www.zoho.com/deluge/help/file/get-file-name.html).

## Configuration
* Zoho WorkDrive Connection needed:
  * WorkDrive.files.ALL
### Tutorial

#### 1. Download the File
The first step here is to download the file you need in Deluge. It can be from CRM, WorkDrive or wherever. In the example below, we are downloading from WorkDrive.
```javascript
fileid = "xxxxxxxxxxxxxxxxxxxxxxxxx";
mp = Map();
mp.put("Accept","application/vnd.api+json");
getFile = invokeurl
[
	url :"https://workdrive.zoho.com/api/v1/download/" + fileid
	type :GET
	headers:mp
	connection:"zohoworkdrive_connection"
];
info getFile;
```
*Note: Replace "fileid" and "zohoworkdrive_connection" accordingly*

#### 2. Get the File Name
When you get `info` on the downloaded file, it shows you the file name but the variable actually contains the entire file itself, not just the name so you can't plug the variable into the <file_name> parameter.

In order to get the file name, use the `getFileName()` function - this returns you the plain text (string) value of the file name.

```javascript
fileName = getFile.getFileName();
info fileName;
```

#### Optional: Rename the File
If you wish to rename the file, this will be helpful to you. Otherwise, you can skip to [3.](#3-upload-file-to-zoho-workdrive)

To rename the file, you need to get the file extension. Since you already have the file name, all you need is some basic string manipulation to get the extension.

```javascript
fileExt = fileName.right(fileName.len() - fileName.lastIndexOf(".") - 1);
info fileExt;
```

#### 3. Upload the File to Zoho WorkDrive

If you're not planning to rename the file, 
```javascript
folderid = "xxxxxxxxxxxxxxxxxxxxxxxxx";
uploadFile = zoho.workdrive.uploadFile(getFile, folderid, fileName, false, "zohoworkdrive_connection");
info uploadFile;
```

If you're renaming the file,
```javascript
folderid = "xxxxxxxxxxxxxxxxxxxxxxxxx";
uploadFile = zoho.workdrive.uploadFile(getFile, folderid, "New File Name" + fileExt, false, "zohoworkdrive_connection");
info uploadFile;
```

*Note: Replace "folderid" and "zohoworkdrive_connection" accordingly*
