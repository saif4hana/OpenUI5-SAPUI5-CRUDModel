# nl.barrydam.CRUDModel (OpenUI5-SAPUI5-CRUDModel)

nl.barrydam.CRUDModel allows you use a php webserver as a data-service 

[OpenUI5](http://openui5.org/)/[SAPUI5](https://sapui5.hana.ondemand.com) Model implementation for [mevdschee/php-crud-api](https://github.com/mevdschee/php-crud-api) and [mevdschee/php-api-auth](https://github.com/mevdschee/php-api-auth)

The CRUDModel is an extension of [sap.ui.model.json.JSONModel](https://sapui5.hana.ondemand.com/#docs/api/symbols/sap.ui.model.json.JSONModel.html) and you can work with it as in a similar way to [sap.ui.model.odata.ODataModel](https://sapui5.hana.ondemand.com/#docs/api/symbols/sap.ui.model.odata.ODataModel.html)

### Database Note
On creating a db table, make sure to define a primary key! 

## Setup in UI5
1. Add the CRUDModel.js file to you project subfolder library/bd/model/
2. In your Component.js file add the following lines
```javascript
jQuery.sap.registerModulePath("nl.barrydam", "library/bd/");
jQuery.sap.require("nl.barrydam.model.CRUDModel");
```

## Example
In your Component.js: 
```javascript
var oServiceExample = new nl.barrydam.model.CRUDModel("http://www.yourapirurl.com/service/");
this.setModel(oServiceExample, "ServiceExample");
// if authentication is needed:
var that = this;
oServiceExample.attachLogout(function(){
	var oRouter = that.getRouter();
	oRouter.navTo('Login');
	oRouter.stop(); // stop listening to the router
});
oServiceExample.attachLogin(function(){
	var oRouter = that.getRouter();
	oRouter.initialize(); // restart the router
	oRouter.navTo('Home');
});
```
In your xml view the following will auto trigger a get request to the service and output your results in a list:
```xml
<List
 items="{
	path : 'ServiceExample>/students',
	filters: [{
        path: 'gender',
        operator: 'EQ',
        value1: 'Male'
    }],
    sorter: [{
	    path:  'firstName',
	    descending: false
    }]
}">
	<items>
		<StandardListItem title="{ServiceExample>firstName} {ServiceExample>lastName}" />
	</items>
</List>
```

## Constructor
**new nl.barrydam.model.CRUDModel(sServiceUrl, mParameters?)**

Constructor for a new CRUDModel.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sServiceUrl** 					| Base uri of the service to request data from; additional URL parameters appended here will be appended to every request can be passed with the mParameters object as well
*{object}*			| **mParameters?**					| (optional) a map which contains the following parameter properties:
*{string}*			| **mParameters.bindingMode?**		| Set the binding mode for the model (OneWay or TwoWay, default = TwoWay)
*{string}*			| **mParameters.user?**				| User for the service
*{string}*			| **mParameters.password?**			| Password for service
*{string}*			| **mParameters.primaryKey?**		| Database primary key (default = 'id')
*{map}*				| **mParameters.serviceUrlParams?**	| Map of URL parameters - these parameters will be attached to all requests
*{boolean}*			| **mParameters.useBatch?**			| When true all POST PUT and DELETE requests will be sent in batch requests (default = false)

## Events
The following events can be triggered by the CRUDModel: Login, Logout, MetadataFailed, MetadataLoaded, Reload.

You can catch them by using the following methods: attachLogin, attachLogout, attachMetadataFailed, attachMetadataLoaded, attachReload.

Or once: You can catch them by using the following methods: attachLoginOnce, attachLogoutOnce, attachMetadataFailedOnce, attachMetadataLoadedOnce, attachReloadOnce.

**Example**
```javascript
oServiceExample.attachLogin(function() {
	alert("User has logged in");
});
```

## Method detail

[callFunction](#callFunction),
[clearBatch](#clearBatch),
[create](#createspath-odata-mparameters),
[createBatchOperation](#createbatchoperationspath-smethod-odata),
[createEntry](#createentryspath-odata),
[exportToCSV](#exporttocsvspath-mparameters),
[getPrimaryKey](#getprimarykeystablename),
[hasPendingChanges](#hasPendingChanges),
[login](#loginsuser-spassword-mparameters),
[logout](#logout),
[read](#readspath-mparameters),
[reload](#reloadfnsuccess-fnerror),
[remove](#removespath-mparameters),
[resetChanges](#resetchangesfnsuccess-fnerror),
[setProperty](#setpropertyspath-ovalue),
[setUseBatch](#setusebatchbusebatch),
[submitChanges](#submitchangesfnsuccess-fnerror),
[update](#updatespath-odata-mparameters).
[upload](#uploadofile-mparameters).


Note: nl.barrydam.CRUDModel is an extension of [sap.ui.model.json.JSONModel](https://sapui5.hana.ondemand.com/#docs/api/symbols/sap.ui.model.json.JSONModel.html) all methods of the JSONModel can also be used except the "loadData" method

### callFunction(sFunctionName, mParameters?)
Trigger a request to the function import OData service that was specified in the model constructor.

### clearBatch()
Removes all operations in the current batch.


### create(sPath, oData, mparameters?)
Trigger a POST request to the CRUD service that was specified in the model constructor. Please note that deep creates are not supported and may not work. 

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| 	A string containing the path to the collection where an entry should be created. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{object}*			| **oData** 						| Data of the entry that should be created.
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| a callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{boolean}*			| **mParameters.async?** 			| Whether the request should be done asynchronously. Default: false Please be advised that this feature is officially unsupported as using asynchronous requests can lead to data inconsistencies if the application does not make sure that the request was completed before continuing to work with the data.


### createBatchOperation(sPath, sMethod, oData?)
Creates a single batch operation (read or change operation) which can be used in a batch request.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| 	A string containing the path to the collection where an entry should be created. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{string}*			| **sMethod** 						| for the batch operation. Possible values are GET, PUT, MERGE, POST, DELETE
*{object}*			| **oData?** 						| optional data payload which should be created, updated, deleted in a change batch operation.


### createEntry(sPath, oData?)
Creates a new entry object which is described by the metadata of the entity type of the specified sPath Name. A context object is returned which can be used to bind against the newly created object.

For each created entry a request is created and stored in a request queue. The request queue can be submitted by calling [submitChanges](#submitChanges). To delete a created entry from the request queue call [deleteCreateEntry](#deleteCreateEntry).

The optional oData parameter can be used as follows:

If oData is not specified, all properties in the entity type will be included in the created entry.

If there are no values specified the properties will have undefined values.

Please note that deep creates (including data defined by navigationproperties) are not supported

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| Name of the path to the collection.
*{object}*			| **oData** 						| An object that specifies a set of properties or the entry

### exportToCSV(sPath, mparameters?)
Export the data to a CSV file.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| 	A string containing the path to the collection where an entry should be created. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{object}*			| **oData** 						| Data of the entry that should be created.
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| A callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{array}*			| **mParameters.excludeColumns?** 	| Array of columns which should be excluded in the CVS export, default false (no columns will be excluded)
*{string}*			| **mParameters.filename?** 		| Filename for the CSV file. If not set, the filename is generated by combining the sPath + the current timestamp
*{function}*		| **mParameters.formatValue?** 		| A callback function which is called when each value is parsed. return the value you want to show in the CSV row.* The handler has the following parameters (string) sColumn and (mixed) value (*see example below) 
*{function}*		| **mParameters.formatColumnName?** | A callback function which is called on creating the CSV header columns. return the columnname you would like tho show in the first csv row.* The handler has the following parameter (string) sName. (*see example below)
*{array}*			| **mParameters.includeColumns?** 	| Array of columns which should only be included in the CSV export, default false (all columns will be included)
*{boolean}*			| **mParameters.refreshData?** 		| If set to True, a fresh set of data will be requested. If set to false, the current model data would be used (if there is no data available a new request will be fired towards the api), default false
*{string}*			| **mParameters.separatorChar?** 	| default: ,

Example:
```js
/*
 This example below will result in a my-custom-filename.csv download
 */
API.exportToCSV('example', {
	error: function(sMessage, sType) {
		if (sType == "no_data") {
			alert("No data available")
		} else {
			alert("Error creating export file");
		}
		oBtn.setBusy(false);
	},
	separatorChar  : ";",,
	filename       : "my-custom-filename",
	formatValue: function(sColumn, sValue) {
		switch (sColumn) {
			case "birthDate":
				sValue = (sValue) ? moment(sDate).format("LL") : "" ;
				break;
			case "temporary":
				sValue = (sValue=== 0) ? "No" : "Yes" ;
				break;
		}
		return sValue;
	},
	formatColumnName: function(sName) {
		switch (sName) {
			case "birthDate":
				sName = "Birth Date"
				break;

			case "temporary":
				sName = "Only temporary?"
				break;
		}
		return sName;
	}
});
```			


### getPrimaryKey(sTablename?)
Get the table primary key. If there is no primary key set in the db, it will return the default primary key which is 'id', the default primaryKey can be changed by setPrimaryKey(); or by passing primaryKey in the class constructor

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sTablename** 					| DB Table name

**returns**
*{string}*	table specific primary key


### hasPendingChanges()
Checks if there exist pending changes in the model created by the setProperty method.

**returns**
*{boolean}*	true/false


### login(sUser, sPassword, mParameters?)
When the CRUD-api has [php-api-auth](https://github.com/mevdschee/php-api-auth) implemented, you first need to login. 

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sUser** 						| User for the service
*{string}*			| **sPassword** 					| password for the service
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| a callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{boolean}*			| **mParameters.async?** 			| Whether the request should be done asynchronously. Default: false Please be advised that this feature is officially unsupported as using asynchronous requests can lead to data inconsistencies if the application does not make sure that the request was completed before continuing to work with the data.


### logout()
Logs the user out when the CRUD-api has [php-api-auth](https://github.com/mevdschee/php-api-auth) implemented.


### read(sPath, mParameters?)
Trigger a GET request to the odata service that was specified in the model constructor. The data will not be stored in the model. The requested data is returned with the response.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| 	A string containing the path to the collection where an entry should be created. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| a callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{boolean}*			| **mParameters.async?** 			| Whether the request should be done asynchronously. Default: false Please be advised that this feature is officially unsupported as using asynchronous requests can lead to data inconsistencies if the application does not make sure that the request was completed before continuing to work with the data.
*{array}*			| **mParameters.filters?**			| An array of sap.ui.model.Filter to be included in the request URL


### reload(sPath?, mParameters?)
Reloads the data from the server and keeps the collected changes.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath**							| Optional, if set it only reloads the givin path data, else all data will be reloaded
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 					| 	a callback function which is called when the data has been successfully updated. The handler can have the following parameters: oData (only passed if the sPath is passed)
*{function}*		| **mParameters.error?** 						| 	a callback function which is called when the request failed. 


### remove(sPath, mParameters?)
Trigger a DELETE request to the odata service that was specified in the model constructor.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| 	A string containing the path to the data that should be removed. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{map}*				| **mParameters?** 					| Optional parameter map containing any of the following properties:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| a callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{boolean}*			| **mParameters.async?** 			| Whether the request should be done asynchronously. Default: false Please be advised that this feature is officially unsupported as using asynchronous requests can lead to data inconsistencies if the application does not make sure that the request was completed before continuing to work with the data.


### resetChanges(fnSuccess?, fnError?)
Resets the collected changes by the setProperty method and reloads the data from the server.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{function}*		| **fnSuccess?** 					| 	a callback function which is called when the data has been successfully updated. The handler can have the following parameters: oData and response.
*{function}*		| **fnError?** 						| 	a callback function which is called when the request failed. 

### setProperty(sPath, oValue)
Sets a new value for the given property sPropertyName in the model without triggering a server request. This can be done by the [submitChanges](#submitChanges) method.
Note: Only one entry of one collection can be updated at once. Otherwise a fireRejectChange event is fired.

Before updating a different entry the existing changes of the current entry have to be submitted or resetted by the corresponding methods: [submitChanges](#submitChanges), resetChanges.

IMPORTANT: All pending changes are resetted in the model if the application triggeres any kind of refresh on that entry. Make sure to submit the pending changes first. To determine if there are any pending changes call the [hasPendingChanges](#hasPendingChanges) method.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| Path of the property to set
*{any}*				| **oValue** 						| value to set the property to

**Returns** *{boolean}*	true if the value was set correctly and false if errors occurred like the entry was not found or another entry was already updated.


### setUseBatch(bUseBatch?)
Enable/Disable batch for all requests

**Parameters:**
*{boolean}* **boolean?** 	whether the requests should be encapsulated in a batch request


### submitChanges(fnSuccess?, fnError?)
Submits the collected changes which were collected by the setProperty method. A MERGE request will be triggered to only update the changed properties. If a URI with a $expand System Query Option was used then the expand entries will be removed from the collected changes. Changes to this entries should be done on the entry itself. So no deep updates are supported.

**Parameters:**

Type 				| Variable 							| Description
--- 				| --- 								| ---
*{function}*		| **fnSuccess?** 					| 	a callback function which is called when the data has been successfully updated. The handler can have the following parameters: oData and response.
*{function}*		| **fnError?** 						| 	a callback function which is called when the request failed. The handler can have the parameter: oError which contains additional error information

**Returns** *{object}* An object which has an abort function to abort the current request.




Type 				| Variable 							| Description
--- 				| --- 								| ---
*{string}*			| **sPath** 						| A string containing the path to the data that should be updated. The path is concatenated to the sServiceUrl which was specified in the model constructor.
*{object}*			| **oData** 						| Data of the entry that should be updated.
*{map}*				| **mParameters?** 					| Optional, can contain the following attributes:
*{function}*		| **mParameters.success?** 			| A callback function which is called when the data has been successfully retrieved. The handler can have the following parameters: oData and response.created.
*{function}*		| **mParameters.error?** 			| a callback function which is called when the request failed. The handler can have the parameter oError which contains additional error information.
*{boolean}*			| **mParameters.async?** 			| Whether the request should be done asynchronously. Default: false Please be advised that this feature is officially unsupported as using asynchronous requests can lead to data inconsistencies if the application does not make sure that the request was completed before continuing to work with the data.


### upload(oFile, mParameters?)
Upload a file towards the api
description soon.


## BUY ME A BEER
[![PayPayl donate button](https://img.shields.io/badge/paypal-donate-yellow.svg)](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=XX68BNMVCD7YS "Donate once-off to this project using Paypal")