---
title: Managing templates
deprecated: false
hidden: false
metadata:
  robots: index
---
Templates are reusable configurations that define the structure and format of generated reports. In this section, you’ll learn how to manage templates via the [Midaz Console](doc:about-midaz-console).

<br />

# Accessing the Templates page

***

To open the **Templates** page, click the **Manage Templates** button on the **Smart Templates** page (*Figure 1*). You can also click the **Templates** tab.

<Image align="center" border={true} caption="Figure 1. Button to access the Templates page." src="https://files.readme.io/29d85591a227fc4fab99f3a1b92c7ac5ad7d6da4674fc05e33dd36317d376ea3-reports_button.jpg" />

<br />

The **Templates** page will open (*Figure 2*).

* If you have any templates previously created, they will be displayed either as a list or a grid.

<Image align="center" border={true} caption="Figure 2. The main Templates page." src="https://files.readme.io/c7716b7845958c8b811c9a07e69a8a357cb2c08dc272e6745715d5b5e8c52e80-templates_main.png" />

<br />

## Available actions

From the Holders page, you can:

* [Add a template](#adding-a-template)
* [Update a template](#updating-a-template)
* [Delete a template](#deleting-a-template)

<br />

# Adding a template

***

Before you can generate any reports, you must upload a `.tpl` file to the Console. This file must be created beforehand, outside the Console.

This section shows how to upload a pre-existing template to make it available for report generation in Midaz.

> 📘 Note
>
> If you need help building a template file, check out our [Template examples](doc:template-examples) section.

To add a template:

1. Go to the **Templates** page and click the **New Template +** button (*Figure 3*).

<Image align="center" border={true} caption="Figure 3. The button to create a new template" src="https://files.readme.io/db683a98e53d3f98d1a44ca55346869975425457d81e8fe636825c55b0d823ac-templates_new.jpg" />

<br />

2. The **Template Details** form will open.
3. Specify the following information:
   1. A name for the template.
   2. The output format (CSV, XML, HTML or CSV).
   3. The `.tpl` file you created.
4. Click **Create Template** to upload the template.

If anything goes wrong, an error message will appear within the form.

> ❗️ Important
>
> Only `.tpl` files are accepted, with a maximum size of **5MB**. The Console doesn’t support editing or generating `.tpl` files, only uploading and managing them.

<br />

# Updating a template

***

Need to update a template’s name, output format, or replace the uploaded file? This section walks you through how to update a template directly from the Console.

> 📘 Note
>
> The Console doesn't allow you to modify the content of the `.tpl` file. To update the structure or logic of a template, you must edit the file locally and re-upload it.

<br />

To edit a template:

1. On the **Templates** tab, locate the template you want to update.
2. Click the three dots ( <i class="fas fa-ellipsis-v" /> ) on the **Actions** column and select **Edit** (*Figure 4*).

<Image align="center" border={true} caption="Figure 4. The option to edit a template from the Actions menu." src="https://files.readme.io/a9ce4b6e56528594ef3582efec6e7461c80eccb82e9829d4d03c5366995e0a13-templates_edit.jpg" />

<br />

3. A form will open in a side sheet, pre-filled with the current metadata.
4. Update the necessary information and click **Save** to apply your changes.
5. If the request fails, an error message will be shown in the form.

> ⚠️ Attention
>
> Replacing a `.tpl` file affects all future reports generated with that template. Reports already created will not be impacted.

<br />

# Deleting a template

***

To remove a template from the Console:

1. On the **Templates** tab, find the template you want to delete.
2. Click the three dots ( <i class="fas fa-ellipsis-v" /> ) on the **Actions** column and select **Delete Template** (*Figure 5*).
3. Confirm the action in the dialog that appears.

<Image align="center" border={true} caption="Figure 5. The option to delete a template from the Actions menu." src="https://files.readme.io/3d732a66a38e23d672ee3c3ab00360ce315dae827f6e88baec7afc7206ff4628-templates_delete.png" />

<br />

> ❗️ Important
>
> Deleting a template cannot be undone. However, reports already generated using that template will remain available in the **Reports** tab.