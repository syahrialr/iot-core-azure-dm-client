# Application Management Decisions

OEMs may have a number of UWP applications running on their devices and they may want to control which applications to install or update.

The applications running on the device can be:

- A foreground application.
- 0 or more background applications.

Also, to remotely manage the device, one of those application will need to be *Azure DM Client* application. Its role is to connect to Azure, and recieve configurations and apply them on the device.

## Application First-Time Installation

### Part of the Image
  - At image creation time, the OEM can include the application in their image. [ToDo: link].

### Through Windows Update
- The OEM packages the application in an OCP, and uploads the OCP to Windows Update.
- The OCP is then configured to target the OEM's product.
- The device needs to be configured to scan for Windows updates (see `allowAutoUpdate` in [Windows Update Management](windows-update-management.md)).
- The device will do the daily scan and discover the new update, download it, and install it - and trigger a reboot.

### Azure DM
  - New applications can be installed on the device using Azure DM. This is achieved by storing the application packages (appx + dependencies) on Azure Storage, and configure the device to download and install them.

## Application Updates

### Through Windows Update

- This is identical to the corresponding First-Time Installation scenario above.

### Through Azure DM

- The OEM uploads the application packages and its dependencies to Azure Storage.
- The OEM configures the device through Azure DM to download and install the application (see [Application Management](application-management.md)).

### Through the Store

#### All Applications on the Device

- The OEM can configure the device to regularly run a scan for store updates.
  - This is currently not exposed through Azure DM. The OS supports it through the [the Policy/ApplicationManagement/AllowAppStoreAutoUpdate csp](https://docs.microsoft.com/en-us/windows/client-management/mdm/policy-csp-applicationmanagement#applicationmanagement-allowappstoreautoupdate).
  - The default is that it is enabled.
- In a given scan, all found updates will be installed. There is no way to selectively update one application but not the other if both have updates.

#### Azure DM Client Application

- The OEM can trigger the Azure DM Client Application self-update by invoking `windows.startDmAppStoreUpdateAsync`.
- Note that this will update only a single application; namely, the Azure DM Client Application.

#### Subset of Applications on the Device

- There is no native way of doing that.
- However, technically, the OEM may use AppServices to communicate an update request comming through the Azure DM Client application to other applications on the system; and consequently triggering their own self update.
- Self-updates can be implemented using the [StoreContext Class](https://docs.microsoft.com/en-us/uwp/api/Windows.Services.Store.StoreContext).
  - APIs
    - GetAppAndOptionalStorePackageUpdatesAsync()
    - RequestDownloadAndInstallStorePackageUpdatesAsync()
- Because these are just UWP API calls, the application can trigger that through its own UI.

----

[Home Page](../README.md) | [Library Reference](library-reference.md)