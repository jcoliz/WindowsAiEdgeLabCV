# Setup Guide

If you are setting up this hands-on-lab for others to run through, this guide is for you!

# Set up Azure Resources

This can be done days before the lab.

1. Set up an Azure Subscription and all the Azure resources as described in the main README. Note that it's better to use paid accounts not free acounts, as free tiers have many limits which may create constraints that get in the way.
2. Record all the configuration information described in the "Ready to Go" section.

# Set up Development PC

This can be done days before the lab.

1. Set up the Development PC as described in the main README.
3. Create a directory on the desktop entitled "Windows IoT Edge Lab"
2. Place a READ ONLY document in that directory containing the README instructions, with the setup bits removed, and with the configuration information added in.
3. Place a web shortcut to [Custom Vision Portal](https://www.customvision.ai/) 
3. Place a web shortcut to[Time Series Insights explorer](https://insights.timeseries.azure.com/) in another browser tab, also logged in
4. Place an app shortcut to VS Code
5. Git pull the lab files down to C:\WindowsAiEdgeLabCV
6. Create a deployment.json file containing the correct credentials for this account

# Set up IoT Core machine

This can be done days before the lab.

1. Set up the IoT Core machine as described in the main README.
4. Verify that simulated temperature readings are coming through in Time Series Insights.
2. Pull the iotcore container onto the IoT Core machine

```
[192.168.1.116]: PS C:\data\modules\customvision> docker pull mcr.microsoft.com/windows/iotcore:1809
1809: Pulling from windows/iotcore
Digest: sha256:d427e051efdef9bfe9600fc00bb877d33e422d2d27f1f204ebc36b22d6dc3a9a
Status: Image is up to date for mcr.microsoft.com/windows/iotcore:1809
```

# Day of the Lab

Just before the lab begins, do the following:

1. Turn on the IoT Core device
1. Plug the camera into the development PC
1. Log into the development PC
7. Open a powershell window to C:\WindowsAiEdgeLabCV
8. Open a powershell window and connect to the IoT Core device
8. Check 'iotedge list' to ensure all modules are up including simulated temp sensor
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
