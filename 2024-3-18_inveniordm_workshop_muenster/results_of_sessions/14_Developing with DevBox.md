# Session Report

## Session title: Developing with Devbox

**Time slot:**
Wednesday, 13.15 - 14.45

**Room:**
R18

**Host:**
Sarah

**Participants:**
- Alberto
- Join-Loic
- Michael
- Michael
- Max
- Hrafn
- Dylan
- Sefakor

### Problem statement

In addition to the short presentation on Monday with the same title we want to give participants the chance to try out using Devbox for development themselves. We will provide a Virtual Box image with all necessary dependencies installed so you can get right into it and we will assist you and answer your questions. 
Please install [Virtual Box](https://www.virtualbox.org/wiki/Downloads) ahead of the session on your own machine and download the [provided image](https://uni-muenster.sciebo.de/s/1zxDIUWuiBcoqFj) if you are interested in this workshop.
[Worksheet with instructions](https://pad.uni-muenster.de/WrVqNv4eTIWLeCPniq1J4Q?view)

### Ideas shared

- Directory structure:

![](https://radosgw.public.os.wwu.de/pad/uploads/c00a53d1-05ca-47c8-a10a-5c51b325af9e.png)

```
(.venv) (devbox) invenio@invenio-VirtualBox:~/Desktop$ tree
.
\u251c\u2500\u2500 install_vm.txt
\u2514\u2500\u2500 invenio_devbox
    \u251c\u2500\u2500 devbox.json
    \u251c\u2500\u2500 devbox.lock
    \u2514\u2500\u2500 devbox_scripts
        \u251c\u2500\u2500 install-empty.sh
        \u2514\u2500\u2500 invenio-cli.sh

```

- Steps to install Docker and Devbox (used to prepare VM image):
```
#install_vm.txt
sudo apt-get install git

curl -fsSL https://get.jetpack.io/devbox | bash

# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" |
    sudo tee /etc/apt/sources.list.d/docker.list >/dev/null
sudo apt-get update

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo groupadd docker
sudo usermod -aG docker invenio

docker run hello-world
docker compose version
```
- Devbox specifications:

```
#devbox.json
{
  "packages": [
    "python39@latest",
    "nodejs@18.18.2",
    "cairo@latest",
    "imagemagick@latest",
    "pcre@latest",
    "gcc@latest",
    "pkg-config@latest",
    "zlib@latest",
    "icu@latest",
  ],
  "env": {
    "NODE_OPTIONS": "--openssl-legacy-provider",
    "PIPENV_VENV_IN_PROJECT": "1",
    "PIPENV_VERBOSITY": "-1",
    "PYTHONWARNINGS": "ignore"
  },
  "shell": {
    "init_hook": [
      "source $VENV_DIR/bin/activate",
      "bash devbox_scripts/invenio-cli.sh",
      "touch $DEVBOX_PROJECT_ROOT/.envrc && source $DEVBOX_PROJECT_ROOT/.envrc",
      "alias iab='cd $DEVBOX_PROJECT_ROOT/$DEVBOX_INSTANCE_PATH && invenio-cli assets build'",
      "alias ir='cd $DEVBOX_PROJECT_ROOT/$DEVBOX_INSTANCE_PATH && invenio-cli run'",
      "alias iaw='cd $DEVBOX_PROJECT_ROOT/$DEVBOX_INSTANCE_PATH && invenio-cli assets watch'",
      "alias feierabend='cd $DEVBOX_PROJECT_ROOT/$DEVBOX_INSTANCE_PATH && invenio-cli services stop && exit'"
    ],
    "scripts": {
      "install-empty": "bash devbox_scripts/install-empty.sh",
    }
  }
}
```
- Install script for empty InvenioRDM v12 instance:
```
#install-empty.sh
# Copyright (C) 2023 University of M\u00fcnster.
#
# Invenio-devbox is free software; you can redistribute it and/or
# modify it under the terms of the MIT License; see LICENSE file for more
# details.


function echo_info() {
    tput setaf 2
    echo -e "$1"
    tput sgr0
}
function echo_failure() {
    tput setaf 1
    echo -e "$1"
    tput sgr0
    exit 1
}
function echo_warning() {
    tput setaf 3
    echo -e "$1"
    tput sgr0
}
function save_var_to_env() {
    # Function to save variable to .envrc
    local varname="$1"
    local value="$(eval echo \$$varname)"
    if grep -q "^export $varname=" .envrc; then
        # If the variable already exists in the file, overwrite it
        sed -i "/^export $varname=/c\export $varname=\"$value\"" .envrc
    else
        # If the variable doesn't exist in the file, append it
        echo "export $varname=\"$value\"" >>.envrc
    fi
}
function write_config_file() {

    local INSTANCE_NAME="$1"
    #create a .invenio file to customize the init process without user-input
    echo_info "Creating .invenio file"
    INSTANCE_NAMEDASH=$(echo "$INSTANCE_NAME" | sed -e 's/ /-/g' | tr '[:upper:]' '[:lower:]')
    INSTANCE_NAMEUNDERSCORE=$(echo "$INSTANCE_NAME" | sed -e 's/ /_/g' | tr '[:upper:]' '[:lower:]')
    echo -e "[cli]\nflavour = RDM\nlogfile = /logs/invenio-cli.log\n\n[cookiecutter]\nproject_name = ${INSTANCE_NAME}\nproject_shortname = ${INSTANCE_NAMEDASH}\npackage_name = ${INSTANCE_NAMEUNDERSCORE}\nproject_site = ${INSTANCE_NAMEDASH}.com\n#github_repo = ${INSTANCE_NAMEDASH}/${INSTANCE_NAMEDASH}\ndescription = ${INSTANCE_NAME} InvenioRDM Instance\nauthor_name = CERN\nauthor_email = info@$INSTANCE_NAMEDASH.com\nyear = 2023\ndatabase = postgresql\nsearch = opensearch2\nfile_storage = local\ndevelopment_tools = yes\nsite_code = yes\n_template = https://github.com/inveniosoftware/cookiecutter-invenio-rdm.git" >".invenio"

}
function install_instance() {
    local FOLDER=$(pwd)
    echo "Please enter the name of the instance (default: my-site):"
    read DEVBOX_INSTANCE_PATH
    if [ -z "$DEVBOX_INSTANCE_PATH" ]; then
        DEVBOX_INSTANCE_PATH="my-site"
        invenio-cli init --no-input -c master
    else
        write_config_file $DEVBOX_INSTANCE_PATH
        invenio-cli init --no-input -c master --config ".invenio"
    fi
    save_var_to_env DEVBOX_INSTANCE_PATH
    cd "$DEVBOX_INSTANCE_PATH"

    echo_info "Installing InvenioRDM instance in $DEVBOX_INSTANCE_PATH"

    echo "Running invenio-cli install..."

    invenio_init_status=$?
    # install instance
    invenio-cli install
    invenio_install_status=$?
    echo "RDM_RECORDS_USER_FIXTURE_PASSWORDS = { 'admin@inveniosoftware.org': '123456' }" >>invenio.cfg
    echo_info "Setting up services without demo data"
    invenio-cli services setup -f --no-demo-data

    invenio_services_status=$?

    # activate admin user
    pipenv run invenio users activate admin@inveniosoftware.org
    sed -i 's/SECURITY_LOGIN_WITHOUT_CONFIRMATION = False/SECURITY_LOGIN_WITHOUT_CONFIRMATION = True/' invenio.cfg
    # check if all steps were successful
    if [ "$invenio_init_status" -eq 0 ] && [ "$invenio_install_status" -eq 0 ] && [ "$invenio_services_status" -eq 0 ]; then
        echo "InvenioRDM is installed."
    fi
    source "$DEVBOX_PROJECT_ROOT/.envrc"
}

install_instance
```
- helper script to install invenio-cli:
```
#invenio-cli.sh
# Copyright (C) 2023 University of M\u00fcnster.
#
# Invenio-devbox is free software; you can redistribute it and/or
# modify it under the terms of the MIT License; see LICENSE file for more
# details.


PACKAGE_NAME="invenio-cli"

# Check if the package is installed
if pip show "$PACKAGE_NAME" >/dev/null 2>&1; then
  echo "$PACKAGE_NAME is installed."
else
  echo "$PACKAGE_NAME is not installed. Installing..."
  pip install "$PACKAGE_NAME"
fi

# Check if an update is available for the package
# update_output=$(pip search "$PACKAGE_NAME" --upgrade 2>&1)

# if echo "$update_output" | grep -q "No matching distribution found"; then
#   echo "No update available for $PACKAGE_NAME. Skipping."
# else
#   echo "Update available for $PACKAGE_NAME:"
#   echo "$update_output"
# fi
```


### obscure Knowledge

Convert the extracted virtualbox image to KVM
`qemu-img convert -f vmdk -O qcow2 Invenio\ Devbox-disk001.vmdk InvenioDevbox-disk001.qcow2`

run with KVM
`virt-install --name=InvenioDevbox --disk path=InvenioDevbox-disk001.qcow2,format=qcow2 --import --osinfo detect=on --memory 4096`


### Actions decided

- share repository with all used scripts with community


