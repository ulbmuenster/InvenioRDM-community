# Session Report

## Session title: 

**Time slot:**
Thursday, 15.00 - 16.30

**Room:**
R18

**Host:** 
Camelia

**Participants:**

### Problem statement
- JRC Use Case:
    -   Migration task involves:
        -   Creating users from LDAP.
        -   Changing the owner of migrated records.
- Requirements for production instance:
  -  Restricts the creation of communities to: a predefined list of users or Users with a specific affiliation
  

### Ideas shared
- Solution for user import and owner change:
    - CERN:
    Steps:
        - populate the User table and db.session.commit to get the id of the user (auto-increment primary key)
        - populate the UserProfile table, by adding all the extra info.If you are using external login authentication (SSO, SAML, OpenID/OAuth, etc.):
        - populate the UserIdentity.
        - populate the RemoteAccount.
    - Caltech:
        - change owner script: https://github.com/caltechlibrary/caltechdata/blob/main/scripts/change_owner.py
         
- Administration Panel
    - How to use it: https://inveniordm.docs.cern.ch/develop/topics/administration_panel/
    - Status of implementation: 
        - functionalities already implemented include:  Records, Mediation, Users (Note: Investigate why this may not be visible on the master )
        - users tab has to be enabled in the instance, config name is `USERS_RESOURCES_ADMINISTRATION_ENABLED`
    

### Actions decided
