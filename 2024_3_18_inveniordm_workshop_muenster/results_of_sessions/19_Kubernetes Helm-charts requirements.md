# Session Report 

## Session title: 

**Time slot:**
Thursday, 10.45 - 12.00

**Room:**
R19/20

**Host:**
Alex (Cern)

**Participants:**

- Jan-Olof Wiefel (University of Münster)
- Mirek Bauer (CESNET)

### Problem statement

Collecting issues on current state of the [chart](https://github.com/inveniosoftware/helm-invenio/tree/master)

![](https://radosgw.public.os.wwu.de/pad/uploads/919dc29b-8350-441c-8296-e9f678825a8b.jpeg)
![](https://radosgw.public.os.wwu.de/pad/uploads/d3dda973-f931-4afe-9eec-e6020a09ef8a.jpeg)

#### Added by Mirek B
- Redis auth support needed (important for connecting to 'external' production redis cluster)
- Ability to provide additional settings to `uwsgi.ini` (e.g. security hardening, configuring uwsgi user to not run under `root`): https://github.com/inveniosoftware/helm-invenio/pull/98/files


### Actions

- Issues on the [`helm-invenio` Github repo](https://github.com/inveniosoftware/helm-invenio/issues)
    - [Configurable resources for deployments](https://github.com/inveniosoftware/helm-invenio/issues/109)
    - [Configurable {liveness,readiness,startup}Probes](https://github.com/inveniosoftware/helm-invenio/issues/110)
    - [Init and Sidecar containers config for web/worker deployments](https://github.com/inveniosoftware/helm-invenio/issues/111)
    - [Granular env-based solution for "connection string"-like config](https://github.com/inveniosoftware/helm-invenio/issues/112)
- Share of current community charts:
    - University of Münster: `helm pull oci://harbor.uni-muenster.de/ulb/invenio --version 0.4.0`
    -