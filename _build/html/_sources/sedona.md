# Sedona/EasyIO
## Hydronic Router App
### Getting Started
* With the laptop connected via ethernet to the EasyIO controller and the controller powered on, open cpt.exe from the Windows start menu. Go to the FS-32 tab and select Open on the Hydronic Router app.
```{image} ./images/CPT_Home_Screen.png
:alt: CPT Home Screen
:class: bg-primary mb-1
:width: 800px
:align: center
```
* From the home page of the app, 5 folders are visible. The Project folder contains the inputs, outputs, and operations for the hydronic router. The BACnetService folder connects the controller to BACnet devices like the thermostat and power meter. The sqlite3Service hosts the SQL tables which collect data from the router. 
```{image} ./images/CPT_App_Home.png
:alt: CPT App Home
:class: bg-primary mb-1
:width: 800px
:align: center
```
* To navigate, double-click on folders or objects to open them. Use the Back arrow at the top left to exit a layer of a folder or object. You can also double-click on folders and objects in the Navigation pane in the top left of the screen.
* To create objects, drag them onto the wiresheet from the kits in the Library pane in the bottom left of the screen. Edit them with the Properties pane on the right of the screen, and tab to the Links pane at the bottom to view links. The objects will automatically be listed in the Navigation pane in the order they are created. Carefully drag object names in this pane to reorder them without accidentally nesting them.
* To select objects, drag to highlight on the wiresheet or hold down Ctrl while selecting objects. You can’t select multiple objects at once in the Navigation pane.
* To create links, click and drag between fields on objects in the same wiresheet. For objects on different wiresheets, right-click an object and select Make Link, or select the object and press Ctrl+L. This will bring up a Make Link pop-up where you can choose the fields you want to connect and select Link.
* To nest objects, drag an object onto another on the wiresheet, or drag the object name in the Navigation pane onto another object name. Links into the nested components are usually preserved, as are links between components if multiple components are nested together. Links out of the nested components will have to be re-created.

### Zone Valves
The ZoneValveX control signals are connected to UO’s 1 through 4 with 2-10 VDC output. The ZoneFeedbackX signals are connected to UI’s 1 through 4 with 2-10 VDC input. They show the real-time position of the zone valve actuators and are connected to logic in the Pump folder, preventing the pump from turning on without at least one zone valve open.

### Flow Meters
Each FlowX_LPM object displays the zone flow rate in L/min. Background processing of signals from the flow meters is hidden in the FlowMeters folder.

In the FlowMeters folder, FlowMeterX signals are connected to UI’s 9 through 12 with 0-5 VDC input, and averaged over 10 seconds to smooth the signal. The average is then divided by Const_Flow in each FlowX_LPM object. Const_Flow converts the voltage value to a L/min value, and is equal to 5 divided by the Max Flow Rate value in the Arduino code. This is currently set to 8.0 L/min for a Const_Flow value of 0.625. If the max flow rate is updated in the arduino code, this value must be updated too! 
```{image} ./images/CPT_FlowMeters_Folder.png
:alt: CPT FlowMeters Folder
:class: bg-primary mb-1
:width: 800px
:align: center
```

### Pump
PumpInput is a WriteFloat which holds the 4-20mA control signal value to be delivered to the pump. The background logic to prevent the pump from turning on when it shouldn’t is hidden in the Pump folder. 

In the Pump folder, the FloatSwitch connected to UI5 detects whether the water level is higher than the outlet of the Chilled Water Tank and outputs true if this is true. The ZoneX_Open objects compare the ZoneFeedbackX values to a threshold of 2.05 to determine if each zone is open. AnyZoneOpen outputs true if at least on of the four zones is open. PumpConditions outputs true if both the water level and zone open conditions are met, switching the output of PumpSwitch from 0 to the PumpInput value. This is connected to UO5 to control the pump with a 4-20mA output.

### Power, Temperature, & Humidity
Power, AirTemp, and RelHumidity are WriteFloat objects that display values communicated via BACnet from the power meter and thermostat. 

In the TempSensors folder, the TempIn signal is connected to UI7, the TempOutX signals are connected to UI’s 13 through 16, and the ObjTemp (thermistor) signal is connected to UI6. These resistance input values are averaged over 10 seconds to smooth the signals. In the Project folder, TempIn_C, TempOutX_C, and ObjTemp_C are Linearize objects that convert the resistance value to a temperature in celsius using a curve for 10k2 thermistors from Belimo. 

### Fan
The Fan control signal is connected to UO7 with 0-10 VDC output.

In the AirFlow folder, the PressureSensor is connected to UI8 with a 0-10 VDC input signal, and averaged over 15 seconds to smooth the signal. In the Project folder, the average is divided by Const_Pressure to convert from voltage to pressure in StaticPressure. Const_Pressure is determined by the pressure sensor range setting, set with the white dial inside the case. 

The AirFlow folder also contains StaticToVelocity which multiples the StaticPressure value by Const_Bernoulli for the first step in converting from static pressure to air velocity. Const_Bernoulli is equal to 2 divided by the air density in kg/m^3. In the Project folder, AirVelocity_MPS takes the square root of the StaticToVelocity output to display the air velocity in m/s. 

These conversions from static pressure to velocity are derived from the Bernoulli Equation:
$P_1 + \frac12 \rho v_1^2 + \rho g h_1 = P_2 + \frac12 \rho v_2^2 + \rho g h_2$

