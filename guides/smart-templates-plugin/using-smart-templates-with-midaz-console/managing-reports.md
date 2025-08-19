---
title: Managing reports
deprecated: false
hidden: false
metadata:
  robots: index
---
Once templates are uploaded, you can start generating reports from them.

This page covers how to view and manage reports through [Midaz Console](doc:about-midaz-console), including filtering, tracking their status, and downloading results.

<br />

# Accessing the Reports page

***

To open the **Reports** page, click the **Manage Reports** button on the **Smart Templates** page (*Figure 1*). You can also click the **Reports** tab

<Image align="center" border={true} caption="Figure 1. Button to open the Reports page." src="https://files.readme.io/c5bf03c75e3b1ae924ad63e864263347e23e268626ea70f5825c926de3570b8f-reports_button.jpg" />

<br />

The **Reports** page will open (*Figure 2*).

* If you've previously generated reports for the organization, they will appear as a list or grid, showing key details like their status, file name, linked template, and timestamps for creation or last update.

<Image align="center" border={true} caption="Figure 2. The main Reports page" src="https://files.readme.io/c59caf1a33610ce1242f7b8f6578053efbd4ea4231f3ebce6c7065453cee7615-reports_main.png" />

<br />

## Searching for reports

If you've previously generated reports, you can search reports by keyword, filter them by status, and navigate across pages using the pagination controls at the bottom of the table.

> 👍 Tip
>
> The list automatically refreshes when you generate a new report or when a report's status is updated.

<br />

### Report status

Each report displays a status badge, so you always know where it stands:

| Status       | Definition                                                             |
| ------------ | ---------------------------------------------------------------------- |
| `Pending`    | The report was just created and is waiting to be processed.            |
| `Processing` | The system is generating the report.                                   |
| `Completed`  | The report is ready to download.                                       |
| `Failed`     | Something went wrong. You can try again or check the filters/template. |

<br />

## Actions available

* [Generate a report](#generating-a-report)
* [Download a report](#downloading-a-report)
* [Edit a report](#editing-a-report)
* [Delete a report](#deleting-a-report)

<br />

# Generating a report

***

To create a new report:

1. Go to the **Reports** page and click the **New Report +** button.
2. The **New Report** form will open.
3. Select a template, fill in the necessary information, and apply the desired filters.
   1. The filters available depend on how the template was built.
4. Click **Generate Report**.
5. The new report appears in the list with a `Pending` status and the status will update automatically in the table.
6. Once the report reaches `Completed`, it becomes available for download.

> ⚠️ Attention
>
> Reports are generated asynchronously. If something goes wrong (e.g. the report fails or stalls), you can try again with the same parameters.

<br />

<br />

# Downloading a report

***

When a report reaches the `Completed` status, you will be able to download it. To do so:

1. Go to the **Reports** page and find the report that you want to download.
2. Click the three dots ( <i class="fas fa-ellipsis-v" /> ) on the **Actions** column and select **Download Report** (*Figure 3*).

<Image align="center" border={true} caption="Figure 3. The option to download a report from the Actions menu." src="https://files.readme.io/f9841aebfae12e7a348a2b6ebba8c8f7eef095e752ff39dcfa463e934da19aa2-reports_download.png" />

> 📘 Note
>
> If the download fails or is not available, make sure the report has been marked as `Completed`. You can also use the retry flow by generating a new report with the same filters.

<br />

# Editing a report

***

To edit a report:

1. On the **Reports** tab, locate the template you want to update.
2. Click the three dots ( <i class="fas fa-ellipsis-v" /> ) on the **Actions** column and select **Edit** (*Figure 4*).

<Image align="center" border={true} caption="Figure 4. The option to edit or view a template from the Actions menu." src="https://files.readme.io/bd5b8d052d272a52f295bdc4e5129096eaccd255bd02b0d4db695072392dbaaf-reports_edit.jpg" />

<br />

3. A form will open in a side sheet, pre-filled with the current metadata.
4. Update the necessary information and click **Save** to apply your changes.
5. If the request fails, an error message will be shown in the form.

<br />

# Deleting a report

***

To remove a report from the Console:

1. On the **Report** tab, find the template you want to delete.
2. Click the three dots ( <i class="fas fa-ellipsis-v" /> ) on the **Actions** column and select **Delete Report** (*Figure 5*).
3. Confirm the action in the dialog that appears.

<Image align="center" border={true} caption="Figure 5. The option to delete a report from the Actions menu." src="https://files.readme.io/42c20f1c565e36d1492392ac98dc16e782788916ad657da2e67dbbcef6a56286-reports_delete.png" />

> ❗️ Important
>
> Deleting a report  cannot be undone.