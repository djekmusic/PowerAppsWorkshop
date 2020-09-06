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
7. Close browser tab with your workbook

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

After that PowerApp will patch your Excel file and add aditional column - PowerApps_Id in order to clearly identify rows during Update/Delete operations.

8. Return back to Excel document and confirm new column was added.

### Display your data in PowerApp

Our App is connect to datasource, so it's time to use our data and display it on the screen.

1. Open Tree View from the left menu
2. Rename Screen1 to MainScreen
3. Switch to Insert tab and search for control named Vertical Gallery
4. Add it to the MainScreen and rename to MS_DevicesGallery
5. Select Gallery control on the Screen
6. Set property Items to value Table1
7. In the Gallery properties screen (on the right) click Edit Fields link
8. Map Title1 to column Device and Subtitle1 to column Vendor

Now Gallery displays the list of devices from our Excel, but as you might notice not all fields are present, lets fix it.

1. Select Title1 control on the first row of the Gallery
2. Change Text property to the follow value: 
> ThisItem.Device&" ("&ThisItem.Code&")"
3. Now lest add one more control into line template in order to display device's price
4. Select first row of the Gallery and on Insert tab click Label
5. Adjust label size and place it in right corner
6. Set the Text property of the Label to the following value: 
> Text(ThisItem.UnitPrice, "[$-en-US]$#.00")
7. The final result should look like this:

![ss1](https://content.screencast.com/users/Jack8647/folders/Capture/media/19af2fc0-7b0e-404c-a98f-b1e1807041aa/LWR_Recording.png)

### Add search functionality to our Gallery

1. Add a Text Input control on top of your Gallery
2. Rename Text Input to 'MS_SearchInput'
3. Inspect properties of MS_SearchInput and set follow values:
- Default Text = ""
- Hint Text = Search
- Clear Button = On
4. Switch to Gallery control
5. Modify property Items with follow value:
> Search(Table1, MS_SearchInput.Text, "Code", "Device", "Vendor")

Try to test your search by entering text. Make sure it can search by any text field of your table.
For users usually inconvinient to type device code manully, lets provide them possibility to enter code automatically by scanning QR code.

1. Modify OnVisible property of MainScreen and initialize a variable:
> UpdateContext({barcodeValue: Blank()})
2. Modify Default property of MS_SearchInput with follow value:
> barcodeValue
3. Add Media->Barcode Scanner control to the form and name it MS_BarcodeScanner
4. Modify OnScan property of MS_BarcodeScanner control and set follow value:
> UpdateContext({barcodeValue: MS_BarcodeScanner.Value})

Now we have 3 controls bound to each other: when barcode is scanned it's value through variable goes to Search input. When Search input is modified - Gallery automatically filters own content based on input. The final screen shpul look like this:

![ss2](https://content.screencast.com/users/Jack8647/folders/Capture/media/ff89bcd8-7ee0-421a-bdf4-ee7c4dfae038/LWR_Recording.png)

### First test

Now we have display functionality implemented - lets test how it works on mobile device. 
> Barcode scanner can't be tested in browser!

1. Save your app and click Publish button.
> When you save app changes are visible to you only, when you publish - all users recevie a new version
2. Take your phone and install Microsoft PowerApps app from GooglePlay/AppStore marketplace
3. Launch app and perform Sign In to your tenant
4. Now you should your app in the list
5. Launch it and confirm that data displayed and search works as well.
6. Use QR code below in order to test a barcode scanner:

![qr1](https://barcode.tec-it.com/barcode.ashx?data=115223&code=QRCode&multiplebarcodes=false&translate-esc=false&unit=Fit&dpi=96&imagetype=Gif&rotation=0&color=%23000000&bgcolor=%23ffffff&codepage=&qunit=Mm&quiet=0&eclevel=L)

### Implement Create/Update item functionality

Excellent we have a screen where our data is shown. Now it's time to modify this data, lets implement a possibility to create a new item and edit an existing one.
1. Add new screen and name it EditItemScreen
2. Insert Forms->Edit control to the form and name it EIS_EditForm
3. Set DataSource property of EIS_EditForm to Table1
4. Click on Fields property and add all avaliable columns to the list as shown below:

![ss3](https://content.screencast.com/users/Jack8647/folders/Capture/media/a65ccfa2-c7b5-4dc0-aead-b16a38a6b371/LWR_Recording.png)

5. Set Item property of EIS_EditForm to: 
> selectedItem
6. Add two buttons to the screen and name it EIS_SaveButton and EIS_CancelButton. 
7. Modify buttons' display text accordinly.
8. Modify OnSelect property of EIS_CancelButton with following value:
> Back()
9. Modify OnSelect property of EIS_SaveButton with next code:
> SubmitForm(EIS_EditForm); Select(EIS_CancelButton);

Our edit screen is ready, it's time to connect it with main screen.
> Note: we used buttons like a functions. Cancel button just return user to previous screen, and Save button submits a form and then call Cancel button. By this way you can avoid code duplication.

1. Return to MainScreen and select DeviceGallery control
2. Find property OnSelect and insert follow code:
> Navigate(EditItemScreen, ScreenTransition.Cover, { selectedItem: ThisItem })
3. Add new button to the MainScreen and name it MS_CreateButton
4. Set following code to property OnSelect of MS_CreateButton:
> Navigate(EditItemScreen, ScreenTransition.Cover, { selectedItem: Defaults(Table1) })

That's it. Our edit form is connect to main screen and ready for use. Make a couple of tests - add new device and edit an exisitng one.

![ss4](https://content.screencast.com/users/Jack8647/folders/Capture/media/0d0f6dcf-7463-4ca1-93e3-a7e52ff07127/LWR_Recording.png)

### Introduce adaptive layout

As you might noticed, our app has fixed size on your phone and doesn't cover whole screen. Lets fix it and make more adaptive.

1. Go to File->Settings->Screen Size + orientation
2. Set Scale to Fit option to Off and click Apply
3. Save and Publish your app. Install a new version on your phone and see a difference in layout.

As you see there are much more free spaces now, but controls are not aligned and app looks ugly. Lets fix it by tuning our controls to fit any screen.

1. Go to File->Settings->Settings
2. Search for Container Control feature and enable it
