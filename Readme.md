
# Table of Contents

- [💎 Definition](#definition)
- [⁉️ When might RXCacheManager be needed?](#when-might-RXCacheManager-be-needed?)
- [💡 Concept](#concept)
- [🗿 What type of server is required to run RXCM?](#what-type-of-server-is-required-to-run-RXCM?)
- [🍑 What are the benefits of using RXCM?](#what-are-the-benefits-of-using-RXCM?)
- [📖 How does web storage work that can be cached by the RXCM framework ?](#how-does-web-storage-work-that-can-be-cached-by-the-RXCM-framework-?)
- [🗂 Folder and file structure](#folder-and-file-structure)
   -  [The contents of the configuration file](#the-contents-of-the-configuration-file)
- [❔What are RXCMOneChange objects for?](#What-are-RXCMOneChange-objects-for?)
- [⚙️ What types of changes does RXCM support](#What-types-of-changes-does-RXCM-support)
- [🈚️ The syntax of the configuration dictionary from which the RXCMOneChange object will be created](#The-syntax-of-the-configuration-dictionary-from-which-the-RXCMOneChange-object-will-be-created)
    - [Adding](#Adding)
    - [Removal](#Removal)
    - [Renaming](#Renaming)
    - [Movement](#Movement)
    - [Redownloading](#Redownloading)
- [🛠 How to add RXCM support on the server?](#How-to-add-RXCM-support-on-the-server?)
- [🔧 What to change in the configuration files after changing the contents of the cache ?](#What-to-change-in-the-configuration-files-after-changing-the-contents-of-the-cache-?)
- [🎯 Connecting RXCM to a project](#Connecting-RXCM-to-a-project)
- [🚀 RXCM integration into the project](#RXCM-integration-into-the-project)
- [🏗 Cache manager management methods](#Cache-manager-management-methods)
- [💼 Snippets and additional features](#Snippets-and-additional-features)
    - [Adjustable type of Internet connection](#Adjustable-type-of-Internet-connection)
    - [Printing logs to console](#Printing-logs-to-console)
    - [Check for errors](#Check-for-errors)
    - [Initialize the downloaded files from the disk](#Initialize-the-downloaded-files-from-the-disk)
    - [Deleting the configuration object from NSUserDefault](#Deleting-the-configuration-object-from-NSUserDefault)



## Definition

**RXCM** (RXCacheManager) - This is a class that caches all changes that occur with files on the server to the device.

## When might RXCacheManager be needed?

**RXCM** may need to write a client-server application when you want to automatically apply the changes to the files that were made on the server.

For example you have a cooking app. On the server you added a json with a description of the new dish, **RXCM** detects the change of the web store and download this file.
Or if you have deleted a file or folder from the cloud, RXCM will also remove them from the device memory.


<br>
<br>

![5cc72762379ff](/Documentation/ScreeSnippet/RXCM_DEMO.gif)

<br>
<br>

## Concept

**RXCM** (RXCacheManager) - this is a class that receives information about the state of cloud storage from the server when the application is opened. Then **RXCM** compares the cache version on the device and the storage version in the cloud. Then commit the updates. 
![5cc72762379ff](/Documentation/ScreeSnippet/ThePrincipleOfWorkingRXCacheManager.png)

## What type of server is required to run RXCM?

Аthe algorithm of the framework **RXCacheManager** is very flexible and for full operation (caching all changes from the server to the device) you will need a cloud storage that has the ability to provide **DIRECT** links to the file.

For example, you can use the standard repository on **github**.

## What are the benefits of using RXCM?

To cache all changes on the server, you do not need to have your own server with a ready API.
You can use any cloud that can provide a direct link to the file.

But if you already have your own backend - it will only simplify the support of RCM configuration files.

## How does web storage work that can be cached by the RXCM framework ?

For your device to be able to compare storage versions, your cache configuration files must be stored somewhere.

#### Folder and file structure

1. **RXCacheConfig** - the folder in which all configuration files that describe version changes are stored.

2. **RXCacheContent** - the folder in which the user files to be cached are stored.

3. **RXCacheContent**.zip - archive the RXCacheContent folder.

4. **HistoryOfVersion** - the folder in which JSON files are stored that describes what changes were made between version A and B.

![5cc7281c60e9e](/Documentation/ScreeSnippet/structureAndArrangementOfFilesInRootDirectory.png)

#### The contents of the configuration file

1. **cacheStatus.json** 

This is a file that describes the current state of the cache.

(Symmetric object in the framework- **RCXMCacheStatus: NSObject**)

![5cc74f4759656](/Documentation/ScreeSnippet/cacheStatus2.png)

| Key                   | Explanation                                                                                                       |
| --------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `actualCacheVersion`  | Tells you what version number the cache has.                                                                      |
| `minimalCacheVersion` | The minimum cache version on the device for which updates from new versions are applied that are stored in the cloud. |

#### What is the "minimalCacheVersion" key for?

> Imagine that you released the first version of your application 3 years ago and a user downloaded the first version of the application to his device. After that, the user did not update this application for a long time, and after 3 years decided to open it.
> 
> Answer please, does it make sense to perform all point changes accumulated over three years ?
> 
> - No, because there could be thousands. Therefore, in this case it is easier to download the archive with the latest version of the cache and unzip it.
> 
> First of all, the RXCM algorithm compares the minimalCacheVersion web storage and the current version of the cache on the device. If the version on the device is lowwer, then the entire cache is deleted and the archive is downloaded.

#### When do I need to change the value of "minimalCacheVersion" ?

> Next, you will know that each change on the web storage you will need to describe in the configuration files (verbatim -"this file was here, and now he lie here"), if suddenly for one update you have made a very large number of changes and do not want to describe them in the configuration files (because it can be a very long time) - you can specify that minimalCacheVersion equals actualCacheVersion

```json
{
"actucalCacheVersion" : "1.5",
"minimalCacheVersion" : "1.5",
}
```

> Then any cache version on the device is lower than 1.5, will be completely deleted and the archive of the Cache Content folder with new content will be downloaded and unpacked on the device

| Key                                | Explanation                                                                                                                                                            |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `urlAtListOfURLsToUpdatesByVersion` | reference to json which contains references to models describing changes by versions.                                                                                     |
| `urlAtZipArchiveOfCacheContent`     | a link to the archive of folder RXCacheContent.                                                                                                                                |
| `zippedArchiveSizeInMB`             | the size of the Packed archive in megabytes. RXCM algorithm before downloading calculates how much space is required archive. If there is not enough space, RXCM returns an error. |
| `unzippedArchiveSizeInMB`           | the size of the unpacked archive in megabytes. The algorithm also calculates the free disk space of the device. If it is not enough, an error will be returned.  |

2. **listOfURLsToUpdatesByVersion.json**

![5cc74f9dbe1a1](/Documentation/ScreeSnippet/listOfURLsToUpdatesByVersion2.png)

| Key                 | Explanation                                                                          |
| ------------------- | ------------------------------------------------------------------------------------ |
| `updatesByVersions` | the array contains DIRECT links to the json model which describes the updates for the versions |

3. **changes_1.0_1.1.json**

![5cc74fbd6e388](/Documentation/ScreeSnippet/changes_1.0_1.1.png)

The file contains an array of changes and version numbers between which they were made. (The framework has no symmetric object).


| Key           | Explanation                                                                                        |
| ------------- | ------------------------------------------------------------------------------------------------- |
| `from` and `to` |version number of the cache between them were applied the changes described in the array `changes`         |
| `changes`     | an array that contains objects describing the changes that have been made between versions of the cache. |

Dictionary nested in the array `changes` has a symmetrical object - **RXCMOneChange : NSObject**

**RXCMOneChange** - this is a class that describes the changes that have been made over ONE file. 

For example, a previously created file was moved in web storage,  in order to move the procedure was also made on the device, you have to add a single dictionary (which will be described in change) in an array `changes` .

About how to configure the dictionary so that the RXCM algorithm understands that for example we want to delete/add a file (or perform other actions) will be described later.


## What are RXCMOneChange objects for ?

As was written above from the dictionaries contained in the array `changes` in files like `changes_1.0_1.1.json` objects of class **RXCMOneChange** will be created. Then an array of objects **RXCMOneChange** will be submitted for execution in the system method fraemwork.

The RXCM algorithm will analyze the state of the object variables and understand what kind of changes should be made.


## What types of changes does RXCM support

| Type of change | Description of change                                                                                                                                                                                                                                                                                                                                                                                        |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Adding     | Adds(i.e. downloads) a file or creates a folder. |
| Removing       | Deletes a file or folder |
| Renaming | Renames a file or folder |
| Replacing    | Moves a file or folder     |
| "Redownloading" | this type of action can be used when you need to update the content of a certain file. For example, on a web storage you have a text file, it was changed from the outside. And now you want the content of the text files on the device and on the server to be identical. <br/><br/> Then you need to choose this type of change. Then the old copy of this file will be deleted and the new one downloaded from the server. |

## The syntax of the configuration dictionary from which the RXCMOneChange object will be created

#### Adding

If you want to upload a file to the device or create a folder, leave the `oldPath` field blank., and in `newPath` specify the url to the element that is stored on the web storage.

```json
"oldPath" : "",
"newPath" : "https://.../RXCacheContent/folder1",
"redownload" : false,
"rename"     : false
}
```

#### Removing

If you want to delete (file or folder), specify the current location of the item in the `oldPath` field, and make the `newPath` field empty. Thus, the RXCM algorithm will understand that you want to delete an item.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1", 
"newPath" : "",
"redownload" : false,
"rename"     : false
}
```

#### Renaming

If you want to rename a file or folder, then in the field `oldPath` specify the current address where the element is located,and in the `newPath` field specify the same path but with a new element name.
Also set `true` in the rename field.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1",
"newPath" : "https://.../RXCacheContent/Myfolder1",
"redownload" : false,
"rename"     : true
}
```

#### Replacing

If you want to move a file or folder, then in the field `oldPath` specify the current address where the element is located, and in the `newPath` field, specify a new address.

```json
{
"oldPath" : "https://.../RXCacheContent/folder1",
"newPath" : "https://.../RXCacheContent/SecretFolder/folder1",
"redownload" : false,
"rename"     : false
}
```

#### Redownloading

If you want the file to be redownloaded (i.e. first deleted from device,and then a fresh copy is downloaded), enter an existing file path in the `oldPath`
 and `newPath`. 
And don't forget to set `true` in the `redownload`field.


⚠️ Attention. You can only reload the file. The FOLDER with the attached files cannot be reloaded.

🍬 You can also **reload** and **move** and **rename** at the same time if you set `true` in the `redownload` and `rename`fields.

```json
{
"oldPath" : "https://.../RXCacheContent/esse.txt",
"newPath" : "https://.../RXCacheContent/esse.txt",
"redownload" : true,
"rename"     : false
}
```

## How to add RXCM support on the server?

#### Step 1

Place the **RXCacheConfig**, **RXCacheContent** folders in the same directory.
For example, such a directory can be the root folder of your repository as I did. Example: https://github.com/m1a7/DemoCacheStorage

#### Step 2

Fill the **RXCacheContent**folder with custom content. And create a **RXCacheContent archive.zip** which will be located in the same directory.

#### Step 3

Create the file `cacheStatus.json` in the **RXCacheConfig** folder and paste this code into it.

(⚠️ Do not forget to insert your own values in the fields related to the size of the archive and your own path to the configuration files).

[Copy code](Documentation/TextSnippet/cacheStatus_Snippet.txt)![5cc84cc2388ee](/Documentation/ScreeSnippet/cacheStatusSnippet.png)

#### Step 4

Create the file `listOfURLsToUpdatesByVersion.json` in the **RXCacheConfig** folder and paste this code into it.

[Copy code](Documentation/TextSnippet/listOfURLsToUpdatesByVersion_EmptySnippet.txt)

![5cc84e986da8a](/Documentation/ScreeSnippet/listOfURLsToUpdatesByVersion_EmptySnippet.png)

#### Server to support RXCM is ready!

Everything had to turn out as shown in this diagram

![5cc7281c60e9e](/Documentation/ScreeSnippet/structureAndArrangementOfFilesInRootDirectory.png) 

## What to change in the configuration files after changing the contents of the cache ?

Every time after you make any changes to the RXCacheConent folder you have two paths:

#### Way 1.

1. Create a new file describing changes between versions (For example `changes_1.0_1.1.json`) and specify all changes in it (adding new files, deleting old ones, etc.)

2. Delete old archive `RXCacheContent.zip` and create a new.

3. Updated content in the `cacheStatus.json` 

```json
// Been
{
"actualCacheVersion"        : "1.0",
"minimalCacheVersion"        : "1.0",

"zippedArchiveSizeInMB"    : "0.27",
"unzippedArchiveSizeInMB" : "0.311"
}
```

```json
// Become
{
"actualCacheVersion"        : "1.1",
"minimalCacheVersion"        : "1.0",

"zippedArchiveSizeInMB"    : "20",
"unzippedArchiveSizeInMB" : "30"
}
```

#### When to use the approach described in "Way 1" ?

Use this approach when **a small number of changes have been made**. Then it will be easy for you to describe them in the configuration file  (`changes_1.0_1.1.json`).

Or when your cache on the device already has a large amount of content and you do not want to for the sake of one added file, the entire cache was deleted and downloaded again by the archive.

This situation can be if you write an application that can save in offline movie mode, let's say the device already stores different movies that take up `10GB` space, and you add two short clips. In this case, it is advisable to describe all the changes in the configuration file, rather than delete the cache and download a large archive.

#### Way 2.

1. Delete old archive `RXCacheContent.zip` and create a new one.

2. Updated content in `cacheStatus.json`(specifying that `minimalCacheVersion` equals  `actualCacheVersion`). 

```json
// Been
{
"actualCacheVersion"      : "1.0",
"minimalCacheVersion"     : "1.0",

"zippedArchiveSizeInMB"   : "0.27",
"unzippedArchiveSizeInMB" : "0.311"
}
```

```json
// Become
{
"actualCacheVersion"     : "1.1",
"minimalCacheVersion"    : "1.1",

"zippedArchiveSizeInMB"   : "20",
"unzippedArchiveSizeInMB" : "30"
}
```

#### When to use the approach described in "Way 2" ?

Use this approach when **a large number of small changes have been made** and the total cache weight is small (up to **50MB**). 
For example, you have created a lot of text files, changed the name of the old, something-removed, something modified. In this case, it is convenient to "increase" minimalCacheVersion, which does not describe all the changes in the configuration files.


## Connecting RXCM to a project

#### Step 1

Create folder **Frameworks** in your project folder and copy the file to it **RXCM.framework**.

![5cc878d7b5ac9](/Documentation/ScreeSnippet/addRXCM-1.png)

#### Step 2

Drag the folder with the nested framework into the project structure.

![5cc879443153e](/Documentation/ScreeSnippet/addRXCM-2.png)

#### Step 3

In the **General** tab, add **RXCM.framework** in section **Embedded Binaries** and **Linked Frameworks and Libraries**.

![5cc879915cdeb](/Documentation/ScreeSnippet/addRXCM-3.png)

## RXCM integration into the project

For the cache Manager to function successfully, you will need to create an object of the RXCacheManager class (or rxcm for short) and implement delegate methods (optional).

For this purpose, we recommend choosing the class **AppDelegate**, because the cache Manager object must exist throughout the life of the application + it is recommended that you call some Manager management methods (for example, pausing an update or resuming)
 in the standard **AppDelegate** methods when the application is closed and opened.

### Step 1

Import the framework into your class.

```objectivec
#import <RXCM/RXCM.h>
```

### Step 2

Create a property on the cache Manager object.

```objectivec
@property (nonatomic, strong) RXCacheManager* cache;
```

### Step 3

Before creating an object, select the delegate Protocol that is comfortable for you.

**RXCacheManager** can work in two modes: 
**1)** Download all files (system and user) independently.
**2)** Shift this responsibility on you and take the files you have already downloaded.

The framework offers a choice of two protocols:

**RXInternalNetworkCacheManagement**   -  when this Protocol is selected, the framework downloads all files by itself, but you have the ability to edit the parameters of the network request (for example, add a token to params). (*This Protocol is used in the vast majority of cases*).

**RXRemoteNetworkCacheManagement** - when you select this Protocol, you must download all files yourself, and provide the framework already downloaded files.This method of interaction can be useful if you already have a complex network layer written, and for example, during any requests to your server, the user must fill in a graphic captcha.


Also decide in which folder on the device you will store the user cache.                       
For this purpose, Apple recommends using the folder `Library/Caches`.

You can read more about Apple's recommendations [Here](https://developer.apple.com/library/archive/documentation/FileManagement/Conceptual/FileSystemProgrammingGuide/FileSystemOverview/FileSystemOverview.html#//apple_ref/doc/uid/TP40010672-CH2-SW1).

```objectivec
@interface AppDelegate () <RXInternalNetworkCacheManagement>
@end
```

```objectivec
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSString* basePrefixURLToCache  =         
    @"https://raw.githubusercontent.com/m1a7/DemoCacheStorage/master/";

    NSString* pathToLocalDirectory = [NSSearchPathForDirectoriesInDomains(NSCachesDirectory,         
                                                                          NSUserDomainMask,YES) 
                                                                          firstObject];


    self.cache = [RXCacheManager initWithBaseURLPathPrefixToCache: basePrefixURLToCache
                       andPathToLocalDirectoryWhereWillStoreCache: pathToLocalDirectory
                                      withInternalNetworkDelegate: self];

    return YES;
}
```

### Step 4

Implement delegate methods. 

**Implementation of  RXInternalNetworkCacheManagement methods**

[Open image in full size](/Documentation/ScreeSnippet/RXInternalNetworkCacheManagement.png)![5cc8951b024d6](/Documentation/ScreeSnippet/RXInternalNetworkCacheManagement.png)

(If you selected **RXInternalNetworkCacheManagement** you may not implement methods at all). 

In all methods of this delegate, you are given the opportunity to edit the network request parameters using the class **RXCMRequiredComponentsForRequest** object.
You can change these properties (`url`,`httpMethod`,`params`,`httpFileds`).

Regardless of whether modified **RXCMRequiredComponentsForRequest** or not, you must return it to `configureBlock`, as it is shown in the picture.

Example of implementation of one of the Protocol methods:

[Open image in full size](/Documentation/ScreeSnippet/rxcm_willCheckingForMoreActualCacheStatusVersion_Body.png)![5cc899949d596](/Documentation/ScreeSnippet/rxcm_willCheckingForMoreActualCacheStatusVersion_Body.png)

#### or

**Implementation of RXRemoteNetworkCacheManagement methods**

[Open image in full size](/Documentation/ScreeSnippet/RXRemoteNetworkCacheManagement.png)![5cc89ecc743f6](/Documentation/ScreeSnippet/RXRemoteNetworkCacheManagement.png)

Example of implementation of one of the Protocol methods:

[Copy code](Documentation/TextSnippet/rxcm_expectsActualCacheStatusFromURL.txt)![5cc8a1a14caf5](/Documentation/ScreeSnippet/rxcm_expectsActualCacheStatusFromURL.png)

### Step 5 (Optional)

Also you can implement helper methods of Protocol **RXCacheManagerHelperProtocol**.

These methods display the update process, notify you when the update is complete, and etc..

[Open image in full size](/Documentation/ScreeSnippet/RXCacheManagerHelperProtocol.png)![5cc8afa94410b](/Documentation/ScreeSnippet/RXCacheManagerHelperProtocol.png)

### Step 6

Decide in what state you'll start the update process, and in which to pause.
We recommend that you use these methods to **start* * and **pause**.

```objectivec
- (void)applicationWillResignActive:(UIApplication *)application {
    // Called when pressed on Home Button

    if (self.cache.enumCacheState == RXCM_WorkingState){
        [self.cache startSuspendResumeUpdateProcess];
    }
}
```

```objectivec
- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Called every time when app became active

    [self.cache startSuspendResumeUpdateProcess];
}
```

## Cache manager management methods

The cache Manager has an enum `RXCM_State` that describes the current state of the cache.
RXCM is a fully asynchronous framework.


```objectivec
typedef NS_ENUM(NSInteger, RXCM_State) {

RXCM_UnknowState = 0,        // State of CacheManager is unknown
RXCM_ReadyToStartState,      // CacheManager is ready to start
RXCM_WorkingState,           // CacheManager is working
RXCM_SuccessFinishedState,   // Update was performed successful
RXCM_FailureFinishedState,   // Update was completed with an error
RXCM_SusspendState           // CacheManager was temporarily suspended
};
```

```objectivec
@interface RXCacheManager : NSObject
...
@property (nonatomic, assign, readonly) RXCM_State enumCacheState;

@end
```

You can also stop and resume the cache at any time. With these methods.

| Method                                                   | Explanation                                                                                                                                                                                                                                          |
| ------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| - (RXCM_State) startSuspendResumeUpdateProcess;         | This method is analogous to the play button on the player. When you call it, it will change the cache state depending on the current state.<br/>For example, if the update process is currently suspended, it will be resumed. And vice versa. |
| - (void) checkForMoreActualCacheVersionAndUpdateIfNeed; | A method that is called to check for a new version and update the cache if required.                                                                                                                                                           |
| - (void) suspendCheckingAndUpdatingCacheProcess;        | Suspends the update process if it was started.      |
| - (void) resumeCheckingAndUpdatingCacheProcess;         | Resumes the update process if it has been Suspends. |
                                                                                                                                                                                            |
We recommend always using the method  `-startSuspendResumeUpdateProcess`, it will make your code easier, because you will not need to write extra logical constructs that track the status of the cache.



## Snippets and additional features

### 1. Adjustable type of Internet connection

Property `whatTypeOfConnectionCanDownloadCache` indicates the cache to the manager on which type of Internet connection the cache can download files.

by default, this property is equal to `RXCM_EveryTypeOfInternetConnection`.
But if you want to save the mobile data of your user, you can set the operation mode to `RXCM_WifiConnection`.

```objectivec
/* Enum describes the types of the Internet connection*/
typedef NS_OPTIONS(NSUInteger, RXCM_TypesOfInternetConnection) {
RXCM_NoneOfListed    = 1 << 0, // 0000 0001
RXCM_WifiConnection  = 1 << 1, // 0000 0010
RXCM_WWANConnection  = 1 << 2, // 0000 0100

RXCM_EveryTypeOfInternetConnection  = RXCM_WifiConnection | RXCM_WWANConnection
};
```

```objectivec
@interface RXCacheManager : NSObject
 
@property (nonatomic, assign) RXCM_TypesOfInternetConnection whatTypeOfConnectionCanDownloadCache;
@end
```

If you specified the connection type `RXCM_WifiConnection` as the only allowed one, then suddenly the user switched to` RXCM_WWANConnection`, then if you implemented protocol support **RXCacheManagerHelperProtocol** the method will be called

` -rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection` in which you have to ask the user if he wants to continue downloading on a new type of connection.


An example of the implementation of this method:
[Copy code](Documentation/TextSnippet/rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection.txt)![5cc8bbe38c361](/Documentation/ScreeSnippet/rxcm_isAllowToContinueDownloadingOnNewTypeOfInternetConnection.png)

An example of the implementation of the method that shows **UIAlertViewController**:

[Copy code](Documentation/TextSnippet/showAlertIsContinueWorkOnNewTypeOfConnection.txt)![5cc8bc98834ca](/Documentation/ScreeSnippet/showAlertIsContinueWorkOnNewTypeOfConnection.png)

### 2. Printing logs to console

Set the value of `YES` in the property` isPrintLogInConsole` to see the console logs.

### 3. Check for errors

If the cache update process was completed with an error, then you can get detailed information in the `errorOccuredAtExecutionUpdate`.

If you want to print an error to the console, then we recommend that you use the convertErrorToString method - which will beautifully draw the console log.

```objectivec
-(NSString*) convertErrorToString:(NSError*)error;
```

### 4. Initialize the downloaded files from the disk

For example, there may be such a situation when you are performing a network request, and the Internet has disappeared at this time. Then you can successfully retrieve the data you need from memory.

**Initializing NSString from a text file.**

[Copy code](Documentation/TextSnippet/InitNSStringFromSandbox.txt)![5cc97b7727e8d](/Documentation/ScreeSnippet/InitNSStringFromSandbox.png)

**Initialize NSDictionary from json file.**

[Copy code](Documentation/TextSnippet/initNSDictionaryFromSandbox.txt)![5cc97cafc2c8a](/Documentation/ScreeSnippet/initNSDictionaryFromSandbox.png)

**Initializing a UIImage from a .png file.**

[Copy code](Documentation/TextSnippet/initUIImageFromSandbox.txt)![5cc97e436f561](/Documentation/ScreeSnippet/initUIImageFromSandbox.png)

### 5. Deleting the configuration object from NSUserDefault

In this way, you can delete absolutely all configuration objects. This may be needed during testing.

```objectivec
[UD    removeObjectForKey:kActualCacheStatus];
[UD    removeObjectForKey:kTempCacheStatus];
[UD    removeObjectForKey:kTempArrayOfOneChange];
[UD    synchronize];
```

**Initializing the cacheStatus.json porotype and writing to the device’s memory.**

```objectivec
RXCMCacheStatus* status = [RXCMCacheStatus new];
status.actualCacheVersion  = @"1.0";
status.minimalCacheVersion = @"1.0";
[RXCacheManager saveObjecInUserDefault:status forKey:kActualCacheStatus];
```

## Author
👨🏼‍💻 [@m1a7](github.com/m1a7) <br>
👌🏻 thisismymail03@gmail.com

## Additionally

RXCacheManager is a private development with closed source code.

[🇷🇺 Russian Readme](Readme(RU).md). <br>
