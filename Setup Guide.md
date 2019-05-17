# Setup Guide

These are the steps you'll need to prepare the hands-on-lab for use. This applies whether you're preparing it for yourself, or for others to follow along.

# Azure Resources

Set up an Azure Subscription and the following  Azure resources. Note that it's better to use paid accounts not free acounts if possible, as free tiers have many limits which may create constraints that get in the way. This can be done days prior to the lab.

1. A valid Azure subscription. [Create an account](https://azure.microsoft.com/free/) for free.
1. [Azure Custom Vision Service](https://www.customvision.ai/)
1. [Azure Container Registry](https://docs.microsoft.com/en-us/azure/container-registry/)
1. [Azure IoT Hub](https://docs.microsoft.com/en-us/azure/iot-hub/), with a single device set up for Azure IoT Edge
1. [Azure Time Series Insights](https://docs.microsoft.com/en-us/azure/time-series-insights/), tied to the same Azure IoT Hub.

## Azure Custom Vision Service

Refer to this guide: [How to build a classifier with Custom Vision](https://docs.microsoft.com/en-us/azure/cognitive-services/Custom-Vision-Service/getting-started-build-a-classifier).

1. Sign into the Azure Portal
1. Create a new "Custom Vision" resource.
1. Sign into the [Custom Vision Portal](https://www.customvision.ai/) with the same account as your azure subscription
1. From the profile menu (upper-right) choose the "directory" associated with your azure subscription.

Your custom vision portal is all set!

## Azure Container Registry

Refer to this guide: [Quickstart: Create a private container registry using the Azure portal](https://docs.microsoft.com/en-us/azure/container-registry/container-registry-get-started-portal)

1. Sign into the Azure Portal
1. Create a new "Container Registry" resource
1. Once created, switch to the "Access Keys" pane.
1. Enable the "Admin User"
1. Make note of the Login Server, username, and password. You'll need these later.

## Azure IoT Hub

1. Sign into the Azure Portal
1. Create a new "IoT Hub" resource
1. Once created, switch to the "Shared access policies" pane, select the "iothubowner" policy.
1. Select the "Owner" role. 
1. Make note of the connection string for the iothubowner role. You'll need this later.
1. Now, we will create a device. Switch to the "IoT Edge" pane.
1. Choose "Add a new device"
1. Make a note of the name you chose for this device. You'll need this later.
1. Once created, select the device from the list.
1. Make note of the connection string for this device. You'll need this later.

## Azure Time Series Insights

Refer to this guide: [Add an IoT hub event source to your Time Series Insights environment](https://docs.microsoft.com/en-us/azure/time-series-insights/time-series-insights-how-to-add-an-event-source-iothub)

1. Sign into the Azure Portal
1. Create a new "Time Series Insights" resource.
1. Choose the "S1" pricing tier.
1. Choose "Next: Event Source"
1. For the event source, choose the existing Azure IoT Hub you configured above. For IoT Hub access policy, choose "iothubowner". For "consumer group", enter a unique name to use as the consumer group for events.

WARNING: The S1 pricing tier is $150/month. I recommend removing the time series insights from your account once you've run through the lab. The PAYG (pay-as-you-go) tier does not produce equivalent results in the Time Series Insights hub.

# Development PC

To set up our Development Machine, we will need the following. This can be done days before the lab.

1. A PC running Windows 10 version 1809.
1. The [Windows SDK version 1809](https://developer.microsoft.com/en-us/windows/downloads/windows-10-sdk) (10.17763.0).
1. [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download)
1. [Visual Studio Code](https://code.visualstudio.com/)
1. Azure IoT Hub Toolkit for Visual Studio Code
1. [Git for Windows](https://git-scm.com/download/win)
1. [Windows 10 IoT Core Dashboard](https://docs.microsoft.com/en-us/windows/iot-core/connect-your-device/iotdashboard)

## Windows SDK

1. Download and install the 1809 version of the Windows SDK from the [Windows SDK Archive](https://developer.microsoft.com/en-us/windows/downloads/sdk-archive).
1. Determine the location of the Windows.winmd file. This is typically "C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.17763.0\Windows.winmd".
1. Set the "WINDOWS_WINMD" environment variable to this location.

```
PS C:\> $env:WINDOWS_WINMD = "C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.17763.0\Windows.winmd"
PS C:\> setx WINDOWS_WINMD "C:\Program Files (x86)\Windows Kits\10\UnionMetadata\10.0.17763.0\Windows.winmd"

SUCCESS: Specified value was saved.
```

## .NET Core SDK

1. Download and install the [.NET Core 2.2 SDK](https://dotnet.microsoft.com/download).

## Visual Studio Code

1. Download and install [Visual Studio Code](https://code.visualstudio.com/)
1. Download and install the [Azure IoT Hub Toolkit](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) for Visual Studio Code
1. Connect VS Code to your IoT Hub as follows…
1. Return to the Explorer tab in VS Code
1. Hover over the "Azure IoT Hub Devices" pane.
1. Click the "…"
1. Choose "Set IoT Hub Connection String"
1. Enter the connection string for the IoT Hub Owner, from the steps above
1. You'll see the device show up which you created in the previous steps

## Author a deployment.json file

Amongst the lab files, you will find a deployment json file named deployment.win-x64.json. Open this file in VS Code. We must fill in the details for the container image built in the lab, along with our container registry credentials.

Search for "{ACR_\*}" and replace those values with the correct values for your container repository.
The ACR_IMAGE must exactly match what you pushed, e.g. aiedgelabcr.azurecr.io/customvision:1.0-x64-iotcore

Save this as  ```C:\Users\Admin\Desktop\WindowsIoT\deployment.json``` on the lab PC.

```
    "$edgeAgent": {
      "properties.desired": {
        "runtime": {
          "settings": {
            "registryCredentials": {
              "{ACR_NAME}": {
                "username": "{ACR_USER}",
                "password": "{ACR_PASSWORD}",
                "address": "{ACR_NAME}.azurecr.io"
              }
            }
          }
        }
...
        "modules": {
            "squeezenet": {
            "settings": {
              "image": "{ACR_IMAGE}",
              "createOptions": "{\"HostConfig\":{\"Devices\":[{\"CgroupPermissions\":\"\",\"PathInContainer\":\"\",\"PathOnHost\":\"class/E5323777-F976-4f5b-9B55-B94699C46E44\"},{\"CgroupPermissions\":\"\",\"PathInContainer\":\"\",\"PathOnHost\":\"class/5B45201D-F2F2-4F3B-85BB-30FF1F953599\"}],\"Isolation\":\"Process\"}}"
            }
          }
```

# IoT Core machine

In this lab, we are using an UP Squared board running IoT Core using CPU evaluation. With just a little change, this can also be done on an AMD V1000 running IoT Core with GPU evaluation, or soon on a Raspberry Pi. With a bit more change, it can also be run on a Windows PC running Windows 10 IoT Enterprise.

To recap, for this lab we are using:

1. [AAEON UP Squared](https://docs.microsoft.com/en-us/windows/iot-core/tutorials/quickstarter/prototypeboards) board
1. Running [Windows 10 IoT Core LTSC 2019](https://developer.microsoft.com/en-us/windows/iot)
1. With a USB camera. See details below.
1. [Azure IoT Edge 1.0.6](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-windows) or higher. See details below.
1. Connected via Ethernet to a network switch on the same subnet as the Development PC. This is required for using the IoT Core Dashboard.

## Choosing a USB Camera

This lab requires a USB Camera. Everything here is set up for a camera from the LifeCam family, such as a LifeCam HD-3000. You can use any USB camera which Windows recognizes. However, you will need to change the lab instructions and the Dockerfile to specify the device you are using. 

To find the names of attached cameras, you can run the lab code itself with the ```--list``` parameter.

```
PS C:\WindowsAiEdgeLabCV> dotnet run --list
Found 1 Cameras
Microsoftr LifeCam HD-6000 for Notebooks
```

## Azure IoT Edge

1. Install Azure IoT Edge. Follow this guide: [Install the Azure IoT Edge runtime on Windows](https://docs.microsoft.com/en-us/azure/iot-edge/how-to-install-iot-edge-windows)
1. After installing Azure IoT Edge, deploy the [Simulated Temperature Sensor](https://docs.microsoft.com/en-us/azure/iot-edge/quickstart). 
1. In VS Code, open the "Azure IoT Hub Devices" pane. 
1. Look for the Edge Device Name there. 
1. Expand "Modules". Notice three modules there, all green and connected.
1. Right-click on that device, then select "Start monitoring D2C message".
1. Look for simulated temperature sensor results in the output window.

## End-to-end setup verification

It's wise to check that the simulated temperature sensor data is flowing through to Time Series Insights.

1. Open the [Time Series Insights explorer](https://insights.timeseries.azure.com/) in a browser tab.
1. Choose the environment name you chose when creating the Time Series Insights resource in the portal.
1. Set "Quick Times" to "Last 30 minutes"
1. Click the "Auto On/Off" button until it reads "Auto On"
1. Press the search icon to update the data set
1. Set the Interval Size to 4 seconds (lowest possible)
1. In the "Events" section of the left panel, set "Measure" to "Count" of "Events", and "Split by" to "(None)"
1. Press the "Refresh" button to refresh data

This will show how many events are coming into Time Series Insights from the hub. This number should stay relatively consistent over time as more data comes in.

## Lab Setup

This can be done days before the lab.

1. Affix a sticker to the bottom of the machine with the name of the machine.
2. Pull the iotcore container onto the IoT Core machine. This is not a required step, however it will greatly speed up the lab if the iotcore container is already on the device.

```
[192.168.1.116]: PS C:\data\modules\customvision> docker pull mcr.microsoft.com/windows/iotcore:1809
1809: Pulling from windows/iotcore
Digest: sha256:d427e051efdef9bfe9600fc00bb877d33e422d2d27f1f204ebc36b22d6dc3a9a
Status: Image is up to date for mcr.microsoft.com/windows/iotcore:1809
```

# Physical environment

1. Create a consistent environment for recognition. Use a single-color background. Place the objects in the same place, and the camera in the same place.
2. Select four or five physical objects you'll use for the classification.
3. Obtain a small desk lamp to illuminate them. Having consistent lighting helps the classifier.

# credentials.txt

Create a file contianing the service and device credentials for each lab user. It should contain the following:

Item | Value
--- | ---
Azure Subscription Username	| 
Azure Subscription Password	| 
Container Registry Login Server	|
Container Registry Username	|
Container Registry Password	|
IoT Hub Name	|
IoT Hub Owner Connection String |
IoT Edge Device Name |	
IoT Edge Device Connection String |
Device Name |
Device IP Address |
Device Administrator Password |

# Prepare Lab PC

Once all the software is installed, the actual lab environment can be prepared.

1. Connected via Ethernet to a network switch on the same subnet as the Target Machine.
1. The lab instructions expect the user logged in as Admin. Adjust as needed if logged in as another user.
1. Create a directory on the desktop entitled "WindowsIoT"
1. Place the README.md in that directory
1. Place the credentials.txt in that directory
1. Place a web shortcut to [Custom Vision Portal](https://www.customvision.ai/) on the desktop
1. Place a web shortcut to [Time Series Insights explorer](https://insights.timeseries.azure.com/) on the desktop
1. Place an app shortcut to VS Code on the desktop
1. Git pull the lab files down to C:\Users\Admin\Desktop\WindowsIoT

# Day of the Lab

Just before the lab begins, do the following:

1. Turn on the IoT Core device
1. Plug the camera into the development PC
1. Log into the development PC. The lab instructions expect the user logged in as Admin. Adjust as needed if logged in as another user.
7. Open a powershell window to C:\Users\Admin\Desktop\WindowsIoT\WindowsAiEdgeLabCV
8. Open the IoT Core Dashboard
8. Using the dashboard, open a powershell window and connect to the IoT Core device
8. Check 'iotedge list' to ensure all modules are up including simulated temp sensor
8. Docker login to the container registry using those credentials
8. Set the $Container variable to match their Container Registry Login Server
9. Map the Q: drive on the development PC to the IoT Core device
2. Open a browser
3. Open the [Custom Vision Portal](https://www.customvision.ai/)
3. Log in with your Azure Subscription. 
3. Select the Directory associated with your Azure custom vision resource. 
3. Open the [Time Series Insights explorer](https://insights.timeseries.azure.com/) in another browser tab
4. Log into Time Series Insights
5. Select the Environment associated with your Time Series Insights resource
4. Verify that simulated temperature readings are coming through in Time Series Insights.
2. Open VS Code
3. Open the C:\WindowsAiEdgeLabCV folder in VS Code
4. Check that the Azure IoT Hub pan in VS Code sees the hub and the device
5. Start monitoring D2C messages and ensure that simulated temperature messages are going up
6. Stop monitorig D2C messages

# After the Lab

In order to reset the lab back to default state:

1. Development PC doesn't need anything. Lab user doesn't make any significant changes to the PC.
2. IoT Core machine doesn't really need anything. You could delete the c:\data\modules\customvision folder, but it's not needed.
3. Deploy simulated temperature workload to the iot core device. Note that you don't need the lab's development PC. You can do that from any PC.
4. Verify that simulated temperature readings are coming through in Time Series Insights.
4. Turn off IoT Core machine. You don't want to pay for endless temperature messages
3. In Custom Vision Portal, delete the project.
4. In Container Registry, delete the "customvision" "repository"
