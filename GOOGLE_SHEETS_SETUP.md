# Google Sheets Integration Setup Guide

Follow these steps to connect your ID Generator to Google Sheets:

## Step 1: Create a Google Sheet
1. Go to [Google Sheets](https://sheets.google.com)
2. Create a new spreadsheet
3. Name it "Vehicle ID Generator Log"
4. In the first row, add these headers:
   - A1: **Timestamp**
   - B1: **Generated ID**
   - C1: **Prefix**
   - D1: **Plate Number**
   - E1: **Brand**
   - F1: **Driver Name**
   - G1: **Color**

## Step 2: Create Google Apps Script
1. In your Google Sheet, click **Extensions** → **Apps Script**
2. Delete any existing code
3. Paste this code:

```javascript
function doPost(e) {
  try {
    var sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    
    // Get parameters from POST request
    var timestamp = e.parameter.timestamp || new Date().toLocaleString();
    var id = e.parameter.id || '';
    var prefix = e.parameter.prefix || '';
    var plateNumber = e.parameter.plateNumber || '';
    var brand = e.parameter.brand || '';
    var driver = e.parameter.driver || '';
    var color = e.parameter.color || '';
    
    // Log for debugging
    Logger.log('Received data: ' + JSON.stringify({
      timestamp: timestamp,
      id: id,
      prefix: prefix,
      plateNumber: plateNumber,
      brand: brand,
      driver: driver,
      color: color
    }));
    
    // Append row to sheet
    sheet.appendRow([timestamp, id, prefix, plateNumber, brand, driver, color]);
    
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'success',
      'row': sheet.getLastRow()
    }))
    .setMimeType(ContentService.MimeType.JSON);
    
  } catch(error) {
    Logger.log('Error: ' + error.toString());
    return ContentService.createTextOutput(JSON.stringify({
      'result': 'error',
      'error': error.toString()
    }))
    .setMimeType(ContentService.MimeType.JSON);
  }
}
```

4. Click **Save** (disk icon)
5. Name the project "Vehicle ID Logger"

## Step 3: Deploy the Script
1. Click **Deploy** → **New deployment**
2. Click the gear icon ⚙️ next to "Select type"
3. Choose **Web app**
4. Fill in:
   - **Description**: "Vehicle ID Generator Logger"
   - **Execute as**: Me
   - **Who has access**: Anyone
5. Click **Deploy**
6. Click **Authorize access**
7. Choose your Google account
8. Click **Advanced** → **Go to Vehicle ID Logger (unsafe)**
9. Click **Allow**
10. **Copy the Web app URL** (it looks like: `https://script.google.com/macros/s/...../exec`)

## Step 4: Update Your Website
1. Open `index.html`
2. Find this line (around line 351):
   ```javascript
   const scriptURL = 'YOUR_GOOGLE_SCRIPT_URL_HERE';
   ```
3. Replace `'YOUR_GOOGLE_SCRIPT_URL_HERE'` with your copied URL:
   ```javascript
   const scriptURL = 'https://script.google.com/macros/s/YOUR_ACTUAL_URL/exec';
   ```
4. Save the file

## Step 5: Push to GitHub
```bash
git add .
git commit -m "Add Google Sheets integration"
git push
```

## Done! 🎉
Now every time someone generates an ID on your website, it will automatically be logged to your Google Sheet with all the details!

## Testing
1. Generate a test ID on your website
2. Check your Google Sheet
3. You should see a new row with all the data

## Troubleshooting
- If data isn't appearing, check the browser console (F12) for errors
- Make sure the script URL is correct
- Ensure "Who has access" is set to "Anyone" in the deployment settings
