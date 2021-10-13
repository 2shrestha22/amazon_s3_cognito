# amazon_s3_cognito


Amazon S3 plugin for Flutter

Unofficial Amazon S3 plugin written in Dart for Flutter. 

Only supports Amazon SDK up to 2.20(excluded), since starting with 2.20, the SDK dropped the deprecated 
"AWSS3TransferManager" which is still in use within this plugin.

https://github.com/aws-amplify/aws-sdk-ios/releases/tag/2.20.0 

The plugin is extension if flutter-amazon-s3 plugin which can be found here 
https://pub.dev/packages/flutter_amazon_s3. This plugin adds image list and delete functionality and also
it allows user to upload image when region and sub-region are different.

Plugin in maintained by fäm properties<no-reply@famproperties.com>.

## Usage
To use this plugin, add `amazon_s3_cognito` as a [dependency in your pubspec.yaml file](https://flutter.io/platform-plugins/).


```yaml
dependencies:
The package is android-x compatible
  amazon_s3_cognito: '^0.2.0' 
```

### Example




``` dart
import 'package:amazon_s3_cognito/amazon_s3_cognito.dart';
import 'package:amazon_s3_cognito/aws_region.dart';




//Use the below code to specify the region and sub region for image upload
//Also this method allows to upload all file type including images and pdf etc.


//imageData - this object will contain file details, like file namem file path, folder where to upload file inside bucket etc
//construct imagedata object
ImageData imageData = ImageData("uniqueFileName", filePath,
        uniqueId: "uniqueIdToTrackImage", imageUploadFolder: "folder to upload inside bucket");

//call AWS to upload file
//needMultipartUpload - only applicable for IOS, when your uploads are so large that they take more than 1 hour to complete set its value to true
String uploadedImageUrl = await AmazonS3Cognito.upload(String bucket, String identity, String region,
                  String subRegion, ImageData imageData,
                  {bool needMultipartUpload = false})


//we can now also upload multiple images via list and listener to its progress
//and upload changes via stream.

//create an event channel

  EventChannel _amazonS3Stream =
      EventChannel('amazon_s3_cognito_images_upload_steam');
  StreamSubscription? uploadListenerSubscription;

  //call _listenToFileUpload() in initstate method or when you want to
  //start listeneing to image upload
  //steeams pass back hashmap, as passing custom object is not supported yet
  //we can covert map to object in stream like below.

   void _listenToFileUpload() {
      //when you want to upload multi-files or listen to upload then
      //you get the image progress via this stream
      uploadListenerSubscription =
          _amazonS3Stream.receiveBroadcastStream().listen((event) {
        LinkedHashMap<Object?, Object?> map = event;
        print(map);
        ImageData imageData = ImageData.fromMap(map);
        //update the ui based on the object returned in stream
      });
    }

    //cancel subcrition of stream when you are done
    void dispose() {
        super.dispose();
        uploadListenerSubscription?.cancel();
    }

    //use multiple image upload method
    uploadImages(
          String bucket,
          String identity,
          String region,
          String subRegion,
          List<ImageData> imageData,
          bool needProgressUpdateAlso,
          {bool needMultipartUpload = false})


//use below code to delete an image
 String result = await AmazonS3Cognito.delete(
                       String bucket,
                       String identity,
                       String imageName,
                       String? folderInBucketWhereImgIsUploaded,
                       String region,
                       String subRegion)


            

        

```
          
## Installation


### Android

Inside AndroidManifest.xml register TransferService like below

 <application
 .....

 <service android:name= "com.amazonaws.mobileconnectors.s3.transferutility.TransferService" android:enabled="true" />

</application>

### iOS

inside your Appdelegate add following method


func application(_ application: UIApplication, handleEventsForBackgroundURLSession identifier: String, completionHandler: @escaping () -> Void) {
    // Store the completion handler.
    AWSS3TransferUtility.interceptApplication(application, handleEventsForBackgroundURLSession: identifier, completionHandler: completionHandler)
}

for documentation further read
https://docs.amplify.aws/sdk/storage/transfer-utility/q/platform/ios/#background-transfers

https://aws.amazon.com/blogs/mobile/amazon-s3-transfer-utility-for-ios/

### Authors
```
the plugin is created and maintained by fäm properties. 
Android version written by Prachi Shrivastava
IOS version written by Prachi Shrivastava
```
