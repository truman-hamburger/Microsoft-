# SharePoint Filter Expression for Power Automate

## Goal

Retrieve all entries from a SharePoint list where:
- **Status** column = `Complete`
- **Billing** column = `Yes`

---

## Solution: OData Filter Query (Get Items Action)

Use the **Get items** action from the SharePoint connector in Power Automate and set the **Filter Query** field to:

```
Status eq 'Complete' and Billing eq 'Yes'
```

### Steps

1. In your Power Automate flow, add the **SharePoint – Get items** action.
2. Set **Site Address** to your SharePoint site URL.
3. Set **List Name** to the name of your SharePoint list.
4. Click **Show advanced options**.
5. In the **Filter Query** field, enter:

   ```
   Status eq 'Complete' and Billing eq 'Yes'
   ```

6. Use the returned array of items in subsequent steps of your flow.

---

## Notes on Column Names

Power Automate OData filter queries use the **internal (static) column name**, not the display name. If your columns were renamed or have spaces, the internal name may differ.

To find a column's internal name:
1. Go to your SharePoint list.
2. Click the column header → **Column settings** → **Edit**.
3. The internal name appears in the browser URL as the `Field=` parameter.

Common examples:
| Display Name | Possible Internal Name |
|---|---|
| Status | `Status` |
| Billing | `Billing` or `Billing_x0020_` (if it contains spaces) |

Update the filter query to match the actual internal names, for example:

```
Status eq 'Complete' and Billing_x0020_Status eq 'Yes'
```

---

## Alternative: Filter an Array Inside the Flow

If you have already retrieved items and need to filter them within the flow using an expression, use the `filter` function:

```
filter(body('Get_items')?['value'], and(equals(item()?['Status'], 'Complete'), equals(item()?['Billing'], 'Yes')))
```

Place this expression in a **Filter array** action:
- **From**: `body('Get_items')?['value']`  
- **Condition** (Advanced mode):

  ```
  @and(equals(item()?['Status'], 'Complete'), equals(item()?['Billing'], 'Yes'))
  ```

---

## Full Flow Example

```
Trigger: Recurrence (e.g., every day)
  │
  └─► SharePoint – Get items
        Site Address : https://yourtenant.sharepoint.com/sites/yoursite
        List Name    : YourListName
        Filter Query : Status eq 'Complete' and Billing eq 'Yes'
  │
  └─► Apply to each (items returned)
        │
        └─► [Your actions here, e.g., send email, update record, etc.]
```
