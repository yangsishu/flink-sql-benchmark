# flink-sql-benchmark

## Generate test dataset

- Step 1: Prepare your environment

  Make sure you have Hadoop and Hive installed in your cluster. `gcc` is also needed to build the TPC-DS data generator.

- Step 2: Build the data generator

  Run `./tpcds-build.sh` to download and build the TPC-DS data generator.

- Step 3: Generate TPC-DS dataset

  Run `./tpcds-setup.sh <SCALE_FACTOR>` to generate dataset. The "scale factor" represents how much
  data you will generate, which roughly translates to gigabytes. For example, `./tpcds-setup.sh 10`
  will generate about 10GB data. Note that the scale factor must be **greater than 1**.

  `tpcds-setup.sh` will launch a MapReduce job to generate the data in text format. By default,
  the generated data will be placed in `/tmp/tpcds-generate/<SCALE_FACTOR>` of your HDFS cluster.
  If the folder already exists, the MapReduce job will be skipped.

  Once data generation is completed, `tpcds-setup.sh` will load the data into Hive tables. Make sure
  the `hive` executable is in your `PATH`, alternatively, you can specify your Hive executable path
  via `HIVE_BIN` environment variable.

  `tpcds-setup.sh` will create external Hive tables based on the generated text files. These tables
  reside in a database named `tpcds_text_<SCALE_FACTOR>`. Then `tpcds-setup.sh` will convert the
  text tables into an optimized format and the converted tables are placed in database
  `tpcds_bin_<FORMAT>_<SCALE_FACTOR>`. By default, the optimized format is `orc`. You can choose
  a different format by setting the `FORMAT` environment variable. The following is an example that
  creates 1TB test dataset in parquet format:

  `FORMAT=parquet HIVE_BIN=/path/to/hive ./tpcds-setup.sh 1000`

  Once the data is loaded into Hive, you can use database `tpcds_bin_<FORMAT>_<SCALE_FACTOR>`to run
  the benchmark.