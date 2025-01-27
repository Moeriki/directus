# Filter Rules

> Permissions, validation, and the API's `filter` parameter all rely on a specific JSON structure to define their rules.
> This page describes the syntax for creating flat, relational, or complex filter rules.

[[toc]]

## Syntax

- **Field** — Any valid root field, [relational field](/reference/filter-rules#relational), or
  [logical operator](/reference/filter-rules#logical-operators)
- **Operator** — Any valid [API operator](/reference/filter-rules#supported-operators) prefaced with an underscore
- **Value** — Any valid static value, or [dynamic variable](/reference/filter-rules#dynamic-variables)

```
{
	<field>: {
		<operator>: <value>
	}
}
```

### Examples

```json
{
	"title": {
		"_contains": "Directus"
	}
}
```

```json
{
	"owner": {
		"_eq": "$CURRENT_USER"
	}
}
```

```json
{
	"datetime": {
		"_lte": "$NOW"
	}
}
```

## Filter Operators

| Operator        | Description                           |
| --------------- | ------------------------------------- |
| `_eq`           | Equal to                              |
| `_neq`          | Not equal to                          |
| `_lt`           | Less than                             |
| `_lte`          | Less than or equal to                 |
| `_gt`           | Greater than                          |
| `_gte`          | Greater than or equal to              |
| `_in`           | Matches any of the values             |
| `_nin`          | Doesn't match any of the values       |
| `_null`         | Is `null`                             |
| `_nnull`        | Is not `null`                         |
| `_contains`     | Contains the substring                |
| `_ncontains`    | Doesn't contain the substring         |
| `_starts_with`  | Starts with                           |
| `_nstarts_with` | Doesn't start with                    |
| `_ends_with`    | Ends with                             |
| `_nends_with`   | Doesn't end with                      |
| `_between`      | Is between two values (inclusive)     |
| `_nbetween`     | Is not between two values (inclusive) |
| `_empty`        | Is empty (`null` or falsy)            |
| `_nempty`       | Is not empty (`null` or falsy)        |

The following operators are **only available in validation permissions**:

| Operator                | Description               |
| ----------------------- | ------------------------- |
| `_submitted`            | Field has to be submitted |
| `_regex` <sup>[1]</sup> | Field has to match regex  |

<sup>[1]</sup> JavaScript "flavor" regex. Make sure to escape backslashes.

## Relational

You can target related values by nesting field names. For example, if you have a relational Many-to-One `author` field,
you can set a rule for the `author.name` field using the following syntax.

```json
{
	"author": {
		"name": {
			"_eq": "Rijk van Zanten"
		}
	}
}
```

## Logical Operators

You can nest or group multiple rules using the `_and` or `_or` logical operators. Each operator holds an array of rules,
allowing for more complex filtering.

```json
{
	"_or": [
		{
			"_and": [
				{
					"owner": {
						"_eq": "$CURRENT_USER"
					}
				},
				{
					"status": {
						"_in": ["published", "draft"]
					}
				}
			]
		},
		{
			"_and": [
				{
					"owner": {
						"_neq": "$CURRENT_USER"
					}
				},
				{
					"status": {
						"_in": ["published"]
					}
				}
			]
		}
	]
}
```

## Dynamic Variables

In addition to static values, you can also filter against _dynamic_ values using the following variables.

- `$CURRENT_USER` — The primary key of the currently authenticated user
- `$CURRENT_ROLE` — The primary key of the role for the currently authenticated user
- `$NOW` — The current timestamp
- `$NOW(<adjustment>)` - The current timestamp plus/minus a given distance, for example `$NOW(-1 year)`,
  `$NOW(+2 hours)`
