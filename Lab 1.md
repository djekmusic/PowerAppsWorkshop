### Setup personal Power Platform Environment

1. Visit Power Platform Admin Center at https://admin.powerplatform.microsoft.com/
2. Create new environment with follow parameters:
- Name = [Dev] Workshop **YourSurname**
- Type = Sandbox
- Region = Europe
- Create database = Yes
- Language = English
- URL = ws-**YourSurname**
- Currency = USD
3. Environment provisioning will take some time. Complete next section while you are waiting.

### Prepare your data

All Apps are build around data, today we will use the most simple and common datasource - Excel Online
1. Go to https://www.office.com/ and Sign In if you are unauthorized
2. Launch Excel Online and create a new workbook
3. Enter test data from the example below:

 Device	| Vendor |	Code	| UnitPrice
--------|-------|-------|-------
Galaxy S20|	Samsung	|577458|	$1,200 
iPhone 11 Pro|	Apple|	115223|	$1,800 
XPeria 8|	Sony|	455487|	$900 

4. Go to tab Insert and click 'Table' button
5. Confirm range of your table and make sure checkbox 'My table has headers' is selected
6. Save workbook with name ws_Book_**YourSurname**

### Create your first PowerApp

1. Make sure the state of your environment is Ready
2. Go to https://make.powerapps.com/
3. In the right corner select an environment you just created - [Dev] Workshop **YourSurname**
4. Go to Apps in navigation menu
5. Click Add New Canvas App
6. Select Balnk App with Phone Layout template
7. Click File-Save, enter App's name in format ws_App_**YourSurname** and click Save button.
> Pay attention, if you close window without saving - whole your App will be lost

### Connect your PowerApp to Excel Online

1. Go to your PowerApp editor
2. In the left menu select Data and type into search bar 'OneDrive'
3. Select 'OneDrive for Business' connector from the list
4. Click Connect
5. Authorize into your work/lab account
6. Select previously created workbook ws_Book_**YourSurname**
7. Select the single avaliable table (Table1) and click Connect

### Display your data in PowerApp

Our App is connect to datasource, so it's time to use our data and display it on the screen.

1. Open Tree View from the left menu
2. Rename Screen1 to MainScreen
3. Switch to Insert tab and search for control named Vertical Gallery
4. Add it to the MainScreen
5. Select Gallery control on the Screen
6. Set property Items to value Table1
7. In the Gallery properties screen (on the right) click Edit Fields link
8. Map Title1 to column Device and Subtitle1 to column Vendor

Now Gallery displays the list of devices from our Excel, but as you might notice not all fields are present, lets fix it.

1. 

> <img src="https://barcode.tec-it.com/barcode.ashx?data=115223&code=QRCode&multiplebarcodes=false&translate-esc=false&unit=Fit&dpi=96&imagetype=Gif&rotation=0&color=%23000000&bgcolor=%23ffffff&codepage=&qunit=Mm&quiet=0&eclevel=L" />

