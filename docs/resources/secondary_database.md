---
page_title: "snowflake_secondary_database Resource - terraform-provider-snowflake"
subcategory: ""
description: |-
  A secondary database creates a replica of an existing primary database (i.e. a secondary database). For more information about database replication, see Introduction to database replication across multiple accounts https://docs.snowflake.com/en/user-guide/db-replication-intro.
---

# snowflake_secondary_database (Resource)

A secondary database creates a replica of an existing primary database (i.e. a secondary database). For more information about database replication, see [Introduction to database replication across multiple accounts](https://docs.snowflake.com/en/user-guide/db-replication-intro).

## Example Usage

```terraform
# 1. Preparing primary database
resource "snowflake_database" "primary" {
  provider = primary_account # notice the provider fields
  name     = "database_name"
  replication_configuration {
    accounts             = ["<secondary_account_organization_name>.<secondary_account_name>"]
    ignore_edition_check = true
  }
}

# 2. Creating secondary database
resource "snowflake_secondary_database" "test" {
  provider      = secondary_account
  name          = snowflake_database.primary.name # It's recommended to give a secondary database the same name as its primary database
  as_replica_of = "<primary_account_organization_name>.<primary_account_name>.${snowflake_database.primary.name}"
  is_transient  = false

  data_retention_time_in_days {
    value = 10
  }

  max_data_extension_time_in_days {
    value = 20
  }

  external_volume              = "external_volume_name"
  catalog                      = "catalog_name"
  replace_invalid_characters   = false
  default_ddl_collation        = "en_US"
  storage_serialization_policy = "OPTIMIZED"
  log_level                    = "OFF"
  trace_level                  = "OFF"
  comment                      = "A secondary database"
}
```

<!-- schema generated by tfplugindocs -->
## Schema

### Required

- `as_replica_of` (String) A fully qualified path to a database to create a replica from. A fully qualified path follows the format of `"<organization_name>"."<account_name>"."<database_name>"`.
- `name` (String) Specifies the identifier for the database; must be unique for your account. As a best practice for [Database Replication and Failover](https://docs.snowflake.com/en/user-guide/db-replication-intro), it is recommended to give each secondary database the same name as its primary database. This practice supports referencing fully-qualified objects (i.e. '<db>.<schema>.<object>') by other objects in the same database, such as querying a fully-qualified table name in a view. If a secondary database has a different name from the primary database, then these object references would break in the secondary database.

### Optional

- `catalog` (String) The database parameter that specifies the default catalog to use for Iceberg tables.
- `comment` (String) Specifies a comment for the database.
- `data_retention_time_in_days` (Block List, Max: 1) Specifies the number of days for which Time Travel actions (CLONE and UNDROP) can be performed on the database, as well as specifying the default Time Travel retention time for all schemas created in the database. For more details, see [Understanding & Using Time Travel](https://docs.snowflake.com/en/user-guide/data-time-travel). (see [below for nested schema](#nestedblock--data_retention_time_in_days))
- `default_ddl_collation` (String) Specifies a default collation specification for all schemas and tables added to the database. It can be overridden on schema or table level. For more information, see [collation specification](https://docs.snowflake.com/en/sql-reference/collation#label-collation-specification).
- `external_volume` (String) The database parameter that specifies the default external volume to use for Iceberg tables.
- `is_transient` (Boolean) Specifies the database as transient. Transient databases do not have a Fail-safe period so they do not incur additional storage costs once they leave Time Travel; however, this means they are also not protected by Fail-safe in the event of a data loss.
- `log_level` (String) Specifies the severity level of messages that should be ingested and made available in the active event table. Valid options are: [TRACE DEBUG INFO WARN ERROR FATAL OFF]. Messages at the specified level (and at more severe levels) are ingested. For more information, see [LOG_LEVEL](https://docs.snowflake.com/en/sql-reference/parameters.html#label-log-level).
- `max_data_extension_time_in_days` (Block List, Max: 1) Object parameter that specifies the maximum number of days for which Snowflake can extend the data retention period for tables in the database to prevent streams on the tables from becoming stale. For a detailed description of this parameter, see [MAX_DATA_EXTENSION_TIME_IN_DAYS](https://docs.snowflake.com/en/sql-reference/parameters.html#label-max-data-extension-time-in-days). (see [below for nested schema](#nestedblock--max_data_extension_time_in_days))
- `replace_invalid_characters` (Boolean) Specifies whether to replace invalid UTF-8 characters with the Unicode replacement character (�) in query results for an Iceberg table. You can only set this parameter for tables that use an external Iceberg catalog.
- `storage_serialization_policy` (String) Specifies the storage serialization policy for Iceberg tables that use Snowflake as the catalog. Valid options are: [COMPATIBLE OPTIMIZED]. COMPATIBLE: Snowflake performs encoding and compression of data files that ensures interoperability with third-party compute engines. OPTIMIZED: Snowflake performs encoding and compression of data files that ensures the best table performance within Snowflake.
- `trace_level` (String) Controls how trace events are ingested into the event table. Valid options are: [ALWAYS ON_EVENT OFF]. For information about levels, see [TRACE_LEVEL](https://docs.snowflake.com/en/sql-reference/parameters.html#label-trace-level).

### Read-Only

- `id` (String) The ID of this resource.

<a id="nestedblock--data_retention_time_in_days"></a>
### Nested Schema for `data_retention_time_in_days`

Required:

- `value` (Number)


<a id="nestedblock--max_data_extension_time_in_days"></a>
### Nested Schema for `max_data_extension_time_in_days`

Required:

- `value` (Number)

## Import

Import is supported using the following syntax:

```shell
terraform import snowflake_secondary_database.example 'secondary_database_name'
```
