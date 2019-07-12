---
title: "Upload large file task"
description: "Describes how you can upload large files to OneDrive."
localization_priority: Normal
author: sdk-contributors
---

# Upload large file task

This task simplifies the implementation of the OneDrive [resumable upload](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/driveitem_createuploadsession) API.

## Uploading a large file

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

**Uploading from the browser**

HTML to select the file for uploading.

```HTML
<input id="fileUpload" type="file" onchange="fileUpload(this)" />
```

Get files from the input element and start uploading.

```typescript
async function fileUpload(elem) {
	let file = elem.files[0];
	try {
		let response = await largeFileUpload(client, file, file.name);
		console.log(response);
		console.log("File Uploaded Successfully.!!");
	} catch (error) {
		console.error(error);
	}
}

async function largeFileUpload(client, file) {
	try {
		let options = {
			path: "/Documents",
			fileName: file.name,
			rangeSize: 1024 * 1024,
		};
		const uploadTask = await MicrosoftGraph.OneDriveLargeFileUploadTask.create(client, file, options);
		const response = await uploadTask.upload();
		return response;
	} catch (err) {
		throw err;
	}
}
```

**Uploading from NodeJS**

```typescript
function uploadFile() {
	fs.readFile("<PATH_OF_THE_FILE>", {}, function(err, file) {
		if (err) {
			throw err;
		}
		let fileName = "<NAME_OF_THE_FILE_WITH_EXTN>";
		oneDriveLargeFileUpload(client, file, fileName)
			.then((response) => {
				console.log(response);
				console.log("File Uploaded Successfully.!!");
			})
			.catch((error) => {
				throw err;
			});
	});
}

async function oneDriveLargeFileUpload(client, file, fileName) {
	try {
		let options = {
			path: "/Documents",
			fileName,
			rangeSize: 1024 * 1024,
		};
		const uploadTask = await OneDriveLargeFileUploadTask.create(client, file, options);
		const response = await uploadTask.upload();
		return response;
	} catch (err) {
		console.log(err);
	}
}
```

---

## Resume an upload

You can resume interrupted uploads.

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

```typescript
uploadTask.resume();
```

---

## Control the upload process

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

You can alter the upload slice.

```typescript
let range = uploadTask.getNextRange();
let slicedFile = uploadTask.sliceFile(range);
uploadTask.uploadSlice(slicedFile, range, uploadTask.file.size);
```

---

## Upload with custom options

# [C#](#tab/CS)

<!-- TODO -->

```csharp

```

# [Javascript](#tab/Javascript)

```typescript
async function largeFileUpload(client, file) {
	const filename = file.name;
	const driveId = "<YOUR_DRIVE_ID>";
	const path = "<LOCATION_TO_STORE_FILE>";
	try {
		const requestUrl = `/drives/${driveId}/root:${path}/${fileName}:/createUploadSession`;
		const payload = {
			item: {
				"@microsoft.graph.conflictBehavior": "fail",
				name: fileName,
			},
		};
		const fileObject = {
			size: file.size,
			content: file,
			name: fileName,
		};
		const uploadSession = await LargeFileUploadTask.createUploadSession(client, requestUrl, payload);
		const uploadTask = await new LargeFileUploadTask(client, fileObject, uploadSession);
		const response = await uploadTask.upload();
		return response;
	} catch (err) {
		throw err;
	}
}
```

---
