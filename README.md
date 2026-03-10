# Microsoft Power Automate – SharePoint Filter Expression

This repository contains expressions and flow configurations for Microsoft Power Automate.

---

## Filter SharePoint List Items: Status = "Complete" AND Billing = "Yes"

The following expressions can be used inside a Power Automate flow to retrieve only the SharePoint list items where the **Status** column equals `Complete` **and** the **Billing** column equals `Yes`.

---

### Option 1 – OData Filter Query (recommended)

Use this in the **"Get items"** SharePoint action under **Filter Query** (Show advanced options).

```
Status eq 'Complete' and Billing eq 'Yes'
```

**Steps:**

1. Add the **SharePoint – Get items** action to your flow.
2. Set the **Site Address** and **List Name** fields.
3. Click **Show advanced options**.
4. In the **Filter Query** field, enter:
   ```
   Status eq 'Complete' and Billing eq 'Yes'
   ```
5. (Optional) Set **Top Count** to limit the number of results returned.

> **Note:** Column internal names may differ from display names. If the filter returns no results, check the internal name of each column in your SharePoint list settings (List Settings → Columns). Common examples:
> - Display name `Status` → internal name may be `Status`
> - Display name `Billing` → internal name may be `Billing` or `Billing0`

---

### Option 2 – Filter Array Expression (dynamic filtering after retrieval)

If you prefer to retrieve all items first and then filter them inside the flow, use a **Filter Array** action after **Get items**.

**From** — set to the output of the Get items action:
```
@outputs('Get_items')?['body/value']
```

**Condition** — use the following expression in Advanced mode:

```
@and(
  equals(item()?['Status'], 'Complete'),
  equals(item()?['Billing'], 'Yes')
)
```

**Steps:**

1. Add **SharePoint – Get items** (no filter needed here).
2. Add a **Data Operations – Filter Array** action.
3. Set **From** to:
   ```
   @outputs('Get_items')?['body/value']
   ```
4. Switch the condition editor to **Edit in advanced mode**.
5. Enter the expression:
   ```
   @and(
     equals(item()?['Status'], 'Complete'),
     equals(item()?['Billing'], 'Yes')
   )
   ```
6. Use the **Filter Array** output in subsequent steps. The filtered results are available as:
   ```
   @body('Filter_array')
   ```

---

### Option 3 – Condition inside Apply to Each

If you are already looping through items with an **Apply to each** action, add a **Condition** block inside the loop:

| Left side | Condition | Right side |
|---|---|---|
| `@items('Apply_to_each')?['Status']` | `is equal to` | `Complete` |
| `@items('Apply_to_each')?['Billing']` | `is equal to` | `Yes` |

Set the **AND / OR** toggle to **AND** so both conditions must be true.

---

## Column Name Reference

| Display Name | Typical Internal Name | Expected Value |
|---|---|---|
| Status | `Status` | `Complete` |
| Billing | `Billing` | `Yes` |

> If your list uses a **Choice** column, values are matched as plain strings (e.g., `'Complete'`, `'Yes'`).
> If your list uses a **Yes/No (Boolean)** column for Billing, use `1` instead of `'Yes'` in the OData query:
> ```
> Status eq 'Complete' and Billing eq 1
> ```

---

## Resources

- [OData query operators supported in SharePoint REST API](https://learn.microsoft.com/en-us/sharepoint/dev/sp-add-ins/use-odata-query-operations-in-sharepoint-rest-requests)
- [Filter Array action in Power Automate](https://learn.microsoft.com/en-us/power-automate/data-operations#use-the-filter-array-action)
- [Get items – SharePoint connector](https://learn.microsoft.com/en-us/connectors/sharepointonline/#get-items)
