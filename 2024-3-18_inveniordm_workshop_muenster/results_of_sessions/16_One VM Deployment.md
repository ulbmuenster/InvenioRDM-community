# Session Report

## Session title: One VM Deployment

**Time slot:**
Thursday, 9.30 - 10.45

**Room:**
R19/20

**Host:**
Michael (Bamberg)

**Participants:**

- Tom Morrell (Caltech)
- David Pape (HZDR)
- Dylan Bollaro ([Ulysseus](ulysseus.eu))
- Kai Wörner (UHH)
- Alberto Ortiz Flores
- Sefakor Ankora (WACREN)
- Janne Jensen (HS Bremen)
- Philipp Gualdi (TU Graz)
- Hrafn Malmquist (Cottage Labs)

### Problem statement

![](https://radosgw.public.os.wwu.de/pad/uploads/307caebe-9930-40fb-9e44-0a7346991546.jpg)

* No documentation how to move from dev to production
    * Some documentation on helmcharts
    * No documentation on the minimal setup

* things that are needed
    * something about reverseproxies and how to get traefic to your machine
    * minimal setup guide
    * local storage
    * docker compose
    * security could be an issue

### Ideas shared

#### Ressources

* https://tu-graz-library.github.io/docs-repository/deployment/
* https://github.com/caltechlibrary/caltechauthors?tab=readme-ov-file#caltechauthors
* https://github.com/inveniosoftware/cookiecutter-invenio-rdm


#### Tops for documentation
The github issue: https://github.com/inveniosoftware/cookiecutter-invenio-rdm/issues/278

entry (big bad internet --> VM)

* reverseproxy for certificate termination
* invenio ngnix for certificate termination
* traefik for certificate termination

volume management
secret management
uwsgi config

Image generation for web/api/celery or local install?

Cleaned up docker compose in cookiecutter (remove kibana, pgadmin, osearchdashboard, minio, elastic stack)

local storage (with S3 config instructions)

redis vs rabbitmq

persistent test
* .env
* secrets (preferred since it will be setup in docker compose)

exemple of docker-compose with secrets
```yaml
version: '2.2'
services:
  cache:
    extends:
      file: docker-services.yml
      service: cache

  db:
    extends:
      file: docker-services.yml
      service: db
    volumes:
      - ./postgres_data:/var/lib/postgresql/data
    secrets:
      - postgresql_pass

  mq:
    extends:
      file: docker-services.yml
      service: mq

  search:
    extends:
      file: docker-services.yml
      service: search
    volumes:
      - opensearch_data:/usr/share/opensearch/data

  # Frontend
  frontend:
    extends:
      file: docker-services.yml
      service: frontend
    image: registry.univ-cotedazur.fr:443/ulysseus/config/invenio:invenio-nginx-11-v2.0
    volumes:
      - static_data:/opt/invenio/var/instance/static
    depends_on:
      - web-ui
      - web-api
    ports:
      - "80:80"
      - "443:443"

  # UI Application
  web-ui:
    extends:
      file: docker-services.yml
      service: app
    env_file:
      - .env
    command: ["uwsgi /opt/invenio/var/instance/uwsgi_ui.ini"]
    image: registry.univ-cotedazur.fr:443/ulysseus/config/invenio:invenio-rdm-11-v2.0
    ports:
      - "5000"
    volumes:
      - static_data:/opt/invenio/var/instance/static
      - uploaded_data:/opt/invenio/var/instance/data
      - archived_data:/opt/invenio/var/instance/archive

  # API Rest Application
  web-api:
    extends:
      file: docker-services.yml
      service: app
    env_file:
      - .env
    command: ["uwsgi /opt/invenio/var/instance/uwsgi_rest.ini"]
    image: registry.univ-cotedazur.fr:443/ulysseus/config/invenio:invenio-rdm-11-v2.0
    ports:
      - "5000"
    volumes:
      - uploaded_data:/opt/invenio/var/instance/data
      - archived_data:/opt/invenio/var/instance/archive

  # Worker
  worker:
    extends:
      file: docker-services.yml
      service: app
    command: ["celery -A invenio_app.celery worker --beat --loglevel=INFO"]
    image: registry.univ-cotedazur.fr:443/ulysseus/config/invenio:invenio-rdm-11-2.0
    volumes:
      - uploaded_data:/opt/invenio/var/instance/data
    depends_on:
      search:
        condition: service_started
      cache:
        condition: service_started
      db:
        condition: service_started
      mq:
        condition: service_started

volumes:
  static_data:
  uploaded_data:
  archived_data:
  opensearch_data:

secrets:
  postgresql_pass:
    file : ./secrets/postgresql.pass
  opensearch_pass:
    file : ./secrets/opensearch.pass

```
and in the service file
```yaml
      - "POSTGRES_PASSWORD_FILE=/run/secrets/postgresql_pass"
```
and in the .env file:

```yaml
      - INVENIO_SQLALCHEMY_DATABASE_URI=""
```


### Actions decided

* improve documentation with the depolyment
    * [Docu PR work in progress](https://pad.gwdg.de/hEje65dOTIWuejjf2r30RA#)
* cookiecutter issue: https://github.com/inveniosoftware/cookiecutter-invenio-rdm/issues/278
    * have a finished docker-compose without dev container
    * improve secrets by default
