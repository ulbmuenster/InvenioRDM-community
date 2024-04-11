# Session Report
## Session title: Digital Preservation

**Time slot:**
Wednesday, 9.30 - ??

**Room:**
R18

**Host:**
Maximilian Moser,
Panna Liptak

**Participants:**
- David Eckhard
- Anika Churilova
- Javier Romero
- Manuel Costa
- Hagar Lowenthal
- Sotiris Tsepelakis
- Hrafn Malmquist
- Martin Obersteiner

### Problem statement

Digital preservation is a requirement for Core Trust Seal
[Presentation](https://docs.google.com/presentation/d/1Wq8HST4uq1L6MWRfiiDC7PVbpeaV4JRflKkws4QBYU8/edit?usp=sharing)

### Ideas shared

Comparison of requirements

Discussion of existing (pieces of) solutions

Rough architecture outline:
* Middleman (like at CERN)
* Direct connection between Invenio and Archivematica

### Actions decided

Brush up Invenio-SIPStore (Max)
Check out Invenio-Archivematica, or look at the AM Python client (Max)
Check if an AM plugin for Invenio is needed (Panna?)

### Notes

```
assumption: no external files (only archive what you actually *have*)

notify users if there's problems with the AM workflow?
    it seems like there's no method for hooking into AM right now (but it's based on Django, so it should be extensible)

automatic creation of AIP on publish (?)
    could be done via `Invenio-SIPStore`
    there's a Python client for AM which could be used (over Invenio-Archivematica, which is an old module)
    actually it could be sufficient, if it's an optional feature (e.g. through a user request)
        but *always* doing it would give us that sweet DP metadata

preservation of deleted content?
    we can delete AIPs if the records are purged

should metadata be preserved as well?
    metadata edits: trigger a new AIP?
        change metadata in old AIP -> recalculate AIP md5
        exclude metadata?

    CERN cares about that
    TUW doesn't care about that
        if every metadata edit triggers a new SIP creation, we could simply ignore the new SIP in archivematica
        (should be configurable in Archivematica)

@ CERN:
    "BagIt Create": tool/Python package for creating AM SIPs from records
    Dark archive: access to AIPs on demand
    mostly just preservation of CERN products across repositories, not externals
        everything for CDS, but not everything in Zenodo/Indico/...
        dedicated staff role for determining what is worthy of preservation

    other things to preserve: Git, CodiMD

    Preservation of really old (e.g. physical) documents
        normalize by digitizing and uploading to a repository (e.g. CDS @ CERN)

NOTE: EU project in the making for standardizing archival for repositories (Archivematica?)
    no details were known at the meeting though
```

![](https://radosgw.public.os.wwu.de/pad/uploads/7a54982b-cafe-458d-a81f-74a974698105.png)