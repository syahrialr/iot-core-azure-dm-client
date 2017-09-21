# Application Management Decisions

OEMs may have a number of UWP applications running on their devices and they may want to control which applications to install or update.

## Application First-Time Installation

### Part of the Image
  - At image creation time, the OEM should include the application in their image. [ToDo: link].

### Windows Update
- The OEM packages the application in an OCP, and uploads the OCP to Windows Update.
- The OCP is then configured to target the OEM's product.
- The device needs to be configured to scan for Windows updates (see `allowAutoUpdate` in [Windows Update Management](windows-update-management.md)).
- The device will do the daily scan and discover the new update, download it, and install it - and trigger a reboot.

### Azure DM
  - New applications can be installed on the device using Azure DM. This is achieved by:
    - OEM uploads the application packages (appx + dependencies) to Azure Storage.
    - OEM configures the device to download and install them. 
    For more details, see [Application Management](application-management.md).

### MDM
  - Using InTune, the OEM can use the [EnterpriseModernAppManagement](https://docs.microsoft.com/en-us/windows/client-management/mdm/enterprisemodernappmanagement-csp).

## Application Updates

### Windows Update

- This is identical to the corresponding First-Time Installation scenario above.

### Azure DM

- This is identical to the corresponding Azure DM scenario above.

### MDM
- This is identical to the corresponding MDM scenario above.

### The Store

#### Uploading All Applications on the Device

- The OEM can configure the device to regularly run a scan for store updates.
  - This is currently not exposed through Azure DM. The OS supports it through the [Policy/ApplicationManagement/AllowAppStoreAutoUpdate](https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-applicationmanagement#applicationmanagement-allowappstoreautoupdate) csp.
  - The default is that it is enabled.
- In a given scan, all found updates will be installed. There is no way to selectively update one application but not the other if both have updates.

#### Updating Azure DM Client Application Remotely

- The OEM can trigger the Azure DM Client Application self-update <i>remotely</i> by invoking `windows.startDmAppStoreUpdateAsync`.
- Note that this will update only a single application; namely, the Azure DM Client Application.

#### Updating a Subset of Applications on The Device

- There is no native way of doing that.
- However, technically, the OEM may use AppServices to communicate an update request comming through the Azure DM Client application to other applications on the system; and consequently triggering their own self update.
- Self-updates can be implemented using the [StoreContext Class](https://docs.microsoft.com/en-us/uwp/api/Windows.Services.Store.StoreContext).
    - APIs
        - GetAppAndOptionalStorePackageUpdatesAsync()
        - RequestDownloadAndInstallStorePackageUpdatesAsync()
- Because these are just UWP API calls, the application can trigger that through its own UI.

----

[Home Page](../README.md) | [Library Reference](library-reference.md)


----

To remotely manage the device, one of those applications will need to be an *Azure DM Client* application (i.e. using the Azure IoTDMClientLib). Its role is to connect to Azure, and recieve configurations and apply them on the device.
