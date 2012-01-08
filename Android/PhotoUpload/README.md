
Introduction
====

PhotoUpload is an Android application which gets an image from the assets folder and uploads the image in a chute. It includes Chute SDK library.


Setup
====

- Create a new Android project or open an existing one.
- Copy the classes and resources into your project.
- Add the required permissions to the manifest:

 ```
  <uses-permission android:name="android.permission.INTERNET" />
  <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
  <uses-permission android:name="android.permission.GET_ACCOUNTS" />
  <uses-permission android:name="android.permission.MANAGE_ACCOUNTS" />
  <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
  <uses-permission android:name="android.permission.READ_PHONE_STATE" />
  <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
  <uses-permission android:name="android.permission.WAKE_LOCK" />
 ```
- Register the activities into the manifest:
  
    ```
    <activity
            android:label="@string/app_name"
            android:name=".PhotoUploadActivity" >
            <intent-filter >
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    ```
- Register the service into the manifest:

  ```
  <service android:name="com.chute.sdk.api.GCHttpService" />
  ```
  
  
Key Concepts
========

## GCLocalAssetCollection
Collection of GCLocalAssetModel.

## GCLocalAssetModel
Asset model which consists of: name, asset ID, file, asset status and fileMD5.

## GCChuteCollection
Collection of GCChuteModel.

## GCChuteModel
Model containing all the characteristics for a given chute.

  
Usage
========

## Starting the Activity
The first thing that has to be done is creation of new GCAccount. Random token is set as a password:
<pre><code>
    GCAccount.getInstance(this).setUsername("TestUser");
	GCAccount.getInstance(this).setPassword(
		"3115093a84aa3c4b696f9e5e0356826085e6c2810e55931ccd4d227ba70cfdbb");
</code></pre>  
PhotoUploadActivity consists of "Upload File" button, progress bar and ImageView for displaying the uploaded image.
When "Upload File" button is clicked, new GCLocalAssetModel is created. The image that can be found in the assets folder is copied
on SDCard and set to the GCLocalAssetModel. 
<pre><code>
GCLocalAssetModel asset = new GCLocalAssetModel();
	    try {
		asset.setFile(FileUtil.copyAsset(getApplicationContext(), "droid4.jpg"));
	    } catch (IOException e) {
		Log.w(TAG, "", e);
	    }  
</code></pre> 
New GCLocalAssetCollection is created and the previously created asset is added to the collection.
New GCChuteModel is created with a random chuteId and added to the GCChuteCollection.
<pre><code>
        GCLocalAssetCollection assetCollection = new GCLocalAssetCollection();
	    assetCollection.add(asset);
	    GCChuteModel chuteModel = new GCChuteModel();
	    chuteModel.setId("1183"); // Test ID for a public chute called
	    GCChuteCollection chuteCollection = new GCChuteCollection();
	    chuteCollection.add(chuteModel);
</code></pre> 
PhotoUploadActivity contains AsyncTask that creates parcel from assets and chutes.
<pre><code>
 public void createParcel(GCLocalAssetCollection assets, GCChuteCollection chutes) {
	GCParcel.create(getApplicationContext(), assets, chutes,
		new GCCreateParcelsUploadsListParser(), new GCParcelCreateCallback())
		.executeAsync();
    }
</code></pre> 
The AsyncTask returns response data equivalent to GCLocalAssetCollection needed for image upload.
Another AsyncTask is executed which successfully uploads the image in the chute.
<pre><code>
            GCAssets.upload(getApplicationContext(),
			new GCUploadProgressListenerImplementation(), new GCStringResponse(),
			new GCHttpUploadCallback(), responseData).executeAsync();
</code></pre> 				    	    