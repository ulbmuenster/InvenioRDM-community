# Session Report 

## Session title: RDM Curation Workflow

**Time slot:**
Wednesday, 10.45 - 12.00

**Room:**
R5/6

**Host:** David Eckhard (TU Graz)

**Participants:**

- David Pape (HZDR)
- Janne Jensen (HS Bremen)

can we crowdsource this section?

### Problem statement

- right now in inveniordm curation can only be done through communities
- that approach somewhat takes away the the community feature, as all records would need to go into one community
- ![](https://radosgw.public.os.wwu.de/pad/uploads/8e7ecc10-b221-4650-a9c7-18a2331e4056.png)


### Ideas shared

- pitched by David: worked on a package for curation requests per record and decoupled from community requests
- discussion about 'Curate' vs 'CurateAndPublish' functionality
- role based 
- possible future requirements: tiered/leveled curation: a) curation of metadata and b) curation of data (useful where domain expertise is available/necessary)
- requirement: make is possible to hide the files to the curators in the curation workflow (make this configurable)
- possibly get rid of the fetch latest request button and replace is with something like "start curation review"
- at best, it would be shown in place of the publish button, better integration with the core will be discussed between Nicola and David
- idea: different statuses 'under review', 'changes requested'. Making/keeping status filterable is desirable from curation perspective

- idea: integration with frictionless package would be handy to automate metadata validation
- idea: invenio is also working on automated validation workflows (?)
- idea: make it possible to ingest that workflow on already published records, e.g. to enhance metadata after publication with new citation info

### Current state of the package
- Will be put on github in the upcoming weeks
- ![](https://radosgw.public.os.wwu.de/pad/uploads/008fd268-0e7f-4717-beec-1113f9a3f2cf.png)
- ![](https://radosgw.public.os.wwu.de/pad/uploads/ea49952f-b487-42b4-9f84-2ff4c05ab405.png)
- ![](https://radosgw.public.os.wwu.de/pad/uploads/32f71bb0-4528-44a8-bb8e-d8fbab0417be.png)

### Actions decided

- open question is if/how this could go into the core or if it should remain an extra package maintained by TU Graz
- ensure possibility to configure aspects in order to meet discussed requirements, use cases and user stories
- upload package to github for future collaboration
- after follow-up discussion, one idea is to extend the functionality of the requests in the core and build the curation on top of these changes
    - more details: https://codimd.web.cern.ch/_pB2IO2sTy2U6MB67ExW2w#
    - Split approval and acceptance of requests
    - Requests can be extended with requirements/checks. These have to be fulfilled before a request can be accepted.
        - Can be specified per request type
        - Repository wide requirements are applied to all request types