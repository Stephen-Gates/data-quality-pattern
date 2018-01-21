# Data Quality Pattern

#### Language

The key words `MUST`, `MUST NOT`, `REQUIRED`, `SHALL`, `SHALL NOT`, `SHOULD`, `SHOULD NOT`, `RECOMMENDED`, `MAY`, and `OPTIONAL` in this document are to be interpreted as described in [RFC 2119](https://www.ietf.org/rfc/rfc2119.txt).


## Introduction

Data Quality Pattern is a proposed [Frictionless Data Pattern](https://frictionlessdata.io/specs/patterns/).

Data Quality Pattern is a consistent, but flexible, way to describe the data quality measures and statistics of a [Data Resource][dr].

The pattern supports the requirement to:

- associate data with data quality measures and annotations
- associate data quality measures with a set of documented, objective data quality metrics
- support user-defined or domain-specific data quality metrics
- discover data packages and data resources that contain data quality measures
- compare data quality measures across similar types of data

The Data Quality Pattern is implemented by:

- calculating quality measures and statistics for a data resource, and optionally providing annotations
- storing the measurement results in a [measurement file](#measurement-file). The measurement file is defined by its data resource's [`quality`](#quality) property, which includes a [`reference`](#reference) to the data being measured and a [`quality-profile`](#quality-profile)
- the `quality-profile` specifies a [measurement schema](#measurement-schema) and `format` for the measurement file
-  the measurement schema:
   - describes each field in the measurement file
   - references a [metrics file](#metrics-file) that describes each metric's `measurement procedure` (how to calculate the data quality measurement or statistic)
-  the metrics file, and its [metrics schema](#metrics-schema), can describe either:
   - a shared set of standard data quality metrics
   - a user-defined or domain-specific set of data quality metrics
- the data resources that describe the data, its data quality measures, and the metric definitions can be stored in one or more data packages, e.g.:
  1. An automated data quality checker may store each resource in its own data package:
     - leaving the data that was measured, and its data package, unchanged
     - referencing standard metric definitions in a shared data package
  2. A data publisher may store the data and its data quality measures in one data package, and reference metric definitions in a shared data package
  3. the data may be stored in its own data package, and its data quality measures and user-defined metric definitions stored in another data package
  4. all resources may be stored in one data package

### Illustrative Structure

A directory structure for a data package on disk that contains both a data file (`my-data.csv`) and its measurement file (`my-data-quality-measures.csv`) is:

```
|- datapackage.json
|- README.md
|- data
|   |- my-data.csv
|   |- my-data-quality-measures.csv
|
```
The associated data package descriptor (`datapackage.json`) is shown below. Note the `quality` property for the measurement file.

```javascript
{
  "name": "my-data-package",
  "profile": "tabular-data-package",
  "resources": [
    {
      "name": "data-quality-measures",
      "path": "data/my-data-quality-measures.csv",
      "profile": "tabular-data-resource",
      "schema": "https://example.com/tableschema.json",
      "quality": {
        "quality-profile": "tabular",
        "reference": {
            "resource": "my-data-resource"
        }
      }
    },
    {
      "name": "my-data-resource",
      "path": "data/my-data.csv",
      "profile": "tabular-data-resource",
      "schema": {
        "fields": [
          {
            ...  // inline schema omitted from brevity
          }
        ]
      }
    }               
  ]  
}
```

## Specification

### Descriptor

Data quality measures are represented by a specialized [Data Resource][dr] descriptor. The data resource descriptor MUST:

- conform to the [Tabular Data Resource][tdr] specification
- include a `quality` property

### Metadata

#### Required Properties

The `resource` descriptor MUST include a `quality` property.

##### `quality`

The `quality` property supports the requirement to discover data packages and data resources that contain data quality measures and statistics.

The `quality` property MUST be a JSON `object` and MUST include:

- `quality-profile` - determines which [Quality Profile](#quality-profiles) to apply
- `reference` - a reference to the data resource being measured. The data resource may be in the same, or another data package

##### `quality-profile`

`quality-profile` is a `string` identifying the profile that describes a measurement file using a [Table Schema][ts].

This unique identifier MUST be a string and can be in one of two forms, either:

1. an `id` from the official [Quality Profile Registry](https://raw.githubusercontent.com/Stephen-Gates/data-quality-pattern/master/registry/registry.json)
2. a fully-qualified URL that points directly to a Table Schema that conforms with the [Quality Profile](#quality-profiles).

As part of the Frictionless Data Specifications project, we publish quality profiles e.g. the [Tabular Data Quality Profile][tdqp]. Other quality profiles to support spatial and fiscal data are anticipated.

Quality profiles can be specified to support user-defined or domain-specific data quality metrics and statistics. The creation of many different Quality Profiles may inhibit the ability to compare data quality measures and statistics across data resources.

##### `reference`

A `reference` associates the measurement file with the data resource it measures.

`reference`  MUST be a JSON `object`. The object:

- MUST have a  `resource` property which is `string` that identifies the `name` of the data resource in the data package being measured.
- MAY have a `datapackage` property which is a `url` that fully resolves the location of a `datapackage.json` file that  describes an external data package. If the `datapackage` property is omitted or is an empty string, then the location is the current data package.

Example: The data resource being measured is in a separate data package.

```javascript
{
  "quality": {
    "quality-profile": "tabular",
    "reference": {
      "datapackage": "https://example.com/datapackage.json",
      "resource": "resource-name"
    }
  }
}
```

Example: The data resource being measured is in the same data package using a custom quality profile.

```javascript
{
  "quality": {
    "quality-profile": "http://example.com/my-tableschema.json",
    "reference": {
      "resource": "resource-name"
    }
  }
}
```

## Quality Profiles

TO DO

A Quality Profile is a ...

A Quality Profile supports the requirement to:

- associate data with a set of documented, objective  data quality metrics and statistics
- support user-defined or domain-specific data quality metrics and statistics
- compare data quality measures and statistics across data resources

A Quality Profile contains:

- [Measurement File](#measurement-file)
- [Measurement Schema](#measurement-schema)
- [Metrics File](#metrics-file)
- [Metrics Schema](#metrics-schema)

### Measurement File

#### Fields

The data resource descriptor that describes the measurement file MUST be a [Data Resource][dr] described by a [Resource Schema][rs]

The measurement file MUST contain the `fields`:

- `resource-name` - `name` of the data resource being measured
- `field-name` - `name` of the `field` being measured
- `metric-name` - a standard way to measure a quality dimension
- `measure` - the evaluation of a values in a `field` against a specific quality metric or statistic
- annotation - a statement from the publisher commenting about the `quality measure` in markdown or text

The measurement file MAY contain additional `fields`.

#### File and Resource Name

The measurement file SHOULD be called the data resource `name` followed by `_quality-measures` and the appropriate file extension for the file `format`.

For example, for the data resource with `"name": "budget-2018"`, the associated:

- data quality file name should be `budget-2018-quality.csv`
- data resource `name` should be `budget-2018-quality`

### Measurement Schema

TO DO

Schema:
- describe `fields`
- add `foreignKey` link to [Metrics File](#metrics-file)

Example: Tabular Data Measurement Schema

```javascript
{
  "schema": {
    "fields": [
      {
        "name": "resource_name",
        "description": "The name of the data resource being measured",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "field_name",
        "description": "The name of the field being measured. If null, the metric applies to the whole data resource",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "metric_name",
        "description": "The name of the metric.",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "measure",
        "description": "calculated value from the data points in the resource_name, field_name",
        "type": "number"
      },
      {
        "name": "value",
        "description": "actual value of a data point in the resource_name, field_name",
        "type": "any"
      },
      {
        "name": "annotation",
        "description": "A statement by the publisher about the measure or value",
        "type": "string"
      }
    ],
    "foreignKeys": [
      {
        "fields": "metric_name",
        "reference": {
          "datapackage": "https://example.com/datapackage.json"
          "resource": "resource_name",
          "fields": "metric_name"
        }
      }
    ]
  }
}
```

### Metrics File

TO DO

#### Fields

The metrics file MUST contain the `fields`:

- `metric-name` - TO DO
- `metric-description` - TO DO
- `measurement-procedure` - TO DO

The metrics file MAY contain additional `fields`, e.g. from [ISO/IEC 25012](https://www.w3.org/TR/vocab-dqv/#DimensionsOfISOIEC25012):

- dimension - a criteria relevant for assessing quality, e.g. Completeness
- category - a group of quality dimensions in which a common type of information is used as quality indicator, e.g. Inherent Data Quality

#### File and Resource Name

The metrics file SHOULD be called `metrics.csv`.
The data resource `name` SHOULD be called `metrics`.

### Metrics Schema

TO DO

Explanation

```
    "schema": {
      "fields": [{
        "name": "metric-name",
        "type": "string",
        "format": "default",
        "title": "Metric Name",
        "constraints": {
          "required": true,
          "unique": true
        }
      }, {
        "name": "metric-description",
        "type": "string",
        "format": "default",
        "title": "Metric Description",
        "constraints": {
          "required": true
        }
      }, {
        "name": "measurement-procedure",
        "type": "string",
        "format": "default",
        "title": "Measurement Procedure",
        "constraints": {
          "required": true
        }
      }],
      "missingValues": [""]
    },
    "primaryKeys": ["metric-name"]
```

## Implementations

There are no known implementations.

It is envisaged that:
- [Goodtables.io](http://goodtables.io) could be extended to assess a whole data resource and its `fields` to calculate data quality measures. An approach to perform [advanced checks](https://github.com/frictionlessdata/goodtables-py#advanced-checks) has already been implemented
- existing [Frictionless Data software libraries](https://frictionlessdata.io/software/) could assist with implementation

## Appendix: Related Work

Data Quality and Statistics draws content and/or inspiration from, among others, the following specifications and implementations:

* [Frictionless Data Specifications][fds]
* [Data on the Web Best Practices: Data Quality Vocabulary][dqv]
* [Spatial Data Package Investigation](https://research.okfn.org/spatial-data-package-investigation/)

## Copyright and license

(c) Stephen Gates and contributors, 2018

[Data Quality Pattern](https://github.com/Stephen-Gates/data-quality-pattern) by [Stephen Gates](https://theodi.org.au/stephen-gates/) and [contributors](https://github.com/Stephen-Gates/data-quality-pattern/graphs/contributors) is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)


[dqv]: https://www.w3.org/TR/vocab-dqv/
[fds]: https://frictionlessdata.io/specs/
[dp]: http://frictionlessdata.io/specs/data-package/
[tdp]: https://frictionlessdata.io/specs/tabular-data-package/
[dr]: http://frictionlessdata.io/specs/data-resource/
[rs]: https://frictionlessdata.io/specs/data-resource/#resource-schemas
[tdr]: http://frictionlessdata.io/specs/tabular-data-resource/
[ts]: https://frictionlessdata.io/specs/table-schema/
[csvd]: https://frictionlessdata.io/specs/csv-dialect/
[tdqp]: /quality-profile/tabular/quality-profile.md
