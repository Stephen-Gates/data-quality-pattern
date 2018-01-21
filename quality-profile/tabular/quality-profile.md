# Tabular Data Quality Profile

A Tabular Data Quality Profile is a specialized [Quality Profile](https://github.com/Stephen-Gates/data-quality-measures/quality-profile/tabular-data/quality-profile.md) to describe tabular data resources.

Like any Quality Profile, it contains a:

- Measurement File
- Measurement Schema
- Metrics File
- Metrics Schema

## Measurement File

**Descriptor**

The data resource descriptor that describes the measurement file:

- MUST be a [Tabular Data Resource][tdr]
- MUST be described by the [Measurement Schema](#measurement-schema)
- MAY include a CSV dialect, if required

**File**

The measurement file contains

- `resource_name` - the `name` of the `resource` being measured
- `field_name` - the `name` of the `field` being measured. If null, the metric applies to the whole data resource
- `metric_name` - the `name` of the metric. The associated `category`, `dimension` and `measurement_process` is available at https://example.com/something.extension.

:::warning
Link `metric_name` in **measurement file** schema to `metric_name` in **metric file** schema using `foreignKey`. **metric file** could be a data resources in the same or another data package.
:::

- `measure` - a calculated value for the data resource or `field`, e.g. column-count, row-count, completeness.
- `value` - an actual value from the data resource or `field`, e.g. minimum-value, maximum-value
- `annotation` - A statement by the publisher about the measure or value

The `metric_name` has a foreign key relationship with the [Data Quality Model](#data-quality-model-tqp) where it is associated with its `measurement_procedure`, `dimension` and other related properties.

Only one of `measure` and `value` should be present in any row.

:::warning
Could replace `measure` and `value` with to `measure` and `type`, e.g.

- a `minimum` metric on a `date` column would return `2017-03-31`, `date`
- a `minimum` metric on a `integer` column would return `45`, `integer`

While this is arguably "cleaner", it prevents the table being validated using the exist Frictionless Data libraries and the Table Schema

:::

Example

resource_name    |field_name |metric_name  | measure| value              | annotation
-----------------|-----------|-------------|--------|--------------------|-----------
tide-observations|           | row-count   | 1203   |                    |
tide-observations| height    | completeness| 0.95   |                    | Observations lost on 2018-01-18 due to battery failure
tide-observations| height    | minimum     |        |                0.62|
tide-observations| height    | mean        |        |                0.88|
tide-observations| date-time | minimum     |        | 2014-01-01T01:02:10|

### Measurement Schema

The `schema` that describes the Data Quality File for the  Tabular Quality Profile is available at https://example.com/tabular-quality-profile/tableschema.json and is shown below:

:::warning
- Should these quality profile table schemas be stored centrally?

- Add `foreignKey` to external data package containing the Quality Framework

- Should this specification list the schema below or provide it at a URL?
:::

Example: Reference to Tabular Data Measurement Schema

```javascript=
"schema": "https://example.com/tabular-quality-profile/tableschema.json"
```

Example: Tabular Data Measurement Schema

```javascript=
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
        "description": "The name of the metric. Associated Category, Dimension and Measurement Process available at https://example.com/something.extension",
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
          "datapackage": "https://example.com/datapackage.json",
          "resource": "data_resource_name",
          "fields": "metric_name"
        }
      }
    ]
  }
}
```

### Metrics File

**Descriptor**

**File**

External Tabular Data Package published at github/datahub.io (can cache for local processing)

Contains:

- `metric-name`
- `metric-description`
- `measurement-procedure`
- `dimension-name` *(refer to a list?)*
- `category-name` *(refer to a list?)*

A category can have many dimensions ==*(drop?)*==
A dimension can have many metrics
A metric has a description and measurement procedure

The following table gives examples of statistics that can be computed on a data resource and interpreted as quality measures

Example

category-name | dimension-name | dimension-definition |metric-name |metric-description |measurement-procedure
--------------|----------------|----------------------|------------|-------------------|-----------
Inherent Data Quality|Completeness|The degree to which subject data associated with an entity has values for all expected attributes and related entity instances in a specific context of use.|Optional Completeness|  | url to equation?

### Metrics Schema

- requirements met


:::warning
Make a snippet. Put it in the Repo
:::

```javascript=
{
  "schema": {
    "fields": [
      {
        "name": "category_name",
        "description": "The name of the data resource being measured",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "dimension_name",
        "description": "The name of the field being measured. If null, the metric applies to the whole data resource",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "metric_name",
        "description": "The name of the metric. Associated Category, Dimension and Measurement Process available at https://example.com/something.extension",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "measurement_procedure",
        "description": "",
        "type": "string"
      },
      {
        "name": "metric_description",
        "description": "",
        "type": "string"
      }
    ],
    "foreignKeys": [
      {
        "fields": "metric_name",
        "reference": {
          "datapackage": "https://example.com/datapackage.json"
          "resource": "data_resource_name",
          "fields": "metric_name"
        }
      }
    ]
  }
}
```

[dp]: http://frictionlessdata.io/specs/data-package/
[tdp]: https://frictionlessdata.io/specs/tabular-data-package/
[dr]: http://frictionlessdata.io/specs/data-resource/
[rs]: https://frictionlessdata.io/specs/data-resource/#resource-schemas
[tdr]: http://frictionlessdata.io/specs/data-resource/
[ts]: https://frictionlessdata.io/specs/table-schema/
[csvd]: https://frictionlessdata.io/specs/csv-dialect/

## Copyright

(c) Stephen Gates, 2018

[Tabular Data Quality Profile](https://github.com/Stephen-Gates/data-quality-measures/specs/tabular-data-quality-profile.md) by [Stephen Gates](https://theodi.org.au/stephen-gates/) is licensed under [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/).
