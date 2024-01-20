
### Transaction logs and backups of registry hives
Transaction logs store the logs of changes of the registry hives, and will be in `C:\Windows\System32\Config\NAME_OF_HIVE.log` 

Backups of registry hives are copied every 10 days to `C:\Windows\System32\Config\RegBack`.

### SetupAPI logs for USB devices
when a new USB device gets inserted, the setup information will be placed in :
```
C:\Windows\inf\setupapi.dev.log
```
It contains information about the type of device, and the first and last connection time.

### Recovering deleted files
For this action, a full disk copy is required.
When a file is deleted, it doesn't really get "deleted". The bit-for-bit data is still there on the SSD, but the allocated space for the file gets marked as deallocated. So, as long as nothing writes in the newly deallocated space, the file can still be recovered.
The best tool to use for this is Autopsy.  After loading the drive copy into Autopsy, go to `Deleted files --> right-click --> "recover files"` to recover deleted files.

### The NTFS file system
##### Volume Shadow Copy
The NTFS filesystem keeps track of changes made to the filesystem in a file, if enabled. It's a type of system restore point.
To view or enable the status of a restore point, enter `create a restore point` in the search bar, it will open a `System properties` bar.
In the opened window, the status of restore points is mentioned for each available disk.

##### Alternate data streams
Alternate data streams (ADS), is a NTFS system feature, allowing for multiple "data streams" to be placed in one file.
More information, and methods on extracting and finding ADS are in [Streams](Sysinternals#Streams).

##### $MFT / MFTExplorer.exe
The `$MFT` is the `Master File Table` file. It's a hidden file placed in the root of the filesystem, It's also the first record of the Volume in any `NTFS`-type drive.

### Evidence of execution



