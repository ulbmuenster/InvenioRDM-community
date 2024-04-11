# Session Report

## Session title: Translations / i18n, i10n

**Time slot:**
Tuesday, 10.30 - ??

**Room:**
R17

**Host:** 
Christina

**Participants:**
- Michael
- Sarah
- Markus
- Alberto
- Zack
- Mirek times two
- David

**Initial ideas:**

improving "translation" features, documentation, overwrite translations,
js-translation-topic / overwrite js / load js strings?, i18n-repo-issues,
alternatives to transifex?, translation-versioning, translation of yaml-files,
where are we? where to go next?

## Points we discussed:

### current situation
  - python strings
  - Jinja Templates stings
  - JavaScript strings
  - python-jinja-js goes into messages.pot-files
  - yaml-files, css-files: vocabularies (subjects, records, licences, launguage.py, xml?, json?) - part of the repo
  - sent emails (there are provided by the repo, but currently aren't sent, maybe v12 - profile set language?)
  - https://github.com/inveniosoftware/invenio-app-rdm/tree/master/invenio_app_rdm/theme/templates/semantic-ui/invenio_app_rdm/help
    - search.de.html
    - search.en.html
    - statistics.en.html
    - versioning.en.html 
  - seach.html
- English is used as a label/id
  - if you fix a typo in English - all languages need to retranslate this label/id
- translation happens after a version release
  - all messages.pot files (from all repositories) are created
  - pushed to transifex
  - within a small time period translation needs to be done for all languages
  - translation is freezed for a specific version
  - pulled from transifex
  - every package is released with the new translations
- overwriting translations in most parts isn't (official) possible
  -  my-site/pages python-code should work
  -  you can hack it in via the translation-files in your own instance
- documentation is spare

### focus on vocabularies

- licences we don't/won't (cannot) translate!

- open question: what files are recommended/allowed for vocabularies? yaml-files, csv-files, xml?
- language-specific: polie officer vs. police man vs. police woman
- free of barriers (aria-labels)

- we don't translate (files that come from "somewhere else"): 
  - https://github.com/inveniosoftware/invenio-rdm-records/blob/master/invenio_rdm_records/fixtures/data/vocabularies/affiliations_ror.yaml
- we want all the languages in:
  - https://github.com/inveniosoftware/invenio-rdm-records/blob/master/invenio_rdm_records/fixtures/data/vocabularies/community_types.yaml 
- yaml:
  - yaml-files
    - translations will be part of the file (files will get bigger :/)
    - there seems no other solution
  - how to get translators to translate them?
  - where are the files (documentation-issue)? (mostly: invenio-rdm-records)


### overwrite translations

- Zack confirmed that translations in my-site take precedence if I (Sarah) understood that correctly (backend translations)


## Actions we decided to take:

- test if yaml-files can have four letter codes
- group tests Münster approach way of overwriting python strings, java-script
- Zack and David try to implement the Münster approach into the official cookiecutter so its easier to use and has a global storage of frontend translations

## Session notes

![](https://radosgw.public.os.wwu.de/pad/uploads/caa3c687-cc16-48db-b822-f840d1cf98f0.jpg)
![](https://radosgw.public.os.wwu.de/pad/uploads/c1c9825f-e3dc-4961-b678-c712c8004400.jpg)
![](https://radosgw.public.os.wwu.de/pad/uploads/01265a20-d106-4d2f-ad9e-9f37cb996001.jpg)


## Münster approach

Link to translations directory containing all currently used files for our override approach:
https://uni-muenster.sciebo.de/s/2Njnp8tbceaglBD

Works in empty, fresh v12 instance:
![](https://radosgw.public.os.wwu.de/pad/uploads/95d2a0bc-3047-4c10-aa1c-840b392c0997.png)


### Marking strings that are to be translated

- You can follow the [official Invenio documentation](https://inveniordm.docs.cern.ch/develop/howtos/i18n/#marking-strings-for-translation) for this.
- Briefly summarized, strings in Python (Jinja2 templates, invenio.cfg) are marked with the gettext function from the Flask-BabelEx package. This is normally imported as _.
    - Example:``` error_message = _("Invalid scheme")```
- In JavaScript files, strings are marked using the i18next module.
    - Example: ```<p>{i18next.t("Only published versions are displayed.")}</p>```
- To do this, our (!) i18next instance from i18next.js must first be imported into the files:
    - Example (adjust exact path if necessary): ```import { *i18next* } from "../../translations/my-site/i18next.js";```

### Creating new translations

All strategies presented refer to translations in **our instance**. For the procedure for translating **modules**, please refer to the [official documentation](https://inveniordm.docs.cern.ch/develop/howtos/i18n/)!

#### Python

- For this you can follow the [official documentation](https://inveniordm.docs.cern.ch/develop/howtos/i18n/#invenio-cli) (section "Invenio-CLI").
- Briefly summarized, all marked strings are first extracted from JINJA2 templates and invenio.cfg and collected in a messages.pot file:
```invenio-cli translations extract```
- Translation catalogs for individual languages can then be initialized from this messages.pot (example for German - de):
```invenio-cli translations init -l de```
    - This creates a folder containing a messages.po file for each initialized language. Translations can now be entered manually in this file.
- The translations in messages.po must then be compiled so that they can also be used in the instance:
```invenio-cli translations compile```
    - This creates a messages.mo for each configured language.
    - The *.mo files do not need to be versioned.
- Then rebuild all assets and restart the InvenioRDM server:
```invenio-cli assets build```
```invenio-cli run```

#### Javascript

- Navigate to the SemanticUI translation folder (my-site/site/my-site/assets/semantic-ui/translations/my-site/messages)
- Create files as described below (e.g. can be copied from invenio_app_rdm and adapted)
   ~~ - The desired language abbreviations must be entered in package.json under languages~~
    - Only the desired languages may be imported and exported in index.js
~~- Run ```npm install``` once in the messages folder if this has not already been done~~
~~- Then extract the selected strings into a translations.pot file:~~
```npm run extract_messages```
~~- Manually create a translations.po for each desired language from the translations.pot in a subfolder ~~with the language abbreviation as the name (e.g. "de")
~~- Then compile the translations.po to a JSON file with~~
```npm run compile_catalog```
```npm run compile_catalog lang <lang> # for specific language```
- **Note: These JSON files can also be created manually without the above steps!**
- The folder structure should then look like this (*.po and *.pot optional)

```
my-site/site/my-site/assets/semantic-ui
- translations
-- messages
--- en
---- translations.json
---- translations.po*
--- en
---- translations.json
---- translations.po*
--- index.js
-- scripts*
--- compileCatalog.js*
--- initCatalog.js*
-- i18next.js
-- i18next-scanner.config.js*
-- package.json
-- translations.pot*

# *: not necessary for overriding only
```

- Then rebuild all assets and restart the InvenioRDM server:
```invenio-cli assets build```
```invenio-cli run```

### Special steps when overwriting existing translations
- Starting point: Some terms in the frontend are to be changed, e.g. "community" → "project"
- Renaming only in JINJA templates is not possible and would not make sense:
    - Strings in React components cannot be changed in this way
    - A large number of templates would have to be copied to our instance and manually maintained and updated there just to make the smallest changes
- Alternative: A solution based on the Invenio translation workflow
- Advantages:
    - All strings in the frontend are marked as "translatable" (or can be easily marked in our self-written templates and components), so we can reach all parts of the frontend relatively easily
    - The translations are only used selectively for overwriting and can be easily maintained, as other parts of the affected Invenio modules are not changed
    - For replacing individual terms in JINJA templates, this is the official way provided in the Invenio documentation
- Compared to the procedure for translating strings in our own instance, some changes must be taken into account

#### Python
- Overrides can simply be added to the messages.po files. When updating the translations, comment characters may have to be removed from these overrides.
- When compiling, the added overrides are also compiled into the generated messages.mo files

#### Javascript

- Strings to be overwritten can be inserted in additional *translations_<module_name>.json* files and added to the folders described above for the respective language under *my-site/site/my-site/assets/semantic-ui/translations/my-site/messages*
- These translations must then be imported and exported in *index.js* (analogous to those for our own instance)
- In *i18next.js*, import the i18next instances of the modules from which strings are to be overwritten with an alias
    - Example: ```import { i18next as i18next_communities } from "@translations/invenio_communities/i18next";```
- Also import the additional translations
    - Example: ```import { *translations_communities* } from "./messages";```
- Additional ResourceBundles can then be added to these imported instances:
    - Example: ```i18next_communities.addResourceBundle("en", "translation", *translations_communities*["en"]["translation"], true, true);```
- For this code to be executed, the i18next.js file must be added with an alias name in *webpack.py* (under "semantic-ui": "entry")
- This must then be called e.g. in templates/semantic-ui/invenio_app_rdm/javascript.html:
    - ```{{ webpack['add-translations.js'] }}```
- Then rebuild all assets and restart the InvenioRDM server:
```invenio-cli assets build```
```invenio-cli run```


## CESNET Approach

A CLI tool `make-translations` that relies on a setup.cfg config to discover
any sources for translation strings, collects & extracts all the messages (babel + i18next) and merges into a single resulting message catalog. After that, messages are compiled from the catalog, to be used by Python & React i18next

![](https://radosgw.public.os.wwu.de/pad/uploads/ee7bf131-93c7-41c8-83cd-2edc4c40f3f8.png)
.

Translations workflow diagram:
https://excalidraw.com/#json=cBWEc4nO6lus5V8Z0IRGM,ZxKGQ7DSAZYB_C0BJZs2VA

`make-translations` cli tool:
https://github.com/oarepo/oarepo-tools/blob/main/oarepo_tools/make_translations.py

Sample configuration file (inside a repository, yaml file):
https://github.com/Molecular-Biophysics-Database/mbdb-app/blob/development/oarepo.yaml

Configuration inside a library (standard setup.cfg):
https://github.com/oarepo/oarepo-vocabularies/blob/eef0d9c8f9826277e6f80124efd1412d7a99b5ea/setup.cfg#L55

## Slightly Off-topic

Quick hack to search for patterns in all translation files (jsons and po) in RDM instance:

https://gist.github.com/mesemus/818b8fd1a73f290c7132a5061a86ea60

Usage:

1. Put the script to the root of RDM
2. Call `./find-translations.sh -l de title`

The search is performed via "egrep", so you can
use regular expressions in there.

Output:

```
.../invenio_app_rdm/theme/assets/semantic-ui/translations/invenio_app_rdm/messages/de/translations.json
=================================
49:    "No title": "Ohne Titel",


.../invenio_vocabularies/translations/de/LC_MESSAGES/messages.po
=================================
113-#: invenio_vocabularies/contrib/awards/schema.py:103
114:msgid "An existing id or number/title must be present."
115-msgstr "ID (existierend) oder Nummer/Titel darf nicht leer sein."
--
147-#: invenio_vocabularies/services/service.py:68
148:msgid "Title"
149-msgstr "Titel"
```