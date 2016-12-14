# dedupe-api

A work-in-progress to provide a standard interface for deduplication of large databases with custom pre-processing and post-processing steps.

### Interface

This provides a simple command-line script, `deduper.py`. Two configuration files specify the configuration parameters and database connection settings.  To run deduplication on a generated dataset, create a `database.yml` file that specifies the following paramters:

```yml
user:
password:
database:
host:
port:
```

You can now create a sample CSV file with:

```sh
$ python generate_fake_dataset.py
creating people: 100%|█████████████████████| 9500/9500 [00:21<00:00, 445.38it/s]
adding twins: 100%|█████████████████████████| 500/500 [00:00<00:00, 1854.72it/s]
writing csv:  47%|███████████▋             | 4666/10000 [00:42<00:55, 96.28it/s]
```

Once complete, store this example dataset in a database with:

```sh
$ python test/initialize_db.py
CREATE SCHEMA
DROP TABLE
CREATE TABLE
COPY 197617
ALTER TABLE
ALTER TABLE
UPDATE 197617
```

Now you can deduplicate this dataset. This will run dedupe as well as the custom pre-processing and post-processing steps.

```sh
$ python deduper.py config.yml --db database.yml
```

### Custom pre- and post-processing

In addition to running a database-level deduplication with `dedupe`, this script adds custom pre- and post-processing steps to improve the run-time and results, making this a hybrid between fuzzy matching and record linkage.

* **Pre-processing:** Before running dedupe, this script does an exact-match deduplication. Some systems create many identical rows; this can make it challenging for dedupe to create an effective blocking strategy and generally makes the fuzzy matching much harder and time intensive.
* **Post-processing:** After running dedupe, this script does an optional exact-match merge across subsets of columns.  For example, in some instances an exact match of just the last name and social security number are sufficient evidence that two clusters are indeed the same identity.

### Further steps

This script was based upon and extended from the example in [dedupe-examples](https://github.com/datamade/dedupe-examples/tree/master/pgsql_big_dedupe_example).  It would be nice to use this common interface across all databases, and potentially even CSV files.
