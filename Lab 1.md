### Setup personal Power Platform Environment

1. Visit Power Platform Admin Center at https://admin.powerplatform.microsoft.com/
2. Create new environment with follow parameters:
- Name = [Dev] Workshop **YourSurname**
- Type = Trial
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
```
ThisItem.Device&" ("&ThisItem.Code&")"
```
3. Now lest add one more control into line template in order to display device's price
4. Select first row of the Gallery and on Insert tab click Label
5. Adjust label size and place it in right corner
6. Set the Text property of the Label to the following value: 
```
Text(ThisItem.UnitPrice, "[$-en-US]$#.00")
```
7. The final result should look like this:

![ss1](https://content.screencast.com/users/Jack8647/folders/Capture/media/19af2fc0-7b0e-404c-a98f-b1e1807041aa/LWR_Recording.png)

If you will go and add new line to Excel document by yourself - PowerApp will not update a list.
In order to trigger an update manually - let's add a Refresh button to the screen.

1. Add a Button control to the form and name it MS_RefreshButton
2. Set Text property to 'Refresh' and enter follow code inside OnSelect property:
```
Refresh(Table1)
```

### Add search functionality to our Gallery

1. Add a Text Input control on top of your Gallery
2. Rename Text Input to 'MS_SearchInput'
3. Inspect properties of MS_SearchInput and set follow values:
- Default Text = ""
- Hint Text = Search
- Clear Button = On
4. Switch to Gallery control
5. Modify property Items with follow value:
```
Search(Table1, MS_SearchInput.Text, "Code", "Device", "Vendor")
```
Try to test your search by entering text. Make sure it can search by any text field of your table.
For users usually inconvinient to type device code manully, lets provide them possibility to enter code automatically by scanning QR code.

1. Modify OnVisible property of MainScreen and initialize a variable:
```
UpdateContext({barcodeValue: Blank()})
```
2. Modify Default property of MS_SearchInput with follow value:
```
barcodeValue
```
3. Add Media->Barcode Scanner control to the form and name it MS_BarcodeScanner
4. Modify OnScan property of MS_BarcodeScanner control and set follow value:
```
UpdateContext({barcodeValue: MS_BarcodeScanner.Value})
```
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

5. Rename controls inside EIS_EditForm in order to match our naming convention:

![ss6](https://content.screencast.com/users/Jack8647/folders/Capture/media/520cff18-7922-43b6-81e1-d6e2c53876f7/LWR_Recording.png)

5. Set Item property of EIS_EditForm to: 
```
selectedItem
```
6. Add two buttons to the screen and name it EIS_SaveButton and EIS_CancelButton. 
7. Modify buttons' display text accordinly.
8. Modify OnSelect property of EIS_CancelButton with following value:
```
Back()
```
9. Modify OnSelect property of EIS_SaveButton with next code:
```
SubmitForm(EIS_EditForm); Select(EIS_CancelButton);
```
Our edit screen is ready, it's time to connect it with main screen.
> Note: we used buttons like a functions. Cancel button just return user to previous screen, and Save button submits a form and then call Cancel button. By this way you can avoid code duplication.

1. Return to MainScreen and select DeviceGallery control
2. Find property OnSelect and insert follow code:
```
Navigate(EditItemScreen, ScreenTransition.Cover, { selectedItem: ThisItem })
```
3. Add new button to the MainScreen and name it MS_CreateButton
4. Set following code to property OnSelect of MS_CreateButton:
```
Navigate(EditItemScreen, ScreenTransition.Cover, { selectedItem: Defaults(Table1) })
```
That's it. Our edit form is connect to main screen and ready for use. Make a couple of tests - add new device and edit an exisitng one.

![ss4](https://content.screencast.com/users/Jack8647/folders/Capture/media/0d0f6dcf-7463-4ca1-93e3-a7e52ff07127/LWR_Recording.png)

### Implementing data validation

Our edit form works as well, but it doesn't validate input unfortenately, so users can enter whatever they want or leave fields blank. 
Lets fix it by implementing follow rules - all fields must be mandatory and UnitPrice value must be greater than zero.

1. Go to EditForm screen
2. Expand EIS_EditForm control and select Device_DataCard control
3. Open Advanced properties and click "Unlock to change properties"
4. Set property Required to true
5. Repeat the same for 3 other DataCard controls

Now all fields in our form marked as required. Try to test it again.
The last thing is implementing custom validation - UnitPrice must be greater than zero.

1. Select Save button control
2. Modify OnSelect property with follow code:
```
If(Value(UnitPriceDataCardValue.Text)>0,     
    SubmitForm(EIS_EditForm);     
    Back(),
    Notify("Unit Price must be greater than zero. Fix and try again", NotificationType.Warning)
);
```
3. Try to test. When you enter incorrect UnitPrice amount - a warning popup message must arrive.

![](https://content.screencast.com/users/Jack8647/folders/Capture/media/414fa1fe-6e14-413e-b0c3-60e228f0ecfc/LWR_Recording.png)

### Integration with Power Automate

Lets assume our device list is a purchase list. Everyone one can request new equipment but manager must approve purchase first.
In order to do this we will create a Power Automate approval flow and integrate it with our PowerApp.

1. Go to https://flow.microsoft.com
2. Make sure you slected a correct environment in the right corner - [Dev] Workshop **YourSurname**
> Nativelly PowerApp and Power Automate Flow must ve placed on the same environment
3. Click Create -> Instant flow and select PowerApps trigger:

![](https://content.screencast.com/users/Jack8647/folders/Capture/media/705e0e76-845f-45c7-9a23-82e8eddb4ddf/LWR_Recording.png)
4. Enter flow name - ws_ApprovalFlow_**TourSurname** and click Create
5. Click Add new step and in search bar type 'Approval'
6. Select 'Start and wait for approval' action
7. Select 'Approve/Reject - First to respond' approval type
8. Enter 'New equipment request' in Title field
9. Enter your own email to Assigned To field
10. Click Add New Action and type 'Excel' into search bar
11. Select Excel Online (Business) and then 'Add a row to the table' action
12. Perform authentication to Office in popup arrived
13. Set a path your Excel file and table:

![](https://content.screencast.com/users/Jack8647/folders/Capture/media/e52ae428-1637-4ad0-86ba-531ed36c6371/LWR_Recording.png)

14. After that Device,Vendor,UnitPrice and Code fields must be dynamically added to the step.
15. Click on each field and in Dynamic Content popup select 'Ask in PowerApps' action:

![](https://content.screencast.com/users/Jack8647/folders/Capture/media/3ac8eb94-cdb5-4ef2-a121-f16a7e85b3eb/LWR_Recording.png)

16. Inside __PowerAppsId__ property enter follow expression: substring(guid(), 0, 8)

![](https://content.screencast.com/users/Jack8647/folders/Capture/media/c87df7d1-0ddb-4fe3-834a-106cac86b35b/LWR_Recording.png)

17. Return to 'Start and wait for Approval' step
18. Modify property Details with following code:
```
Device: @{triggerBody()['Addarowintoatable_Vendor']} @{triggerBody()['Addarowintoatable_Device']} Price: $@{triggerBody()['Addarowintoatable_UnitPrice']} 
```

Don't forget to click Save and our flow is ready, lets integrate it with PowerApp.
We want a PowerApp to launch a flow instead of submitting line to Excel by itself.

1. Return to PowerApp and go to EditScreen
2. Click Action tab in the top menu and slect Power Automate
3. Select a flow you just cteated from the List
4. Now go to Save button OnSelect property and replace follow line:
```
SubmitForm(EIS_EditForm);
```
with 
```
ws_ApprovalFlow_YourSurname.Run(DeviceDataCardValue.Text, VendorDataCardValue.Text, CodeDataCardValue.Text, UnitPriceDataCardValue.Text);
```

That's it - try to test your app either in browser or on mobile device:

1. Click Save button
2. Go to your flow and inspect run history table
3. Open a line with state Running
4. As you can see flow is stopped on Approval step and waitng for approval
5. Go to your mailbox and find an email with Approval request
6. Click Approve button and return to flow run
7. Confirm that flow completed succesfully
8. Confirm new line was added to Excel sheet

If you have time - repeat the test but click Reject button instead of Approve in your mailbox.

### Introduce adaptive layout

As you might noticed, our app has fixed size on your phone and doesn't cover whole screen. Lets fix it and make more adaptive.

1. Go to File->Settings->Screen Size + orientation
2. Set Scale to Fit option to Off and click Apply
3. Save and Publish your app. Install a new version on your phone and see a difference in layout.

As you see there are much more free spaces now, but controls are not aligned and app looks ugly. Lets fix it by tuning our controls to fit any screen.

1. Go to File->Settings->Advanced Settings
2. Search for Container Control feature and enable it
3. Go to main screen
4. Select DevicesGallery control and modify Width property with follow value: App.Width
5. Add Container control to the form and name it MS_ButtonsContainer
6. Move MS_CreateButton and MS_RefreshButton inside container
7. Set container properties to follow:
```
X = (App.Width - MS_ButtonsContainer.Width)/2
Y = App.Height - MS_ButtonsContainer.Height
```

Now your buttons must be centered and attached to the bottom on screen of any size.
Publish and test a new version on your phone.

8. By the same way fix layout of other controls on both forms
