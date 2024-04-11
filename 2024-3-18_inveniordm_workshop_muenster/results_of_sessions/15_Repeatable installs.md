# Session Report

## Session title: 

**Time slot:**
Thursday, 9.30 - 10.45

**Room:**
R5/6

**Host:**
Mirek & Mirek (CESNET)

**Participants:**
* Nico (CERN)
* Mirek & Mirek (CESNET)

### Problem statement

The `package-lock.json` file is not included in `my-site` folder, and builds are not fully reproducible, as the JS dependencies are always re-locked.

### Ideas shared

`npm ci` command to install packages, while respecting `package-lock.json`: https://support.deploybot.com/article/131-why-developers-should-use-npm-ci-instead-of-npm-install-and-its-benefits

### Actions decided

* always copy `package-lock.json` from `../var/instance/assets/` to `my-site/`. Make sure that `git` ignores the creation/modification file dates if the file content didn't change
* have an extra CLI param in `invenio-cli` that will:
    * copy the `package-lock.json` from `my-site` to `../var/instance/assets/`
    * if the `my-site/pipfile.lock` is newer than `my-site/package-lock.json`, then overwrite or fail (some python deps might have changed)
    * run `npm ci` instead of `npm install` 
    
* the development workflow with linked editable (watched) modules should be tested, to ensure nothing breaks
* when in `NODE_ENV=production`, the above should always happen. When in `NODE_ENV=dev`, then I can use a new param `invenio-cli install --keep-deps`
* New issue tracked here: https://github.com/inveniosoftware/product-rdm/issues/179