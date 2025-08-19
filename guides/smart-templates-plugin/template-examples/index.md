---
title: Template examples
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
The **Smart Templates** plugin supports multiple output formats, so you can generate exactly the file type you need, whether it’s HTML, XML, CSV, or plain text.

On this page, you’ll find practical examples of how to structure your `.tpl` files for each format. These examples are a great starting point to help you design templates that fit your use case, follow formatting rules, and get parsed correctly during generation.

> ❗️ Important
>
> The file content **must** follow the output format you need (HTML, XML, CSV, or TXT), but make sure to save it with a `.tpl` extension. **This is required for the template to work properly.**

<br />

# HTML template - Analytical financial report

***

This example shows how to structure an **Analytical Financial Report** focused on account-level transaction analysis. The report includes:

* Organization and Ledger information.
* Per-account breakdown.
* Detailed operation list.

## Template code

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Analytical Financial Report</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f4f6f9;
      color: #333;
      padding: 40px;
    }

    .container {
      max-width: 900px;
      margin: auto;
      background: #ffffff;
      padding: 30px 40px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    h1 {
      text-align: center;
      font-size: 24px;
      color: #004080;
      margin-bottom: 10px;
    }

    .subtitle {
      text-align: center;
      font-size: 14px;
      color: #777;
      margin-bottom: 30px;
    }

    .section-title {
      font-weight: bold;
      font-size: 16px;
      margin-top: 30px;
      margin-bottom: 10px;
      color: #004080;
      border-bottom: 1px solid #ccc;
      padding-bottom: 4px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }

    th, td {
      padding: 10px;
      text-align: left;
      border: 1px solid #ccc;
    }

    th {
      background-color: #e8f0fe;
      font-weight: bold;
      color: #003366;
    }

    .info p {
      margin: 4px 0;
    }

    .footer {
      margin-top: 40px;
      text-align: center;
      font-size: 12px;
      color: #999;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Analytical Financial Report</h1>
    <div class="subtitle">Detailed analysis of movements by linked accounts.</div>

    <div class="info">
      <p><strong>Generation Date:</strong> {% date_time "dd/MM/YYYY HH:mm" %}</p>
      <p><strong>Organization:</strong> {{ midaz_onboarding.organization.0.legal_name }}</p>
      <p><strong>Ledger:</strong> {{ midaz_onboarding.ledger.0.name }}</p>
    </div>

    {% for account in midaz_onboarding.account %}
      {% with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}
        <div class="section-title">Account: {{ account.alias }}</div>
        <div class="info">
          <p><strong>ID:</strong> {{ account.id }}</p>
          <p><strong>Currency:</strong> {{ balance.asset_code }}</p>
          <p><strong>Current Balance:</strong> {{ balance.available }}</p>
        </div>

        <table>
          <thead>
            <tr>
              <th>Operation ID</th>
              <th>Type</th>
              <th>Original Amount</th>
              <th>Discount (3%)</th>
              <th>Final Amount</th>
              <th>Description</th>
            </tr>
          </thead>
          <tbody>
            {% for operation in midaz_transaction.operation %}
              {% if operation.account_id == account.id %}
                {% set original_amount = operation.amount %}
                {% set discount_amount = original_amount * 0.03  %}
                {% set final_amount = original_amount - discount_amount %}
                <tr>
                  <td>{{ operation.id }}</td>
                  <td>{{ operation.type }}</td>
                  <td>{{ original_amount|floatformat:2 }}</td>
                  <td>{{ discount_amount|floatformat:2 }}</td>
                  <td>{{ final_amount|floatformat:2 }}</td>
                  <td>{{ operation.description }}</td>
                </tr>
              {% endif %}
            {% endfor %}
          </tbody>
        </table>
      {% endwith %}
    {% endfor %}

    <div class="footer">
      Document generated automatically via Smart Templates - Lerian · {{ midaz_onboarding.organization.0.legal_document }}
    </div>
  </div>
</body>
</html>
```

<br />

## Code breakdown

### Organization and Ledger information

```html
    <div class="info">
      <p><strong>Generation Date:</strong> {% date_time "dd/MM/YYYY HH:mm" %}</p>
      <p><strong>Organization:</strong> {{ midaz_onboarding.organization.0.legal_name }}</p>
      <p><strong>Ledger:</strong> {{ midaz_onboarding.ledger.0.name }}</p>
    </div>
```

<br />

#### `<p><strong>Generation Date:</strong> {% date_time "dd/MM/YYYY HH:mm" %}</p>`

This function applies the date and time when the template is rendered, using the `dd/MM/YYYY HH:mm` format.

#### `<p><strong>Organization:</strong> {{ midaz_onboarding.organization.0.legal_name }}</p>`

This line renders the legal name of the first Organization associated with the onboarding data.

* `midaz_onboarding.organization`: the list of Organizations in the system.
* `0`: fetches the first item in the list.
* `legal_name`: fetches the value of the `legal_name` field.

#### `<p><strong>Ledger:</strong> {{ midaz_onboarding.ledger.0.name }}</p>`

This line renders the name of the first Ledger associated with the onboarding data.

* `midaz_onboarding.ledger`: the list of Ledgers in the system.
* `0`: fetches the first item in the list.
* `name`: fetches the value of the `name` field.

<br />

### Looping through accounts

```html
    {% for account in midaz_onboarding.account %}
      {% with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}  
```

#### `{% for account in midaz_onboarding.account %}`

This loop goes through each account in the `midaz_onboardTing.account` list.

* This list represents all accounts linked to a customer or associated with an onboarding flow.
* For each iteration, it creates a new scope where `account` refers to one specific item in that list.

#### `{% with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}`

Inside the loop, this line:

* Uses the filter function to find a balance from the `midaz_transaction.balance` list where the `account_id` matches the current account's ID.
* The `[0]` gets the first matching balance (assuming there’s only one per account).
* The result is stored in a temporary variable called `balance`, scoped only within this block.

<br />

### Account information

```html
        <div class="section-title">Account: {{ account.alias }}</div>
        <div class="info">
          <p><strong>ID:</strong> {{ account.id }}</p>
          <p><strong>Currency:</strong> {{ balance.asset_code }}</p>
          <p><strong>Current Balance:</strong> {{ balance.available }}</p>
        </div>
```

#### `<div class="section-title">Account: {{ account.alias }}</div>`

Displays a section title with the account alias (a friendly or readable name for the account).

#### `<p><strong>ID:</strong> {{ account.id }}</p>`

Shows the unique identifier of the account.

#### `<p><strong>Currency:</strong> {{ balance.asset_code }}</p>`

Displays the currency code (like "BRL" or "USD") associated with the account’s balance.

#### `<p><strong>Current Balance:</strong> {{ balance.available }}`

Shows the current available balance.

<br />

### Operations information

```html
            {% for operation in midaz_transaction.operation %}
              {% if operation.account_id == account.id %}
                {% set original_amount = operation.amount %}
                {% set discount_amount = original_amount * 0.03  %}
                {% set final_amount = original_amount - discount_amount %}
```

#### `{% for operation in midaz_transaction.operation %}`

This loops through all operations from the `midaz_transaction.operation` list.

#### `{% if operation.account_id == account.id %}`

This line checks whether the current operation is related to the current account in the outer loop (from your earlier block) and only proceeds if:

* The operation’s `account_id` matches the `account.id` in focus.

#### `{% set original_amount = operation.amount %}`

This line sets a temporary variable named `original_amount` and gives it the value of the `amount` field in the operation object.

#### `{% set discount_amount = original_amount * 0.03 %}`

This line calculates a 3% discount on the original amount.

* The result is saved as `discount_amount`.

#### `{% set final_amount = original_amount - discount_amount %}`

This line subtracts the discount from the original amount.

* The final result is stored in `final_amount`, which can be rendered later.

<br />

### Table columns

```html
                <tr>
                  <td>{{ operation.id }}</td>
                  <td>{{ operation.type }}</td>
                  <td>{{ original_amount|floatformat:2 }}</td>
                  <td>{{ discount_amount|floatformat:2 }}</td>
                  <td>{{ final_amount|floatformat:2 }}</td>
                  <td>{{ operation.description }}</td>
                </tr>
```

This block outputs a clean, readable table row for each operation, including the following information:

#### `<td>{{ operation.id }}</td>`

Unique identifier for the operation.

#### `<td>{{ operation.type }}</td>`

Type of operation, such as `credit` or `debit`.

#### `<td>{{ original_amount|floatformat:2 }}</td>`

Shows the original (unadjusted) amount, formatted with 2 decimal places.

#### `<td>{{ discount_amount|floatformat:2 }}</td>`

Displays the calculated discount, also with 2 decimal places.

#### `<td>{{ final_amount|floatformat:2 }}</td>`

Shows the final amount after applying the 3% discount, nicely formatted.

#### `<td>{{ operation.description }}</td>`

Renders the description or label of the operation, such as "Transfer to savings".

### Report footer

```html
    <div class="footer">
      Document generated automatically via Smart Templates - Lerian · {{ midaz_onboarding.organization.0.legal_document }}
    </div>
```

This line renders a footer at the bottom of the document, indicating that the content was automatically generated using Smart Templates by Lerian.

* It also includes the legal document number of the first organization linked to the onboarding data, pulled dynamically using the `{{ midaz_onboarding.organization.0.legal_document }}` placeholder.

<br />

## Template output

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Analytical Financial Report</title>
  <style>
    body {
      font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
      background-color: #f4f6f9;
      color: #333;
      padding: 40px;
    }

    .container {
      max-width: 900px;
      margin: auto;
      background: #ffffff;
      padding: 30px 40px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
    }

    h1 {
      text-align: center;
      font-size: 24px;
      color: #004080;
      margin-bottom: 10px;
    }

    .subtitle {
      text-align: center;
      font-size: 14px;
      color: #777;
      margin-bottom: 30px;
    }

    .section-title {
      font-weight: bold;
      font-size: 16px;
      margin-top: 30px;
      margin-bottom: 10px;
      color: #004080;
      border-bottom: 1px solid #ccc;
      padding-bottom: 4px;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 15px;
    }

    th, td {
      padding: 10px;
      text-align: left;
      border: 1px solid #ccc;
    }

    th {
      background-color: #e8f0fe;
      font-weight: bold;
      color: #003366;
    }

    .info p {
      margin: 4px 0;
    }

    .footer {
      margin-top: 40px;
      text-align: center;
      font-size: 12px;
      color: #999;
    }
  </style>
</head>
<body>
  <div class="container">
    <h1>Analytical Financial Report</h1>
    <div class="subtitle">Detailed analysis of movements by linked accounts.</div>

    <div class="info">
      <p><strong>Generation Date:</strong> 23/05/2025 14:08</p>
      <p><strong>Organization:</strong> Mante - Pfannerstill</p>
      <p><strong>Ledger:</strong> Rice, Rowe and O'Hara</p>
    </div>    
        <div class="section-title">Account: @external/USD</div>
        <div class="info">
          <p><strong>ID:</strong> 0196d97e-a58d-7814-8eb3-e510b818f6a5</p>
          <p><strong>Currency:</strong> USD</p>
          <p><strong>Current Balance:</strong> 499950.00</p>
        </div>

        <table>
          <thead>
            <tr>
              <th>Operation ID</th>
              <th>Type</th>
              <th>Original Amount</th>
              <th>Discount (3%)</th>
              <th>Final Amount</th>
              <th>Description</th>
            </tr>
          </thead>
          <tbody>            
                <tr>
                  <td>0196d982-4085-7e1d-b698-3aad306df721</td>
                  <td>CREDIT</td>
                  <td>500000.00</td>
                  <td>15000.00</td>
                  <td>485000.00</td>
                  <td>Duplicated account test</td>
                </tr>
                <tr>
                  <td>0196d983-a2c2-7dce-ad14-78eb36d9f2d1</td>
                  <td>DEBIT</td>
                  <td>20.00</td>
                  <td>0.60</td>
                  <td>19.40</td>
                  <td>Loan payment person1</td>
                </tr>
                <tr>
                  <td>0196d983-a2c2-7e0d-a246-45d5234138e3</td>
                  <td>DEBIT</td>
                  <td>30.00</td>
                  <td>0.90</td>
                  <td>29.10</td>
                  <td>Loan payment person1</td>
                </tr>
          </tbody>
        </table>      
    <div class="footer">
      Document generated automatically via Smart Templates - Lerian · 123.456.789-0001/00
    </div>
  </div>
</body>
</html>

```

<br />

## Rendered output

<Image align="center" alt="Figure 1. An example of a financial report rendered from the HTML code in the example." border={true} caption="Figure 1. An example of a financial report rendered from the HTML code in the example." src="https://files.readme.io/a4c3f72775b5d16bc0ec4d1fe9fd1decce254103c58742eb1faf098c50c1ee2d-html_template.jpg" width="600px" />

<br />

# XML template - Analytical financial report

***

This page guides you through a full example of an **Analytical Financial** report in **XML**. The template breaks down account-level activity, applies custom logic (like discounts), and includes clear summaries to support analysis and auditing.

**What this report shows:**

* **Organization and Ledger details**.
* **Account-level overview**: balance, currency, and alias.
* **Operation breakdowns**: original amount, discount, final amount, status, description, and type.
* **Summaries per account**: total operations, total value, and average per operation.

<br />

## Template code

```xml
<AnalyticalReport>
    <Organization>{{ midaz_onboarding.legal_name }} - Tax ID: {{ midaz_onboarding.legal_document }}</Organization>
    <GenerationDate>28.04.2025</GenerationDate>
    {%- with ledger = midaz_onboarding.ledger[0] %}
    <Ledger>{{ ledger.name }}</Ledger>

    {%- for account in midaz_onboarding.account %}
    <Account>
        <AccountID>{{ account.id }}</AccountID>
        <Alias>{{ account.alias }}</Alias>
        {%- with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}
        <CurrentBalance> {{ balance.available }}</CurrentBalance>
        {%- endwith %}
        {%- for balance in midaz_transaction.balance %}
        {%- if balance.account_id == account.id %}
        <CurrentBalance>{{ balance.available }}</CurrentBalance>
        {%- endif %}
        {%- endfor %}
        <Currency>{{ account.asset_code }}</Currency>
        <Operations>
        {%- for operation in midaz_transaction.operation %}
        {%- if operation.account_id == account.id %}
            {%- set original_amount = operation.amount %}
            {%- set discount_amount = original_amount * 0.03 %}
            {%- set final_amount = original_amount - discount_amount %} 
        <Operation>
                <OperationID>{{ operation.id }}</OperationID>
                <Description>{{ operation.description }}</Description>
                <Type>{{ operation.type }}</Type>
                <Route>{{ operation.route }}</Route>
                <OriginalAmount>{{ original_amount }}</OriginalAmount>
                <DiscountAmount>{{ discount_amount }}</DiscountAmount>
                <FinalAmountWithDiscount>{{ final_amount }}</FinalAmountWithDiscount>
                <Currency>{{ operation.asset_code }}</Currency>
                <Status>{{ operation.status }}</Status>
            </Operation>
        {%- endif %}
        {%- endfor %}
        </Operations>
        <AccountSummary>
            <TotalOperations>{% count_by midaz_transaction.operation if account_id == account.id %}</TotalOperations>
            <SumOfOperations>{% sum_by midaz_transaction.operation by "amount" if account_id == account.id %}</SumOfOperations>
            <AverageOfOperations>{% avg_by midaz_transaction.operation by "amount" if account_id == account.id %}</AverageOfOperations>
        </AccountSummary>
    </Account>
    {%- endfor %}
</AnalyticalReport>
```

<br />

## Code breakdown

### Organization and Ledger information

```xml
<AnalyticalReport>
    <Organization>{{ midaz_onboarding.legal_name }} - Tax ID: {{ midaz_onboarding.legal_document }}</Organization>
    <GenerationDate>28.04.2025</GenerationDate>
    {%- with ledger = midaz_onboarding.ledger[0] %}
    <Ledger>{{ ledger.name }}</Ledger>
```

#### `<Organization>{{ midaz_onboarding.organization.legal_name }} - CNPJ: {{ midaz_onboarding.organization.legal_document }}</Organization>`

Displays the organization’s legal name and corresponding *CNPJ* (Brazilian Tax ID).

* `{{ midaz_onboarding.organization.legal_name }}`: retrieves the organization’s name.
* `{{ midaz_onboarding.legal_document }}`: retrieves the *CNPJ*.

#### `{%- with ledger = midaz_onboarding.ledger[0] %}`

Creates a temporary variable pointing to the first available ledger. This keeps the code cleaner and avoids repetitive references.

* `-`: removes extra spaces in the rendered file.
* `ledger`: the name of the temporary variable.
* `midaz_onboarding.ledger[0].name`: points to the first available ledger.

<br />

### Looping through accounts

```xml
    {%- for account in midaz_onboarding.account %}
    <Account>
        <AccountID>{{ account.id }}</AccountID>
        <Alias>{{ account.alias }}</Alias>
        {%- with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}
        <CurrentBalance> {{ balance.available }}</CurrentBalance>
        {%- endwith %}
```

#### `{%- for account in midaz_onboarding.account %}` ... `{% endfor %}`

Loops through all accounts linked to the user. Since most users have more than one account, this allows you to display individual data for each one.

* `midaz_onboarding.account`: the full list of user accounts.
* `{%- for account in midaz_onboarding.account %}`: the loop runs once per account.

#### `{%- with balance = filter(midaz_transaction.balance, "account_id", account.id)[0] %}`

Defines a variable named `balance` that holds the current account’s balance.

* `filter()`: scans through `midaz_transaction.balance` to find the one matching the `account.id`.
* `[0]`: grabs the first match.
* The result is stored in `balance`.

#### `<CurrentBalance> {{ balance.available }}</CurrentBalance>`

Shows the account’s available balance (`balance.available`).

<br />

### Looping without `{%with...%}`

```xml
        {%- for balance in midaz_transaction.balance %}
        {%- if balance.account_id == account.id %}
        <CurrentBalance>{{ balance.available }}</CurrentBalance>
        {%- endif %}
        {%- endfor %}
```

#### `{%- for balance in midaz_transaction.balance %}`

Loops through all balances to find the one that matches the current account. This is a more explicit alternative to `filter()`.

* `midaz_transaction.balance`: The list of all balances.
* `{%- balance in midaz_transaction.balance %}`: the loop runs once per balance.

#### `{% if balance.account_id == account.id %}`

Filters balances to show only those that match the current account being processed.

* If they match, the balance is displayed.

<br />

### Currency information

```xml
       <Currency>{{ account.asset_code }}</Currency>
```

Shows the account’s currency (e.g., BRL, USD) to help you understand the value context.

* `account.asset_code`: dynamically injects the currency code.

<br />

### Grouping operations

```xml
        <Operations>
        {%- for operation in midaz_transaction.operation %}
        {%- if operation.account_id == account.id %}
            {%- set original_amount = operation.amount %}
            {%- set discount_amount = original_amount * 0.03 %}
            {%- set final_amount = original_amount - discount_amount %} 
```

<br />

### `<Operations>...</Operations>`

Defines the section where all operations for an account are listed.

* Keeps the report structured and easy to scan.

#### `{%- for operation in midaz_transaction.operation %}`

Loops through all operations in the transaction, and displays operations that match the current account.

* `operation`: represents the current operation that is part of the current transaction.
* `midaz_transaction.operation`: the list of all operations in the transaction.

#### `{%- if operation.account_id == account.id %}`

Ensures that only the relevant operations for the current account are included.

* If the `account_id` of the operation is equal to the current `account.id`, the rest of the block will be executed.

#### `{%- set original_amount = operation.amount %}`

Defines the original amount and sets it as a variable.

* `original_amount`: the name of the variable.

#### `{%- set discount_amount = original_amount * 0.03 %}`

Defines a variable and calculates a 3% discount on the original amount.

* `discount_amount`: the name of the variable.
* `original_amount * 0.03`: applies a 3% discount on the original amount.

#### `{%- set final_amount = original_amount - discount_amount %`}

Subtracts the discount from the original amount to get the final value.

* `final_amount`: variable that represents the final value.
* `original_amount - discount_amount`: the subtraction operation.

<br />

### Operation block

```xml
        <Operation>
                <OperationID>{{ operation.id }}</OperationID>
                <Description>{{ operation.description }}</Description>
                <Type>{{ operation.type }}</Type>
                <Route>{{ operation.route }}</Route>
                <OriginalAmount>{{ original_amount }}</OriginalAmount>
                <DiscountAmount>{{ discount_amount }}</DiscountAmount>
                <FinalAmountWithDiscount>{{ final_amount }}</FinalAmountWithDiscount>
                <Currency>{{ operation.asset_code }}</Currency>
                <Status>{{ operation.status }}</Status>
        </Operation>
```

Each `<Operation>` contains detailed information about the transaction. These fields help users audit and understand what happened.

#### `<OperationID>{{ operation.id }}</OperationID>`

Unique ID of the operation.

#### `<Description>{{ operation.description }}</Description>`

Short description of the operation.

#### `<Type>{{ operation.type }}</Type>`

Operation type (e.g., credit, debit, adjustment).

#### `<Route>{{ operation.route }}</Route>`

Associated operation route entry.

#### `<OriginalAmount>{{ original_amount }}</OriginalAmount>`

Original amount before any adjustments.

#### `<DiscountAmount>{{ discount_amount }}</DiscountAmount>`

Value of the discount applied.

#### `<FinalAmountWithDiscount>{{ final_amount }}</FinalAmountWithDiscount>`

Final amount after discount.

#### `<Currency>{{ operation.asset_code }}</Currency>`

The currency used for the operation.

#### `<Status>{{ operation.status }}</Status>`

Operation status (e.g., Approved, pending).

<br />

### Account summary block

```xml
<AccountSummary>
            <TotalOperations>{% count_by midaz_transaction.operation if account_id == account.id %}</TotalOperations>
            <SumOfOperations>{% sum_by midaz_transaction.operation by "amount" if account_id == account.id %}</SumOfOperations>
            <AverageOfOperations>{% avg_by midaz_transaction.operation by "amount" if account_id == account.id %}</AverageOfOperations>
        </AccountSummary>
```

<br />

#### `{% count_by midaz_transaction.operation if account_id == account.id %}`

Counts the number of operations associated with your account and assists in understanding the transaction volume for the reporting period.

* The `count_by` function goes through `midaz_transaction.operation` and adds up how many operations have the same `account_id` as the current account.

<br />

#### `{% sum_by midaz_transaction.operation by "amount" if account_id == account.id %}`

Sums up the total value of all operations for this account, and outputs the result using two decimal places.

* `sum_by` loops through transactions filtered by `account_id`.
* Sums the `amount` fields.

<br />

#### `{% avg_by midaz_transaction.operation by "amount" if account_id == account.id %}`

Calculates the average operation value for this account. Useful for spotting spending patterns or outliers.

* `avg_by` applies the average calculation to the amount values.
* Only considers operations for the current account.

<br />

## Template output

```xml
<AnalyticalReport>
	<Organization>Ferry, Stiedemann and Jast - CNPJ: 78425230000190</Organization>
	<GenerationDate>28.04.2025</GenerationDate>
	<Ledger>O'Connell, Dietrich and Bernhard</Ledger>
	<Account>
		<AccountID>01965efe-5afd-733d-9a64-3d53f73b13c8</AccountID>
		<Alias>@external/BRL</Alias>
		<CurrentBalance>1000.00</CurrentBalance>
		<CurrentBalance>1000.00</CurrentBalance>
		<Currency>BRL</Currency>
		<Operations>
			<Operation>
				<OperationID>01965f04-7087-73f1-a802-f8fceedb34ec</OperationID>
				<Description>Initial transfer</Description>
				<Type>DEBIT</Type>
				<Route></Route>
				<OriginalAmount>1000.00</OriginalAmount>
				<DiscountAmount>30.00</DiscountAmount>
				<FinalAmountWithDiscount>970.00</FinalAmountWithDiscount>
				<Currency>BRL</Currency>
				<Status></Status>
			</Operation>
		</Operations>
		<AccountSummary>
			<TotalOperations>1</TotalOperations>
			<SumOfOperations>1000.00</SumOfOperations>
			<AverageOfOperations>1000.00</AverageOfOperations>
		</AccountSummary>
	</Account>
	<Account>
		<AccountID>01965eff-0b2d-73cf-bbfe-0dc1ac9032d3</AccountID>
		<Alias>@wallet_46040127</Alias>
		<CurrentBalance> 0</CurrentBalance>
		<CurrentBalance> 0</CurrentBalance>
		<Currency>BRL</Currency>
		<Operations></Operations>
		<AccountSummary>
			<TotalOperations>0</TotalOperations>
			<SumOfOperations>0.00</SumOfOperations>
			<AverageOfOperations>0.00</AverageOfOperations>
		</AccountSummary>
	</Account>
	<Account>
		<AccountID>01965f01-aa72-7254-bb4a-fa9c8c9d4009</AccountID>
		<Alias>@account1</Alias>
		<CurrentBalance>300.00</CurrentBalance>
		<CurrentBalance>300.00</CurrentBalance>
		<Currency>BRL</Currency>
		<Operations>
			<Operation>
				<OperationID>01965f04-7087-73d2-a167-cb5146bee801</OperationID>
				<Description>External to accounts 1 and 2</Description>
				<Type>CREDIT</Type>
				<ChartOfAccounts></ChartOfAccounts>
				<OriginalAmount>300.00</OriginalAmount>
				<DiscountAmount>9.00</DiscountAmount>
				<FinalAmountWithDiscount>291.00</FinalAmountWithDiscount>
				<Currency>BRL</Currency>
				<Status></Status>
			</Operation>
		</Operations>
		<AccountSummary>
			<TotalOperations>1</TotalOperations>
			<SumOfOperations>300.00</SumOfOperations>
			<AverageOfOperations>300.00</AverageOfOperations>
		</AccountSummary>
	</Account>
	<Account>
		<AccountID>01965f01-ff74-712a-9e2c-d987f15a1d14</AccountID>
		<Alias>@account2</Alias>
		<CurrentBalance>700.00</CurrentBalance>
		<CurrentBalance>700.00</CurrentBalance>
		<Currency>BRL</Currency>
		<Operations>
			<Operation>
				<OperationID>01965f04-7087-73b1-aff9-69f59d623d31</OperationID>
				<Description>External to accounts 1 and 2</Description>
				<Type>CREDIT</Type>
				<ChartOfAccounts></ChartOfAccounts>
				<OriginalAmount>700.00</OriginalAmount>
				<DiscountAmount>21.00</DiscountAmount>
				<FinalAmountWithDiscount>679.00</FinalAmountWithDiscount>
				<Currency>BRL</Currency>
				<Status></Status>
			</Operation>
		</Operations>
		<AccountSummary>
			<TotalOperations>1</TotalOperations>
			<SumOfOperations>700.00</SumOfOperations>
			<AverageOfOperations>700.00</AverageOfOperations>
		</AccountSummary>
	</Account>
	<Account>
		<AccountID>01965f02-55fc-7a06-9f95-07e9a9ff4941</AccountID>
		<Alias>@account3</Alias>
		<CurrentBalance> 0</CurrentBalance>
		<CurrentBalance> 0</CurrentBalance>
		<Currency>BRL</Currency>
		<Operations></Operations>
		<AccountSummary>
			<TotalOperations>0</TotalOperations>
			<SumOfOperations>0.00</SumOfOperations>
			<AverageOfOperations>0.00</AverageOfOperations>
		</AccountSummary>
	</Account>
</AnalyticalReport>
```

<br />

<br />

# TXT template - Transaction receipt

***

Just follow the steps below to create a transaction receipt template using **Smart Templates**. This example uses the `.txt` format.

<br />

## Template code

```text
##########################################
#            PROOF OF PAYMENT            #
##########################################

Generation Date: {% date_time "dd/MM/YYYY HH:mm" %}
Ledger Name: {{ midaz_onboarding.ledger.0.name }}

{%- for transaction in midaz_transaction.transaction %}
-----------------------------------------
Transaction ID: {{ transaction.id }}
Transaction Date: {{ transaction.created_at }}
Total Amount: {{ transaction.amount }}
Transaction Status: {{transaction.status}}
------------------------------------------

Source Accounts:
{%- for operation in filter(midaz_transaction.operation, "transaction_id", transaction.id) %} 
{%- if operation.type == "DEBIT" %} 
- Alias: {{operation.account_alias }} 
- Debit Amount: {{ operation.amount }} 
{%- endif %}
{%- endfor %}

Target Accounts:
{%- for operation in filter(midaz_transaction.operation, "transaction_id", transaction.id) %} 
{%- if operation.type == "CREDIT" %} 
- Alias: {{operation.account_alias }} 
- Credit Value: {{operation.amount }} 
{%- endif %}
{%- endfor %}

{%- endfor %}
------------------------------------------
Automatically generated document.
```

<br />

## Code breakdown

### `Generation Date: {% date_time "dd/MM/YYYY HH:mm" %}`

Renders the **current date and time** when the template is processed.

* Uses the format `dd/MM/YYYY HH:mm` (e.g., `26/05/2025 11:45`).

### `Ledger Name: {{ midaz_onboarding.ledger.0.name }}`

Displays the **name of the ledger** associated with this report.

* `midaz_onboarding.ledger` is a list, and `[0]` selects the **first ledger**.
* `.name` fetches the name property, like `"Corporate Ledger"`.

### `{% for transaction in midaz_transaction.transaction %}`

This loop iterates over **each transaction** in the `midaz_transaction.transaction` list.\
For every transaction, the code below will run once to display its information.

<br />

### Transaction Header Block

Each transaction prints the following details:

```text
-----------------------------------------
Transaction ID: {{ transaction.id }}
Transaction Date: {{ transaction.created_at }}
Total Amount: {{ transaction.amount }}
Transaction Status: {{ transaction.status }}
------------------------------------------
```

* `transaction.id`: Unique ID of the transaction.
* `transaction.created_at`: Timestamp when the transaction was created.
* `transaction.amount`: The raw amount of the transaction.
* `transaction.status`: Current status such as `COMPLETED`, `PENDING`, or `FAILED`.

<br />

### Source Accounts Section

```jinja
Source Accounts:
{% for operation in filter(midaz_transaction.operation, "transaction_id", transaction.id) %}
  {% if operation.type == "DEBIT" %}
    - Alias: {{ operation.account_alias }}
    - Debit Amount: {{ operation.amount }}
  {% endif %}
{% endfor %}
```

* Filters the operations list to include only operations linked to the **current transaction**.
* Further filters it to only show operations with `type == "DEBIT"` (funds leaving an account).
* For each matching debit:

  * Displays the **account alias**.
  * Displays the **debit amount**.

<br />

### Target Accounts Section

```jinja
Target Accounts:
{% for operation in filter(midaz_transaction.operation, "transaction_id", transaction.id) %}
  {% if operation.type == "CREDIT" %}
    - Alias: {{ operation.account_alias }}
    - Credit Value: {{ operation.amount }}
  {% endif %}
{% endfor %}
```

* Same logic as the debit block, but filters for `type == "CREDIT"` (funds received).
* For each matching credit:

  * Displays the **account alias**.
  * Displays the **credit amount**.

<br />

### Footer Text

```text
------------------------------------------
Automatically generated document.
```

* Simple text footer that marks the end of the report.
* Indicates that the file was generated automatically, typically for traceability.

<br />

## Template output

```text
##########################################
#            PROOF OF PAYMENT            #
##########################################

Generation Date: 23/05/2025 14:08
Ledger Name: Rice, Rowe and O'Hara

-----------------------------------------
Transaction ID: 75906707-8c31-479c-b354-aa805c4cefbc
Transaction Date: 2025-05-22T14:15:22.123Z
Total Amount: 5000.00
Transaction Status: ACTIVE
------------------------------------------

Source Accounts:
- Alias: @external|USD 
- Debit Amount: 1500.00

Target Accounts:
- Alias: @wallet_12345 
- Credit Value: 1500.00

------------------------------------------
Automatically generated document.
```

<br />

# Tip for rendering a transaction receipt

To generate a transaction receipt using Smart Templates, you can send a request like this to the [Create a Report](ref:create-report) endpoint:

```json
{
  "templateId": "0196f99c-4d3e-73bc-b5d3-2a5bb1efbee4",
  "ledgerId": [
    "0196d97e-587e-76d8-a458-9d2517ed9aba"
  ],
  "filters": {
    "midaz_transaction": {
      "transaction": {
        "id": ["0196d983-a2c2-7d5a-a5b7-029fe0dcb710"]
      }
    }
  }
}
```

### What does this filter do?

The `filters` field lets you limit the data used when rendering the template. In this case, you're telling the system to use **only** the transaction with the ID `0196d983-a2c2-7d5a-a5b7-029fe0dcb710`.

That means your receipt will include just that one transaction — and the operations linked to it — keeping the output clean and focused.

### What happens next?

Once the request is sent:

* The API returns a `reportId`.
* You can use this ID to check the status of the report.
* When it’s ready, the report will be available for download.

### Why it matters

This approach gives you clarity and control. Each receipt is generated in isolation, using the filters and templates you define.