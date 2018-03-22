This repository contains the ports of MySQL and various microbenchmarks used in my bachelor thesis.

It was git-filter-branched off modifications I originally made in the ba-osv repo.

Main repo that includes this repository as a submodule: github.com/problame/ba-osv

## MySQL Ports

If the PRESEED environment variable is set while building the MySQL ports, the build script expects a tar ball that contains a MySQL data directory at its root.
It will use this tar ball to pre-seed the data directory in the install directory and thus ultimately the OSv image with data.

This is handy for benchmarks like OLTP TPC-C, which take quite a lot of time for provisioning the database with the benchmark data before actually executing the benchmark.

Example invocation of the OSv `scripts/build` command:

```
PRESEED=/home/cschwarz/evaluation/blobs/whole_system/oltp_tpcc_preseed_2warehouses.tar \
    scripts/build modules=cs_microbench,mysql_stagesched,cli -j 16
```

** Note that scripts/build does not support passing KEY=VALUE arguments to the module build scripts, hence the environment variable.**


