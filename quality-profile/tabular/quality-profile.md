# Tabular Data Quality Profile

A Tabular Data Quality Profile is a specialized [Quality Profile](https://github.com/Stephen-Gates/data-quality-pattern/blob/master/data-quality-pattern.md#quality-profiles) to describe tabular data resources.

The Quality Profile describes [Measurements](#measurements) and [Metrics](#metrics).

## Measurements
### Measurement file

#### Descriptor

The data resource descriptor that describes the measurement file:

- MUST be a [Tabular Data Resource][tdr]
- MUST be described by the [Measurement Schema](#measurement-schema)
- MAY include a CSV dialect, if required

#### File

The measurement file MUST contain the `fields`:

- `resource_name` - the `name` of the `resource` being measured
- `field_name` - the `name` of the `field` being measured. If null, the metric applies to the whole data resource
- `metric_name` - the `name` of the metric. The `metric-name` has a foreign key relationship with the [Metrics File](#metrics-file) where it is associated with its `measurement-procedure`.
- `measure` - a calculated value for the data resource or `field`, e.g. column-count, row-count, completeness.
- `value` - an actual value from the data resource or `field`, e.g. minimum-value, maximum-value
- `annotation` - A statement by the publisher about the measure or value

Only one of `measure` or `value` should be present in any row.

##### Example

resource_name    |field_name |metric_name   | measure| value              | annotation
-----------------|-----------|--------------|--------|--------------------|-----------
tide-observations|           | row-count    | 1203   |                    |
tide-observations| height    | completeness | 0.95   |                    | Observations lost on 2018-01-18 due to battery failure
tide-observations| height    | minimum-value|        |                0.62|
tide-observations| height    | mean         |        |                0.88|
tide-observations| date-time | minimum-value|        | 2014-01-01T01:02:10|

### Measurement Schema

The Measurement File is described by a [`schema`](/measures/tableschema.json):

> @todo import from file

```javascript
{
  "schema": {
    "fields": [
      {
        "name": "resource-name",
        "description": "The name of the data resource being measured",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "field-name",
        "description": "The name of the field being measured. If null, the metric applies to the whole data resource",
        "type": "string",
        "constraint": {
          "required": true
        }
      },
      {
        "name": "metric-name",
        "description": "The name of the metric",
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
        "fields": "metric-name",
        "reference": {
          "datapackage": "https://github.com/Stephen-Gates/data-quality-pattern/quality-profile/tabular/metrics/datapackage.json",
          "resource": "metrics",
          "fields": "metric-name"
        }
      }
    ]
  }
}
```

### Metrics
#### Metrics File

##### Descriptor

The data resource descriptor that describes the metrics file:

- MUST have the `name`, `"metrics"`
- MUST be a [Tabular Data Resource][tdr]
- MUST be described by the [Metrics Schema](#metrics-schema) within a data package
- MAY include a CSV dialect, if required

##### File

The metrics file MUST be called `metrics.csv`.

The metrics file is described by the [metrics schema](/metrics/datapackage.json) and MUST contain the `fields`:

- `metric-name` - the name of the standard being used to measure a data quality dimension or statistic
- `metric-description` - a brief description of the standard being used to measure a data quality dimension or statistic
- `measurement-procedure` - the steps or formula used to calculate a measurement value for a specific metric

The following table gives examples of statistics that can be computed on a data resource and interpreted as quality measures.

##### Example: Metrics File

| metric-name               | metric-description                                                                                 | measurement-procedure                                 |
|---------------------------|----------------------------------------------------------------------------------------------------|-------------------------------------------------------|
| row-count                 | Total number of rows in the field                                                                  | Need a way to formally specify measurement procedures |
| row-sum                   | Sum of all values in the field                                                                     |                                                       |
| mean                      | Mean (average) of all values in the field                                                          | row-sum / row-count                                   |
| minimum-value             | "Minimum value found - works for number, integer, date, time, datetime, year, yearmonth, duration" |                                                       |
| maximum-value             | "Maximum value found - works for number, integer, date, time, datetime, year, yearmonth, duration" |                                                       |
| completeness              | proportion of required values in the field                                                         | required-count / row-count                            |
| uniqueness                | proportion of unique values in the field                                                           | unique-count /row-count                               |

#### Metrics Schema

The metrics file is described by [`schema`](/metrics/datapackage.json) within the metrics data package.

> @todo import from file

```javascript
{
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

## Copyright and license

(c) Stephen Gates and contributors, 2018

[Tabular Data Quality Profile](https://github.com/Stephen-Gates/data-quality-pattern/blob/master/quality-profile/tabular/quality-profile.md) by [Stephen Gates](https://theodi.org.au/stephen-gates/) and [contributors](https://github.com/Stephen-Gates/data-quality-pattern/graphs/contributors) is licensed under a [Creative Commons Attribution 4.0 International License](https://creativecommons.org/licenses/by/4.0/)

[dp]: http://frictionlessdata.io/specs/data-package/
[tdp]: https://frictionlessdata.io/specs/tabular-data-package/
[dr]: http://frictionlessdata.io/specs/data-resource/
[rs]: https://frictionlessdata.io/specs/data-resource/#resource-schemas
[tdr]: http://frictionlessdata.io/specs/tabular-data-resource/
[ts]: https://frictionlessdata.io/specs/table-schema/
[csvd]: https://frictionlessdata.io/specs/csv-dialect/
