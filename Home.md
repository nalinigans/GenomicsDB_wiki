# Introduction
[TileDB](https://people.csail.mit.edu/stavrosp/papers/vldb2017/VLDB17_TileDB.pdf) is a system for efficiently storing, querying and accessing sparse 
matrix/array data. TileDB is developed by researchers at the [Intel Science and Technology Center for Big Data](http://istc-bigdata.org/#&panel1-1).

GenomicsDB is a C++ library built on top of the TileDB system for importing, querying and transforming variant data. Variant data is sparse by nature (sparse relative to the whole genome) and hence TileDB is a perfect fit for storing such data.

Note: TileDB started as an ISTC project but is now being developed by [TileDB Inc](https://tiledb.io/press-oct-19-2017). There are two repositories of TileDB - the [original hosted in Github](https://github.com/Intel-HLS/TileDB) under the Intel organization and the [fork maintained by TileDB Inc](https://github.com/TileDB-Inc/TileDB). GenomicsDB builds on top of the original Intel repository and is not compatible with TileDB Inc's fork - we would eventually like to support the fork as well (5/3/2018).

The GenomicsDB stores variant data in a 2D TileDB array where:
* Each column corresponds to a genomic position (chromosome + position)
* Each row corresponds to a sample in a VCF (or CallSet in the GA4GH terminology)
* Each cell contains data for a given sample/CallSet at a given position; data is stored in the form of TileDB cell attributes.
* Cells are stored in column major order - this makes accessing cells with the same column index (i.e. data for a given genomic position over all samples) fast.
* Variant interval/gVCF interval data is stored in a cell at the start of the interval. The END is stored as a cell attribute. For variant intervals (such as deletions and gVCF REF blocks), an additional cell is stored at the END value of the variant interval. When queried for a given genomic position, the query library performs an efficient sweep to determine all intervals that intersect with the queried position.
