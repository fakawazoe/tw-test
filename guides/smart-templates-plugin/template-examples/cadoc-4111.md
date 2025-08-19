---
title: CADOC 4111
deprecated: false
hidden: false
metadata:
  robots: index
---
**Smart Templates** lets you generate XML-based reports that follow the official CADOC structure, exactly as required by the Brazilian Central Bank (BACEN).

This guide walks you through the structure and logic used to generate the **CADOC 4111** report in XML.

> ❗️ Important
>
> These reports follow the **COSIF** standard and must match the XML structure defined by BACEN. You can adapt the logic to your own data model, but the XML format must be respected.

<br />

# What is CADOC 4011?

***

The **CADOC 4111** is used to submit **daily information about accounting balances**, including assets, liabilities, and memorandum accounts, as defined in items I and III of Article 2 of **BCB Resolution No. 208**, dated March 22, 2022, and **BCB Normative Instruction No. 524**, dated September 18, 2024.

You’ll use **Smart Templates** to generate this report in XML, using your own data.

<br />

# CADOC structure

***

## Base format

The CADOC report **must** be an XML file and **must** follow the structure defined by **BACEN**:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<documento codigoDocumento="4111" cnpj="99999999" dataBase="AAAA-MM" tipoRemessa="I">
<contas>
<conta codigoConta="1000000009" saldoDia="99.99" />
<conta codigoConta="1100000002" saldoDia="99.99" />
</contas>
</documento>
```

<br />

### Mandatory fields

These fields are required and must be included:

#### `<?xml version="1.0" encoding="UTF-8"?>`

Always starts the file. It defines the XML version and encoding so the system knows how to read the content.

#### `<documento>` tag

Wraps the entire CADOC structure and includes:

* `codigoDocumento`: Identifies the type of report which is `4111`
* `cnpj`:  Identifies the financial institution.
  * Use only the first 8 digits of the *CNPJ* (numbers only — no punctuation).
* `dataBase`: Reporting period, in the `"YYYY-MM"` format.
  * Example: `"2025-01"` means January 2025.
* `tipoRemessa`: Defines the report type:
  * `"I"` (Inclusion): First submission for that period.
  * `"S"` (Replacement): Update or correction of previously accepted data.

> ❗️ Important
>
> If your first submission was rejected due to errors, you still need to use `"I"` on your next attempt. Only use `"S"` for replacing previously approved data.

#### `<contas>` tag

Groups all account entries for the reporting period.

#### `<conta>` tag

* `codigoConta`: Account code, following COSIF format.
* `saldo`: Account balance in decimal format (two decimal places).

<br />

# Using Smart Templates

***

Each organization can adapt the template to fit its own data model, just replace the placeholder names (like `midaz_onboarding.account`, `midaz_transaction.balance`, etc.) to match your structure. The logic stays the same, only the field paths and names change.

Here’s a working example you can use as a starting point:

```xml tpl
<documento codigoDocumento="4111"
           cnpj={{ midaz_onboarding.organization.0.legal_document|slice:':8' }}
           dataBase={% date_time "YYYY/MM" %}
           tipoRemessa="I">
  <contas>
    {%- for account in midaz_onboarding.account %}
      {%- with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}
        {%- set saldoDia = balance.available %}
        <conta codigoConta="{{ account.id|slice:':10' }}" saldo="{{ saldoDia|floatformat:2 }}"/>
      {% endwith %}
    {% endfor %}
  </contas>
</documento>
```

<br />

## Code breakdown

### `<documento ...>`

```xml tpl
<documento codigoDocumento="4111"
          cnpj={{ midaz_onboarding.organization.0.legal_document|slice:':8' }}
          dataBase={% date_time "YYYY/MM" %}
          tipoRemessa="I">
```

Defines the top-level document and required metadata.

#### `codigoDocumento="4111"`

Tells **BACEN** that you're sending a `4111` report.

#### `cnpj={{ midaz_onboarding.organization.0.legal_document|slice:':8' }}`

Grabs the first 8 digits of the organization's CNPJ.

* `midaz_onboarding.organization.0.legal_document`: Grabs the CNPJ from the first organization in the list.
* `slice:':8'`: Trims it to the first 8 digits.

#### `dataBase={% date_time "YYYY/MM" %}`

Sets the reporting period dynamically.

* `{% date_time "YYYY/MM" %}`: Prints the current year and month at render time.

#### `tipoRemessa="I"`

Indicates this is a first-time submission (`I`) or a correction (`S`).

<br />

### Account information

```xml tpl
    {%- for account in midaz_onboarding.account %}
      {%- with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}
        {%- set saldoDia = balance.available %}
```

#### `{% for account in midaz_onboarding.account %}`

Loops through all accounts associated with the `midaz_onboarding.account` list.

* For each iteration, it creates a new scope where `account` refers to one specific item in that list.

#### `{% with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}`

Inside the loop:

* Filters the `midaz_transaction.balance` by `account_id` to match the current account's ID.
* `[0]`: Grabs the first matching balance (assuming there’s only one per account).
* Stores it as a temporary variable `balance`.

#### `{%- set saldoDia = balance.available %}`

* Retrieves the balance and store it as `saldoDia`.

<br />

### Outputting each `<conta />` tag

```xml tpl
<conta codigoConta="{{ account.id|slice:':10' }}" saldo="{{ saldoDia|floatformat:2 }}"/>
```

Creates one `<conta>` element per account, with two attributes:

* `codigoConta="{{ account.id|slice:':10' }}"`: Uses the first 10 characters of the account ID; useful if the full ID is too long or the integration expects a fixed length.
* `saldo="{{ saldoDia|floatformat:2 }}"`: Formats the balance with exactly two decimal places.

> ❗️ Important
>
> Always apply scaling before rendering numeric values to ensure accuracy. Use `floatformat:2` to enforce two decimal places.

<br />

### Closing the template

```xml tpl
  </contas>
</documento>
```

Wraps up the account list and document structure.

<br />

# Rendered example

***

Here’s how the final XML might look after rendering:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<documento codigoDocumento="4111" cnpj= 78425230 dataBase=2025/06 tipoRemessa="I" >
	<contas>
		<conta codigoConta="0196d9758" saldoDia= "-2500000.00" />
		<conta codigoConta="0196d9720" saldoDia= "499950.00" />
		<conta codigoConta="0196d9761" saldoDia= "500050.00" />
		<conta codigoConta="0196d9779" saldoDia= "500000.00" />
		<conta codigoConta="0196d9797" saldoDia= "500000.00" />
		<conta codigoConta="0196d9725" saldoDia= "500000.00" />
		</contas>
	</documento>
```

<br />

# Customizing the template

***

Feel free to adjust the placeholder names (like `midaz_onboarding.account`) to match your own data model. Just make sure to preserve the XML structure, that’s what ensures BACEN will accept your file.

> 🚧 Attention
>
> Always validate the rendered XML against BACEN’s schema before submitting.\
> The structure alone isn’t enough, the data must reflect your institution’s actual ledger.