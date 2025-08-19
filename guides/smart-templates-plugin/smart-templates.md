---
title: Overview
excerpt: ''
deprecated: false
hidden: false
metadata:
  title: ''
  description: ''
  robots: index
next:
  description: ''
---
The **Smart Templates** plugin was created to help Midaz Ledger users generate dynamic, data-driven reports using plain-text templates (`.tpl` files). It interprets these templates and renders reports in CSV, XML, HTML, or TXT, always matching the structure defined in the original file.

<br />

# Why use the Smart Templates plugin?

***

Instead of writing complex queries, you can use **simple placeholders** to reference domains, tables, and fields directly. It’s a fast and flexible way to build reports that stay up to date, even when your data model evolves.

## What it can do

* **Dynamic queries with placeholders**: Templates use simple placeholders to pull data directly from the database; no SQL is required.
* **Loop and condition logic**: Add loops and conditions to handle more complex scenarios without complicating things.
* **Async processing**: Large reports are processed asynchronously so that they won’t interfere with your live workloads.
* **Multiple output formats:** Generate reports in CSV, XML, HTML, or TXT — whichever fits your use case.

## Why it matters

* **No code required**: Create powerful reports with simple templates.
* **Flexible**: Your templates adapt automatically as the data model evolves.
* **Optimized performance**: Runs on a replicated read database to avoid slowdowns.

# Template model

***

The **Smart Templates** plugin reads template files that follow the structure of the final output, whether it’s CSV, XML, HTML, or TXT. The generated report will match this structure exactly.

### Example

* CSV-structured template = CSV output.
* XML-structured template = XML output.
* HTML-structured template = HTML output.
* TXT-structured template = TXT output.

> ❗️ Important
>
> Even though the file content must follow the output format, make sure to save it with a `.tpl` extension. **This is required for the template to work properly.**

<br />

## Setting up your environment

Before starting, you **must** rename the databases used as references for searching for information while rendering reports. This configuration must be done in the project's `.env` file.

By defining unique and clear names for each database, we avoid conflicts, especially in cases where different databases have tables with the same names, and we ensure better fluidity in queries.

### Example

If you are using a database called `onboarding`, we recommend renaming it to something more descriptive, such as:

* `midaz_onboarding` (PostgreSQL).
* `midaz_onboarding_metadata` (MongoDB).

<br />

## Using placeholders

Templates use placeholders to fetch data dynamically. A placeholder points to a field inside a table or a document collection, and it follows this structure:

```tpl
{{ base.table_or_collection.field_or_document }}
```

This format works for SQL databases (tables) and MongoDB (collections), giving you full flexibility regardless of your data model.

<br />

# Building templates

***

You can enhance your templates with **conditionals**, **math operators**, and **temporary variables**, making them more dynamic and flexible.

## Common blocks

### Loop

Iterates through a set of data and renders values inside the block. While it may look like a conditional, it's a repetition structure.

**Example**

```tpl
{% for <condition> %}
....
{% endfor %}
```

### Simple condition

Checks a logical rule. If it's true, the block is executed.

**Example**

```tpl
{% if value_a == value_b %}
...
{% endif %}
```

### Temporary scope

Creates a temporary variable inside the block. This makes templates easier to read and avoids repeating long paths (like accessing the same object or field multiple times).

**Example**

```tpl
{% with ... %}
...
{% endwith %}
```

### Value formatting

Allows you to display numbers with a fixed number of decimal places, perfect for keeping financial values clear and consistent.

* Add a number after the colon to define the decimal precision. For example, `:2` shows two decimal places: `123.45`.

**Example**

```
{{{placeholder}}|floatformat:2}
```

<br />

## Conditional blocks

| Block                 | Description                                     | Example                                                |
| --------------------- | ----------------------------------------------- | ------------------------------------------------------ |
| If                    | Runs a block if the condition is true.          | `{% if condition %}...{% endif %}`                     |
| If-else               | Runs one block if true, another if false.       | `{% if condition %}...{% else %}...{% endif %}`        |
| If - else - if        | Allows multiple checks within the same block.   | `{% if a %}...{% elif b %}...{% else %}...{% endif %}` |
| Equal                 | Checks if two values are equal.                 | `{% if a == b %}`                                      |
| Not equal             | Checks if two values are different.             | `{% if a != b %}`                                      |
| Greater than          | Checks if a is greater than b.                  | `{% if a > b %}`                                       |
| Less than             | Checks if a is less than b.                     | `{% if a < b %}`                                       |
| Greater than or equal | Checks if a is greater than or equal to b.      | `{% if a >= b %}`                                      |
| Less than or equal    | Checks if a is less than or equal to b.         | `{% if a <= b %}`                                      |
| And                   | Returns true if both conditions are true.       | `{% if a and b %}`                                     |
| Or                    | Returns true if at least one condition is true. | `{% if a or b %}`                                      |
| Not                   | Inverts the Boolean result of a condition.      | `{% if not a %}`                                       |

