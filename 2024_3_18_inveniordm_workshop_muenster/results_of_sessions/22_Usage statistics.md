# Session Report

## Session title: Usage statistics

**Time slot:**
Thursday, 13.15 - 14.45

**Room:**
R19/20

**Host:**
Tom Morrell (Caltech)

**Participants:**

- David Pape (HZDR)
- Alberto Ortiz Flores
- Philipp Gualdi(TU Graz)

### Problem statement

- No documented way to migrate index
- Backing up index is difficult because it is large
- How does this even work?

### Ideas shared

There are three levels of processing with the usage statistics

Events (views snd downloads) in the API and UI are sent to RabbitMQ. These are anonymized (IP address-> country), robods defined by [counter-robots](https://github.com/inveniosoftware/counter-robots) are removed, double clicks are removed, and then indexed in Open Search. The Open Search indexes are templates and defined in monthly indexes (In early v12beta versions these indexes were daily). The processing task is controlled by stats-process-events

Most of the processing is defined in rdm-records and [invenio-stats](https://invenio-stats.readthedocs.io/en/latest/) has some documentation.

The second phase of processing is aggregation. This is run on a schedule based on stats-aggregate-events. These are stored in the same index as the events. A bookmark (stats_bookmark value in the index) is used to record what events have been aggregated. Admins can adjust this bookmark via a cli command, which is useful for controlling reprocessing

Finally a query looks acros xall the aggregations to get the total views and downloads for a record. These queries can be extended if different data is requested (such as views per month) Some types of queries (such as aggregation by community) will require changes at all levels of processing (to have the community show up in the aggregation, for example)

The display of the views and downloads in a record happend at record index time. This means that the numbers will not live-update when a used views a record.

The Open Search indexes need to be backed up because this data is not stored elsewhere. Because the indexes are templates they should not be touched by reindex commands.

Importing legacy usage statistics could be achieved by creating the appropriate open search indexes. It's ideal to provide events, since RDM can generate the aggregations and events enable the creation of different aggregations in the future. When an event patches a pattern (date) for an index it is included in the appropriate index.

Why not store these in the database?

Large tables are more challenging to store and manage
Open search could be replaced, but would involve imporlementation changes at every level of processing

- Counter reports are not implemented but could be in the future. This would require a monthly aggregation
- Indexing roll-up into (e.g. yearly) indexes could be a future feature to reduce the number of indexes
- Making reports over a custom period is a future feature.


### Actions decided

Created discussions

https://github.com/inveniosoftware/product-rdm/discussions/181
https://github.com/inveniosoftware/product-rdm/discussions/182
