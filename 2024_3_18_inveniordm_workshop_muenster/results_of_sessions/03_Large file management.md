# Session report


## Session title: Large file management

**Time slot:** 
Tuesday, 10.30 - ??

**Room:** 
R19/20

**Host:**
Tom Morrell (Caltech)

**Participants:**
* Max Moser (TU Wien)
* Nicola Tarocco (CERN)
* Panna Liptak (CERN)
* Mirek Simek (CESNET)
* Camelia Ignat (JRC)
* Alberto Ortiz Flores (NYU) (aortiz@nyu.edu)
* Sefakor Ankora (WACREN)
* David Pape (HZDR)
* Philipp Gualdi(TU Graz)
* [Werner Greßhoff (University of Münster)](werner.gresshoff@uni-muenster.de)
* Michael Groh (Bamberg)
* Mirek Bauer (CESNET)
* Javier Romero (CERN)
* Manuel Costa (CERN)
* [Kai Wörner (UHH)](kai.woerner@uni-hamburg.de)
* Sotiris Tsepelakis (TU Wien)
* Dylan Bollaro ([Ulysseus](ulysseus.eu))
* Pearl Go (Northwestern University)
* Jan-Olof Wiefel (University of Münster)

### Points we discussed:
- Use cases: 
    - **Caltech use case**: for the moment using links to S3 storage where the large files are uploaded; 
        - it would be need an API to link taken into account the versioning
        - Workflow User deposits metadata in invenio and large files to S3 then an *admin* does the linking between metadata and data
    - **Mirek's usecase**: the upload is done via Invenio, the user provide the files in chuncks, multipart upload, permission managed by Invenio
        - solution needed: 
    - **Uni Münster usecase**: extend the CESNET uecase by providing the download of a (bash?) script to add the mssing data to the prepared draft document
    - **UHH Usecase**: Need a background uploader that takes metadata and a list of (reachable) files and creates a record and uploads the files in the background. Had this as a Invenio module for the zenodo code that relied on files being on actally mounted volumes (not ideal). Something like this could work together with background uploaders that connect to cloud services like Nexcloud/Dropbox etc.
    - **JRC usecase**: manage large files, big number of files, hierachical directory structure. dataset processing
    - Storage by community
        - current implementation where a record could be moved between communities make it difficult
- Needs:
    - multipart uploader
    - background uploader (command line or server connection configurable in user profile)
    - uploading large number of files
    - keep the directory structure
- UI and storage:
    - select location during upload
    - picker for file linking and upload
    - credential per user , per storage (i.e files not public yet)
- File meta-data: description, types, title (UI concerns)
     

- Mirek B: possible file uploader UI & logic: 
    - https://uppy.io/examples/
    - https://github.com/oarepo/file-management-dialog (still kind of crude implementation, but supports extra file metadata input)
    - https://64e5facb8f19c102adc64ebe-wvcchagfvz.chromatic.com/?path=/docs/file-management-dialog-filemanagementdialog--docs

    - s3 multipart upload plugin: https://github.com/oarepo/file-management-dialog/blob/main/src/utils/uppy-plugins/oarepo-upload/index.js

- Multipart uploader:
    - Data integrity checks for upload and download
    - Is UI sufficient?

### Actions we decided to take:
- **Multipart uploader**
    - [Issue 2612](https://github.com/inveniosoftware/invenio-app-rdm/issues/2612)
- **File linking API**
    - [Issue 2613](https://github.com/inveniosoftware/invenio-app-rdm/issues/2613)
- **File metadata**
    - [Issue 2614](https://github.com/inveniosoftware/invenio-app-rdm/issues/2614)


## Wednesday Afternoon Session

### Multipart uploads - API proposal

github.com/oarepo/oarepo-s3/blob/master/proposal.md

On the API level, the upload process has the same phases
as the current local file upload: initiation, upload, commit.

#### Initiation phase

During the initiation phase, user specifies an "M" transfer
type together with the number of parts that will be sent.

```json5
// POST /api/records/<id>/draft/files
[{
  "key": "dataset.zip",
  "storage_class": "M",
  "parts": 20
}]
```

The server will respond with a standard response with `links` section enriched with "part" links.

```
{
  "entries": [{
    ... standard invenio response
    "status": "pending",
    "links": {
      "self": ".../files/dataset.zip",
      "parts": [
         {
             "part": 1,
             "url": "https://s3.cesnet.cz/<presigned>",
             "expiration": "<utc datetime iso format>"
         },...
      ],
      "commit": ".../files/dataset.zip/commit"
    }
  }]
}
```

#### Upload phase

The client (commandline or UI) splits the file into the chunks and uses the `parts` url to upload the parts.

If upload of the part fails, the user can retry the upload multiple times.

The chunks might be uploaded in any order, sequentially or in parallel.

#### Commit phase

The client calls "commit" after all the parts have been uploaded.


#### Deleting partially uploaded file

User can abort the upload and delete the partially uploaded
file from invenio. Backend is responsible for freeing all resources on the upload backend connected with the upload (in case of S3, call `abort`)

#### Getting upload progress

TODO: need to discuss this and make sure it behaves consistently with different transfer types (backend fetch as well as multipart upload)

### Backend proposal

The `TransferType` will be made extensible using a "registry" pattern that is already present in different parts of invenio. A new transfer type, MultipartTransferType ('M'), will be created to handle multipart uploads.

Changes needed:

* Remove the "enum" part of TransferType
* Create TransferRegistry class (register and get_transfer methods, similar to service registry), instatiate inside records_resources extension add create a  `current_transfer_registry` proxy
* Change Transfer class to use the registry
* Create a `M` transfer type
* Create a `MultipartUploadMixin` and implement it in S3FSFileStorage
* Add `MultipartUploadLinks` to the links section of the file record

Optional: hide the `content` link until the upload has finished

Optional: implement the MultipartUploadMixin in File storage as well (no usecase for that now, but might be done for consistency)

### Minutes

Two possible implementations - Just S3 or for all file storage types? The S3 only component implementation requires less core changes but is less extensible. Transfer type would be more general and can put information in the file data field. 

Multipart uploads are focused on API uploads. Upbay is a js uploader that can do multipart but it pretty complicated. Ideally this would be configurable

storage_class `R` may just work for linking.

We also discussed UI possibilities for the uploader. Ideally the uploader would show progress even for multipart jobs.


### Flipcharts



![](https://radosgw.public.os.wwu.de/pad/uploads/3a13a2e3-4182-4275-b2b1-560c7b2f2d96.jpg)
![](https://radosgw.public.os.wwu.de/pad/uploads/5baeff03-b702-4e7b-a98b-7212a85cc859.jpg)
![](https://radosgw.public.os.wwu.de/pad/uploads/fc215b7c-4668-4e41-b5b6-3ea370a35462.jpg)
![](https://radosgw.public.os.wwu.de/pad/uploads/0d831244-61cc-4332-bdb6-1ca42bed05d2.jpg)

