# The following information can be used for Data encoding

The datatypes are represented by a byte identified according to TDS protocol.

While sending the data to sql server, a Name needs to be used for each of these. Reference Microsoft.Data.SqlClient



| TdsType Value | MetaType Name String | MetaType Name       |
|---------------|----------------------|---------------------|
| `0x7F`        | "bigint"             | `BIGINT`            |
| `0x3E`        | "float"              | `FLOAT`             |
| `0x3B`        | "real"               | `REAL`              |
| `0xAD`        | "binary"             | `BINARY`            |
| `0xA5`        | "varbinary"          | `VARBINARY`         |
| `0x22`        | "image"              | `IMAGE`             |
| `0x32`        | "bit"                | `BIT`               |
| `0x30`        | "tinyint"            | `TINYINT`           |
| `0x34`        | "smallint"           | `SMALLINT`          |
| `0x38`        | "int"                | `INT`               |
| `0xAF`        | "char"               | `CHAR`              |
| `0xA7`        | "varchar"            | `VARCHAR`           |
| `0x23`        | "text"               | `TEXT`              |
| `0xEF`        | "nchar"              | `NCHAR`             |
| `0xE7`        | "nvarchar"           | `NVARCHAR`          |
| `0x63`        | "ntext"              | `NTEXT`             |
| `0x6C`        | "decimal"            | `DECIMAL`           |
| `0xF1`        | "xml"                | `XML`               |
| `0x3A`        | "datetime"           | `DATETIME`          |
| `0x3B`        | "smalldatetime"      | `SMALLDATETIME`     |
| `0x3C`        | "money"              | `MONEY`             |
| `0x7A`        | "smallmoney"         | `SMALLMONEY`        |
| `0x24`        | "uniqueidentifier"   | `ROWGUID`           |
| `0x62`        | "sql_variant"        | `VARIANT`           |
| `0xF0`        | "udt"                | `UDT`               |
| `0xF3`        | "table"              | `TABLE`             |
| `0x28`        | "date"               | `DATE`              |
| `0x29`        | "time"               | `TIME`              |
| `0x2A`        | "datetime2"          | `DATETIME2`         |
| `0x2B`        | "datetimeoffset"     | `DATETIMEOFFSET`    |
| `0xF2`        | "json"               | `JSON`              |

This table provides a detailed mapping of TDS types to their corresponding `MetaType` names and string representations. If you need further details or have specific questions, feel free to ask!