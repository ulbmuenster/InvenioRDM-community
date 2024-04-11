# Session Report
## Session title: Customising Schema

**Time slot:**
Tuesday, 13.15 - ??

**Room:**
R18

**Host:**
Steve [CL] steve@cottagelabs.com,
Hrafn [CL] hrafn@cottagelabs.com

**Participants:**
- Steve
- Hrafn
- Hagar Lowenthal (JRC)
 -2 x Mirek
- ...

This was a knowledge sharing session to explore approaches to answer the question 'can InvenioRDM support our domain specific schema?'

### Points we discussed:
- Supplying multiple metdatata schema in RDM is hard / unnecessary / already decided against. OARepo (and others) have done it as an Invenio Framework customisation orthogonal to RDM.
- Modifying the Marshmallow validation in RDM is _possible_ but not recommended.
- Custom fields gets us most of the way there but we (community) require a bit more understanding, particularly those hopping LTS releases (v9-v12).
- There's a v12 feature from Zenodo that will enable a library of available custom fields to add at submission time (via ?)
- This feature supports hiding & reordering fields, but not specifying additional required fields on the core DataCite schema.
- Make RDM templates more agnostic to the core schema (for field in record, render field)

### Actions we decided to take:
- Write a feature request for overlay config for required fields per instance / community.
- Improve docs on Custom Fields - checklist on when they're useful, community examples.
- Case studies / knowledge sharing of the 'hard way' customisations that have been achieved.

![](https://radosgw.public.os.wwu.de/pad/uploads/a3bd3599-93ab-47ce-9091-dcc9c53c8284.jpg)

![](https://radosgw.public.os.wwu.de/pad/uploads/e62cced2-1689-421b-b7d6-b96501161d48.jpg)

![](https://radosgw.public.os.wwu.de/pad/uploads/dc4c5e64-9f73-4c43-91ad-edff86d7ae0b.jpg)

