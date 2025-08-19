---
title: Using Smart Templates
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
The Smart Template plugin helps Midaz Ledger users create dynamic, data-driven reports without writing SQL. Instead of building complex queries, you define domains, tables, and fields using simple placeholders.

<br />

# Typical usage

***

Here’s how the workflow goes from start to finish:

### Step 1 – Create the `.tpl` file

Write your template using the structure that matches your desired output: HTML, XML, CSV, or TXT.

For example, if your goal is an XML report, the file should follow XML syntax and include Smart Template blocks. Check the [Template examples](doc:template-examples) pages for a few examples on how to create your file.

> ❗️ Important
>
> Regardless of the format, the file **must** be saved with a `.tpl` extension. This is required for proper processing.

<br />

### Step 2 – Upload the template

Once your `.tpl` file is ready, upload it using the [Upload a Template](ref:upload-template) endpoint.

* Set the `outputFormat` field to define the expected report format.
* The system automatically names the file using a UUID: `0196159b-4f26-7300-b3d9-f4f68a7c85f3.tpl`.

<details>
  <summary><b>Managing templates</b></summary>

  You can manage your templates with the following endpoints:

  * **[List Templates](ref:list-templates)** – View all templates in your organization.
  * **[Retrieve Template details](ref:retrieve-template-details)** – Access the information for a specific template.
  * **[Update a Template](ref:update-templates)** – Replace the `.tpl` file, edit the description, or change the output format.
  * **[Delete a Template](ref:delete-template)** – Marks a template as deleted by setting the `deletedAt` timestamp. The data remains stored, but won’t appear in standard queries.
</details>

<br />

### Step 3 – Generate the report

With your template uploaded, use the [Create a Report](ref:create-report) endpoint to trigger report generation.

* The API returns a `reportId` that you can use to track the process and fetch the final output when it’s ready.

<br />

### Step 4 – Check the report status

Use the [Check Report Status](ref:check-report-status) endpoint to monitor progress.

* You’ll see whether the report is `processing` or `finished`.
* Additional context may be included to help troubleshoot or confirm completion.

<br />

### Step 5 – Download the report

Once the report status is `finished`, download the file using the [Download a Report](ref:download-report) endpoint.

* The file is returned in the response body.
* The `Content-Disposition` header sets the correct filename and format to support a smooth download experience.

<br />

# Mapping data sources for dynamic filters

***

To support dynamic filtering during report generation, the Smart Templates plugin offers two supporting endpoints:

* [List Data Sources](ref:get_v1-data-sources): Lists the available data sources in the system.
* [Retrieve a Data Source by id](ref:get_v1-data-sources-id): Returns the schema for the selected data source, including tables and fields.

These endpoints are especially useful if you're building an interface where users can select a data source and apply filters dynamically before generating a report.

This separation helps reduce payload size and improves performance by allowing systems to load only what they need, when they need it.

> 📘 Note
>
> These endpoints are optional and designed for advanced use cases, like building custom UIs or automating report workflows based on schema data.