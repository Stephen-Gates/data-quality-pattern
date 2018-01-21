Example Data Package demonstrating the draft [Data Quality Pattern](https://github.com/Stephen-Gates/data-quality-pattern/data-quality-pattern.md)

## Data

This data package contains two data resources:

1. my-data.csv - some sample data
2. my-data-quality-measures.csv - a file has measures against some data quality metrics for my-data.csv

The datapackage.json file describes these files in `resources`. Each file as described by a `schema`.

The metrics are in another [data package](https://github.com/Stephen-Gates/data-quality-measures/quality-profile/tabular/metrics/datapackage.json) located via the [registry](https://github.com/Stephen-Gates/data-quality-measures/quality-profile/registry/registry.json) using the `tabular` identifier.

## Preparation

The data and data packages where produced with Data Curator.

The datapackage.json was then hand-crafted to comply with the draft specification and validated using http://jsoneditoronline.org

## License

This data and the data package are licensed under [CC0 1.0](https://creativecommons.org/publicdomain/zero/1.0/).
