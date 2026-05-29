# Elasticsearch integration (QES module)

Starting from version 3.10, G3W-SUITE ships with the **`qes`** module (QGIS Elasticsearch), an optional component that indexes QGIS layer features into [Elasticsearch](https://www.elastic.co/elasticsearch) to provide fast, full-text search across the published WebGIS projects.

The module is part of the [g3w-admin](https://github.com/g3w-suite/g3w-admin) codebase (`g3w-admin/qes/`) and is activated by adding `qes` to the `G3WADMIN_LOCAL_MORE_APPS` list in `local_settings.py`.

## Overview

When the module is enabled, G3W-SUITE will:

- create one Elasticsearch index per user (`qgis_features_<user_pk>`, or `qgis_features_anonymous` for unauthenticated access) containing the features the user is authorized to see;
- index attribute values and a derived `text_content` field used for full-text search;
- keep the indexes synchronized with editing operations through Django signals (create / update / delete on vector layers managed by the `editing` module);
- expose a REST endpoint that returns search hits for a given query within a project.

Per-user indexes are used to respect G3W-SUITE permissions: each user only searches the subset of features they have access to.

## Requirements

- An Elasticsearch server reachable from G3W-ADMIN. The reference version used by the official Docker images is **Elasticsearch 8.18.x**.
- The following Python packages (already declared in the g3w-admin `requirements.txt`):
    - `django-elasticsearch-dsl==8.0`
    - `elasticsearch` (installed as a transitive dependency)

When `qes` is listed in `G3WADMIN_LOCAL_MORE_APPS`, G3W-SUITE automatically adds `django_elasticsearch_dsl` to `INSTALLED_APPS`.

## Deploy with Docker

The official `docker-compose.ltr.yml` / `docker-compose.latest.yml` files in [g3w-suite-docker](https://github.com/g3w-suite/g3w-suite-docker) include an `elasticsearch` service ready to use:

```yaml
elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:8.18.1
    environment:
      - discovery.type=single-node
      - xpack.security.enabled=false
      - ES_JAVA_OPTS=-Xms512m -Xmx512m
    volumes:
      - esdata:/usr/share/elasticsearch/data
    ports:
      - "9200:9200"
```

The G3W-SUITE container reaches it through the internal Docker network at `http://elasticsearch:9200`.

## Manual installation

If you are not using the provided Docker compose stack, install and start an Elasticsearch 8.x instance on a host reachable by G3W-ADMIN, then configure the connection as described below.

For installation instructions refer to the official documentation: <https://www.elastic.co/guide/en/elasticsearch/reference/current/install-elasticsearch.html>.

## Configuration

Add `qes` to the local apps and configure the Elasticsearch connection in `local_settings.py`:

```python
G3WADMIN_LOCAL_MORE_APPS = [
    # ...
    'qes',
]

# Elasticsearch connection
ELASTICSEARCH_DSL = {
    'default': {
        'hosts': 'http://elasticsearch:9200',
        # 'http_auth': ('username', 'password'),  # if security is enabled
    }
}

# Activate/deactivate indexing of QGIS projects
QES_INDEXING_PROJECT = True
```

### Available settings

``ELASTICSEARCH_DSL``
^^^^^^^^^^^^^^^^^^^^^

Standard [`django-elasticsearch-dsl`](https://django-elasticsearch-dsl.readthedocs.io/) connection dictionary. The `default` alias is the one used by the QES indexer. Use `http_auth` to provide credentials when X-Pack security is enabled.

``QES_INDEXING_PROJECT``
^^^^^^^^^^^^^^^^^^^^^^^^

Default: `True`. Master switch that enables (or disables) the automatic indexing of QGIS projects in Elasticsearch. When set to `False` the `qes` module is loaded but does not index anything and the search API returns no results.

``QES_INDEXING_FIELDS``
^^^^^^^^^^^^^^^^^^^^^^^

Default: `{}` (index every attribute). Optional dictionary, keyed by QGIS layer id, that restricts the fields that will be indexed. Example:

```python
QES_INDEXING_FIELDS = {
    'cities10000eu20171228095720113': ['name', 'country', 'population'],
}
```

Layers that are not listed in `QES_INDEXING_FIELDS` are still fully indexed; layers that are listed are limited to the declared field names.

``QES_SEARCH_QUERY``
^^^^^^^^^^^^^^^^^^^^

Elasticsearch query template used by the search endpoint. The placeholder `'$query_text'` is replaced at runtime with the text submitted by the user. The default template combines `match_phrase`, `match` (with operator `and`), `wildcard` and a fuzzy `multi_match` over the `text_content` field to balance precision and recall. Override it only if you need to tune relevance.

``QES_SEARCH_SORT``
^^^^^^^^^^^^^^^^^^^

Default sort applied to search hits. Defaults to ascending `layer_id`:

```python
QES_SEARCH_SORT = {
    "layer_id": {"order": "asc"}
}
```

``QES_INDEXING_CRON_SCHEDULE``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Default: `None`. When set to a [Huey `crontab`](https://huey.readthedocs.io/) expression, the periodic task `es_project_cron_indexing` is scheduled and runs a full re-indexing on the defined schedule. Example — re-index every four hours:

```python
from huey import crontab
QES_INDEXING_CRON_SCHEDULE = crontab(hour='*/4')
```

This requires the Huey consumer to be running (see the *consumer* Docker compose recipe in [docker.md](docker.md)).

``QES_INDEXING_CRON_PRJIDS``
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Optional, used together with `QES_INDEXING_CRON_SCHEDULE`. Space-separated list of QGIS project ids to restrict the cron-driven re-indexing to a subset of projects. If omitted, every project is re-indexed.

```python
QES_INDEXING_CRON_PRJIDS = '1 2 3'
```

## Indexing projects

### One-shot indexing from the command line

The `qes_indexer` management command performs a full indexing of one or more QGIS projects for every user that has permission to access them:

```bash
# Index every project
python manage.py qes_indexer

# Index only the given project ids
python manage.py qes_indexer --prj_ids 1 2 3
```

A separate per-user index named `qgis_features_<user_pk>` is created for each user with `view_project` permission on the indexed projects.

### Automatic incremental indexing

When `QES_INDEXING_PROJECT = True`, G3W-SUITE listens to project save / delete and to editing commits on vector layers and updates the corresponding documents automatically. No manual action is required after the initial indexing.

### Scheduled re-indexing

Set `QES_INDEXING_CRON_SCHEDULE` (see above) to run a full re-indexing on a regular basis through the Huey consumer.

## Search API

The module exposes a per-project search endpoint backed by `QesSearchAPIView`. Issue a `GET` request to the project search URL with a `q` query parameter, for example:

```
GET /api/qes/search/<project_type>/<project_id>/?q=<text>
```

The endpoint honours the standard G3W-SUITE project permissions: anonymous users only see results from projects shared with the anonymous user, authenticated users see the subset of features that match their grants.

The response is a JSON list of hits. Each hit contains:

- `score` — Elasticsearch relevance score
- `project_id`, `project_name`
- `layer_id`, `layer_name`
- `feature_id`
- `attributes` — the indexed attribute values
- `highlights` — Elasticsearch highlighting on `text_content` and `attributes.*`

## Troubleshooting

- **No results / empty indexes** — verify that `QES_INDEXING_PROJECT` is `True`, that the Elasticsearch service is reachable at the configured host, and that the `qes_indexer` command completes without errors. The module logs to the `elasticsearch` Python logger (`[QES-elasticsearch]` log tag).
- **Authentication errors** — if your Elasticsearch instance has security enabled, set `http_auth` in `ELASTICSEARCH_DSL['default']`.
- **Stale data after editing** — the receivers only update documents for the project / layer being modified. To rebuild from scratch run `python manage.py qes_indexer --prj_ids <id>`.