<br />

## Operators and filters

You can combine logical conditions with math operations to define rules, calculate values, and format results with precision.

**Example: Calculating Total Sum with Filter**

```html
<Sum>
  {% sum_by transaction.operation by "amount" if accountAlias != "@external/BRL" %}
</Sum>
```

Assuming the following data:

```json
"transaction": {
  "operation": [
    { "value": "10.00", "accountAlias": "@user/BRL" },
    { "value": "5.00", "accountAlias": "@external/BRL" },
    { "value": "25.00", "accountAlias": "@user/BRL" }
  ]
}
```

The output will be:

```html
<Sum>35.00</Sum>
```

### Other functions

#### Count

```html
<Count>
  {% count_by transaction.operation if accountAlias != "@external/BRL" %}
</Count>
```

#### Average

```html
<Average>
  {% avg_by transaction.operation by "amount" if accountAlias != "@external/BRL" %}
</Average>
```

#### Minimum

```html
<Minimum>
  {% min_by transaction.operation by "amount" if accountAlias != "@external/BRL" %}
</Minimum>
```

#### Maximum

```html
<Maximum>
  {% max_by transaction.operation by "amount" if accountAlias != "@external/BRL" %}
</Maximum>
```

#### Percentage

Let’s say the user is generating a monthly expense report and wants to show how much of the total was spent on "Food":

```html
<Percentage>
  {{ category.amount | percent_of: total.expenses }}
</Percentage>
```

**If:**

* `category.amount = "6.00"`
* `total.expenses = "20.00"`

**Output:**

```html
<Percentage>
  30.00%
</Percentage>
```

#### Date

Applies the current date when rendering the template.

You can format it however you need (`ddMMyyyy`, `yyyyMMdd`, and more). Time can be included too, like in the example below.

> 📘 Note
>
> The time is generated in UTC (Coordinated Universal Time), without regional time zones or daylight saving adjustments.

**Input:**

```html
{% date_time "ddMMYYYY HH:mm"}
```

**Output:**

```html
13-05-2025 21:15
```

#### Contains

Use this filter to check if one value is **partially** included in another,  even if they’re not identical. It’s beneficial when your data includes dynamic prefixes or suffixes, like in account aliases.

```jinja
{% if contains(midaz_transaction.transaction.body.source.from.accountAlias, midaz_onboarding.account.alias) %}
```

**Example:**

* `midaz_transaction.transaction.body.source.from.account_alias = 0#@external/BRL`
* `midaz_onboarding.account.alias = @external/BRL`

Even though the strings don’t match exactly, `contains` will return `true` because `@external/BRL` is inside `0#@external/BRL`.

<br />

### Math operations

Allows you to perform calculations on exported data.

| Keyword                        | Description                                   |
| :----------------------------- | :-------------------------------------------- |
| `sum_by`                       | Sum values.                                   |
| `-`                            | Subtract.                                     |
| `*`                            | Multiply.                                     |
| `/`                            | Divide.                                       |
| `avg_by`                       | Calculate average.                            |
| `min_by`                       | Minimum value.                                |
| `max_by`                       | Maximum value.                                |
| `percent_of`                   | Calculates percentage.                        |
| `filter(list, "field", value)` | Filters lists as if it were a `WHERE` clause. |

# Security recommendations

***

Security is foundational when working with Lerian plugins. **Before deploying any plugin into your environment, we strongly recommend reviewing the[Security recommendations](doc:security-recommendations)**. Whether you're working with Access Manager, Fee Engine, CRM, or Smart Templates, each plugin should be implemented in alignment with security best practices — including securing network boundaries, managing secrets, applying patch management, and enforcing strict access controls.

By following these guidelines, you help ensure every plugin runs securely, integrates smoothly with your infrastructure, and maintains compliance, data integrity, and user trust.

# Need inspiration?

***

Check out the [Template examples](doc:template-examples) page to explore what you can do and start shaping your own template.