$h_1 = h_2, v_2 = 0,  P_s = P_2 - P_1$

$P_s =  \frac12 \rho v_1^2$

$v_1 = \sqrt{\frac{2P_s}{\rho}}$

### Set Points & PID Loops
The set points are WriteFloat objects that can be changed to the desired set point value and connected to the PID Loops setPoint fields. The in fields of the PID loops can be connected to a temperature or humidity output value, and the out fields connected to the in fields of Fan, PumpInput, or a ZoneValve object. New PID Loops can be created from the EasyIO Energy kit.

## Data Collection
### Recording a new data point:
* Double-click the sqlite3Service object in your app to view its wiresheet (under EasyIOFS in Navigation). If you’re using a new app you’ll need to follow the steps in SQLite User Guide to set this up.
```{image} ./images/CPT_SQLiteService.png
:alt: CPT SQLiteService Wiresheet
:class: bg-primary mb-1
:width: 800px
:align: center
```
* Find the easyioFGSql kit in the list of kits in your library. Drag an sqlTable object onto the wiresheet (make sure you’re inside sqlite3Service). Type the table name into the tableNameBuf field. Change the enable field to true (it won’t start recording until later steps). Change recordType, recordInterval, and maxRecord as desired. Double-click on this new table or an existing table to view its wiresheet.
```{image} ./images/CPT_SQLTable.png
:alt: CPT SQL Table Wiresheet
:class: bg-primary mb-1
:width: 800px
:align: center
```
* From the easyioFGSql kit in the library, drag your desired data type object onto the wiresheet (you probably want sqlFloat). Type the data point/column name into the columnName field. The columnReady field should change to true when you do this. Right-click the object and select Make Link. Choose the source and the in field of the sql column and select Link. The in field of the column object should now display the data you want to collect.
```{image} ./images/CPT_SQLColumn.png
:alt: CPT SQL Column Wiresheet
:class: bg-primary mb-1
:width: 800px
:align: center
```
* Hit the Back arrow or double-click the sqlite3Service object in Navigation to return to its wiresheet. Right-click the table you’re using and hover over Actions. If using a new table, select syncStructure. If using an existing table, select updateStructure. For a new table, the currentRecord field should change from -1 to 0 to show it has started recording. The lastRecorded field will display the last time it recorded so you can check it’s updating correctly. If you go into the table wiresheet and click on one of your columns/data points, the lastRecordedValue will also display the last value it recorded for that column.

### Viewing data graphically:
* Double-click the graphs page under Graphics in Navigation. Here you can see generic, static representations of the graphics displayed in the HTML page for your app. Your library will automatically show the list of graphics kits.
* For History Line Charts: Drag a HistoryLineChart object onto the wiresheet from the chart kit in your library. Type the name of the table that you want to display data from in the tableName field (this should exactly match the tableName field in the sqlTable object). Type the name of the column you want to display data from in the colNameOfLineX field (again, this should exactly match the columnName field of the column object inside the table you’re referencing).
```{image} ./images/CPT_HistoryLineChart.png
:alt: CPT History Line Chart
:class: bg-primary mb-1
:width: 800px
:align: center
```
* For Meters and other non-history chart graphics: While viewing the graphs wiresheet, find the source of the data you want to display in Navigation and drag it onto the wiresheet. A Create Event Binding pop-up will appear. Select the Meter object from the chart kit, or another graphic object, and hit OK. The inputData field of the object should have a film strip/pencil icon to show that you have successfully connected your data source. Change the minOfRange and maxOfRange fields to fit your data.
```{image} ./images/CPT_Meter_EventBinding.png
:alt: CPT Meter Event Binding
:class: bg-primary mb-1
:width: 800px
:align: center
```
* Move and resize graphics by dragging as you would expect, or use the fields inside rect in each object to set position and dimensions.
* In the top toolbar, select Deploy (clockwise arrow icon) to publish your graphics so they can be viewed. Select OK on the pop-up. Once the files have been uploaded you can select Preview in the top toolbar (blue flag icon) to view your graphics in a webpage. The username and password if needed are admin and hellocpt.
```{image} ./images/CPT_Graphics.png
:alt: CPT Graphics Webpage
:class: bg-primary mb-1
:width: 800px
:align: center
```
* Select the time range of data you want to view using the dropdown menu on each History Line Chart, or select the range you want with your cursor. The y-axis is scaled automatically to the min and max values of all data in that table. Meter graphics update in real-time, but to display new History Line Chart data you will need to reload the page.

### Managing recorded data:
* In the webpage view of your graphics, select the Utilities button on the left-hand side and then DB Manager. Use the menu on the left of the new page that pops up to navigate the database and the tables stored in it. 
```{image} ./images/CPT_DBManager.png
:alt: CPT Database Manager Webpage
:class: bg-primary mb-1
:width: 800px
:align: center
```
* To edit or delete data in on your tables, select the table, select the rows you’re changing using the parameters at the top, and hit Show. Select Edit on a row to manually change its recorded values, or Delete to delete a whole row. This is useful if you have erroneous data messing up the y-axis scale on your graphics.
* To download the data from your tables, select Export from the tabs at the top and choose the table, download type, and filename before hitting Export to download it.
* To empty your tables (which can help you avoid re-downloading the same data in the future) select the database (easyio.db) from the left menu. In the Structure tab select Empty next to the table you want to clear the data from.
* To delete a table, select Drop next to the table in the Structure tab of the database (same as above).
