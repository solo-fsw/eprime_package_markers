# Markers package sample tasks
The following sample tasks demonstrate how the Markers package is used:
- Markers_Sample_Task
- Markers_Flanker_Task

The following sample task demonstrates how two marker devices can be used (without making use of the Markers package):
- Multiple_Marker_Devices

## Markers Sample Task
The Markers_Sample_Task demonstrates sending markers with the Markers package. 

An **OpenMarkerDevice** object was added to the start of the experiment to open the marker device. In this sample task the following settings in OpenMarkerDevice are used:
- DeviceType = "UsbParMarker"
- CrashOnMarkerErrors = True
- DummyMode = False
- GenMarkerFile = True
- Flash 255 = False

A **CloseMarkerDevice** object was added to the end of the experiment. No settings are required in the CloseMarkerDevice object.

The task consists of three blocks that demonstrate different ways of sending markers. The trials within each block are all the same, except for the way markers are sent. 
Each trial consists of:
- **Fixation:** A fixation cross for 500 ms
- **Stimulus:** A stimulus that lasts until a key was pressed
- **ShowResp:** Feedback of which button was pressed and which marker is sent with a duration of 1000 ms
- **checkExit:** This InLine checks whether the q was pressed and the task needs to be terminated

Short explanation of the blocks:
- SendMarkerTaskEventProc demonstrates how to use the **SendMarkerTaskEvent** routine to send markers as Task Event. The SendMarkerTaskEventShowResp object sets up the marker to be sent at the OnsetTime of the ShowResp object, and reset to 0 at the OffsetTime of the ShowResp object. The marker value is stored in the MarkerValue attribute in the TrialList.
- SendMarkerProc demonstrates how to use the **SendMarker** routine. The SendMarker object sends a marker with value stored in the MarkerValue attribute in TrialList2. The marker is sent just before the ShowResp2 object. The SendMarker0 object resets the marker value to 0 after the ShowResp2 object.
- SendMarkerBlockingProc demonstrates how to use the SendMarker routine with a duration. The SendMarkerBlocking object sends a marker with a value that is stored in the MarkerValue attribute in TrialList3, then waits for 1000 ms, and then resets the value to 0. The SendMarkerBlocking object is blocking, thus, during the execution of this object, the task just waits. Because the Marker objects do not have a visual component, the ShowResp0ms is shown during these 1000 ms. The ShowResp0ms object itself has a duration of 0 ms, because it will stay on screen during the SendMarkerBlocking object. 

**Whenever possible, it is advised to use the SendMarkerTaskEvent method.** With this method, markers are sent as closely to the onset of an event as possible. When the SendMarker object is used in the same way as the SendMarkerProc, there will be a slight delay between the marker and the actual onset of the object of interest. In this delay, the object is prepared. How large this delay is, depends on the Generate PreRun setting of the object, and the content of the object (e.g. when a stimulus is visually taxing, the preparation time will be longer).

![SampleTaskStruct](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/b2d05232-2467-47f8-9165-b1f51193231a)

## Markers Flanker Task
The Markers_Flanker_Task demonstrates how the markers package can be used in a simple Flanker task and points out some considerations when sending markers.

In this task, participants are presented with the following:
- **Fixation**: A fixation cross for 200 ms
- **Stimulus**: A stimulus consisting of 5 letters (HHHHH, HHSHH, SSSSS, SSHSS). The participant needs to respond to the letter in the middle as fast as possible, while ignoring the flanking letters. The Stimulus is presented for a maximum duration of 800 ms, or until the participant responds.
- **Response**: Feedback consisting of a green (correct) or red (incorrect or no response) fixation cross for a duration of 1500 ms.
- **ISI**: An inter stimulus interval that is just a blank screen for a duration of 500 ms.
 
A marker is sent during the Stimulus object. This marker conveys which Stimulus was presented (HHHHH, HHSHH, SSSSS, SSHSS). Another marker is sent during the Response object. This marker conveys whether the response was correct, incorrect, or no response was given.

The following marker related objects were added to the task:
- **SendMarkerTaskEventStim**: The stimulus marker is set up here. At the OnsetTime of the Stimulus object a marker with a value that is stored in the StimMarker attribute is sent. The marker is not reset to 0 at the Offset of the Stimulus object, because the duration of the Stimulus object is set to the participant's response. In theory, it is possible that the participant responds within 1 ms of the Stimulus duration. When this happens, the marker will end almost immediately after Stimulus onset, and it is possible that the marker will not be picked up by the physiology equipment (BIOPAC, BioSemi). Therefore, the marker is not reset to 0 at the Offset of the Stimulus.
- **Wait10ms**: A Wait object of 10 ms was added to make sure the marker has a duration of at least 10 ms. For example, in the unlikely but possible scenario where the participant responds within 1 ms, the task will wait here for 10 ms, and then resets the marker value to 0 in the next object.
- **SendMarker0**: Resets the marker value to 0. This object has a duration of 10 ms. This is because a new marker will be sent right after this object. When the duration is 0, the marker value will change from the value sent during the Stimulus directly to the value sent during the Response. Some analysis software however only detect markers at the start of the marker, that is, when the marker signal changes from 0 to a non-zero value. It is thus important to reset the marker signal to 0 for at least 10 ms before sending the next marker.
- **SetRespMarker**: Based on the response on the Stimulus object the marker value that is sent during the Response object is determined and saved in the RespMarker attribute.
- **SendMarkerTaskEventResp**: Sets-up the marker that is sent during the Response object. A marker with a value that is set in the RespMarker attribute is sent at the OnsetTime of the Response object and resets to 0 at the OffsetTime of the Response object. Here, resetting the marker value to 0 at the OffsetTime works, because the Response object has a fixed duration of 1500 ms. Also, there is no need to add a wait to make sure the marker signal is 0 for at least 10 ms, because the Response object is followed by an object during which no marker is sent.

![FlankTaskStructure](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/c80e2302-7e16-48f7-9349-c81d51d39e74)

## Multiple Marker Devices
The Multiple_Marker_Devices task demonstrates how to control marker devices without using the Markers package. This may be necessary when multiple marker devices need to be controlled. Currently, the Marker package can only be used with one marker device.

Note that when possible, it is advised to use the Markers package. This is because:
- When using the Markers package, it is not necessary to know to which port the marker device is connected. When not using the package, the port must be known and thus the marker device should not be switched to a different port.
- When using the Markers package, extra metadata concerning the marker device that was used is saved in the Edat file.
- The Markers package allows the user to save a marker file, in which the markers and a summary of the markers is given.
- The Markers package checks the markers and either crashes when a marker error occurs, or saves the error message in the Marker file.
All benefits mentioned above are not available when controlling one or more marker devices with the method described here, which is a very simple and crude method to control a marker device.

### How to create a task with multiple marker devices

#### Add serial devices:
When the Markers package is not used, the marker devices need to be added as devices to the E-Prime task. This is done by going to **Edit** --> **Experiment** --> **Devices**. In the Multiple_Marker_Devices task, two Serial devices called UsbParMarker1 and UsbParMarker2 are visible. These devices were added by clicking on **Add** and then **Serial**. UsbParMarker and Eva are both Serial devices. Note that in the current example UsbParMarker devices were used, but the same applies for Eva. Double-clicking on a serial device opens its settings. Here, a **Name** for the device can be chosen. The **COM Port** should be specified, which is the port to which the marker device is connected to. This port can be found by going to the **Device Manager** (see [below](#finding-the-com-port)). The rest of the settings should be: **Bits per second = 115200**, **Data Bits = 8**, **Partiy = None** and **Stop Bits = 1**. These settings are the same for all UsbParMarker and Eva devices. When using two marker devices, make sure both devices have the correct settings and the correct port number.

<img src="https://github.com/solo-fsw/eprime_package_markers/assets/56065641/435a0106-80f5-4b8a-afb1-1683d733f6b3/AddSerialDevices.png"/>

##### Finding the COM port
The COM port numbers can be found by going to the Device Manager. Search for the Device Manager app in the Windows search bar.
<img src="https://github.com/solo-fsw/eprime_package_markers/assets/56065641/4ec0364c-9954-4828-b92c-741b6a4f9e29/FindDeviceManager.png" width="500"/>

In the Device Manager, the UsbParMarker or Eva devices are listed under Ports (COM & LPT) and are called Arduino Leonardo or USB Serial Device.
<img src="https://github.com/solo-fsw/eprime_package_markers/assets/56065641/3e05b221-cb49-46cc-9e77-8a43fb65a883/DeviceManagerComPorts.png" width="500"/>

#### Send marker in InLine:
The InitMarkerDevices InLine provides an example on how to send markers to the different marker devices. This is done with the WriteByte method. In the Multiple_Marker_Devices sample task marker value 255 (all bits high) is sent to both devices for 100 ms and then reset to 0.

![SendMarkerMultipleDevices](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/0af9507f-9f53-45f6-b5ef-64ff91da3a09)

#### Send marker as Task Event:
Another method to send a marker is through Task Events. A Task Event allows the execution of a certain Task (sending a marker) at a certain event (the Onset of a Stimulus). Thus, with Task Events, a marker value can be sent at the Onset of an object and reset to 0 at the Offset of an object. The Task Event is set-up by going to an Object, clicking on the **Properties**, and going to the **Task Event** tab. Task Events can be added by clicking the **Add** button. Usually, only the OnsetTime and OffsetTime of an object are relevant when sending markers. When a Task Event is added, click on the ... next to **Name (Choose a Task)**. Here, choose the device to which a marker needs to be sent. Under **Action**, select **WriteByte**. Some Parameters appear at the rights side. Keep **Source** at **(custom)**. Fill in the marker value in **Custom** (or refer to an attribute where the marker values are stored). Select **Integer** at **Data Type**. To use the Task Event, keep **Enabled** to **Yes**. Usually a marker value is sent at the Onset and reset to 0 at the Offset. Alternatively, if for example only a marker pulse of 10 ms needs to be sent, two Task Events referencing the OnsetTime can be created. One of the Task Events sends the marker value at the OnsetTime. The other Task Event sends value 0 at 10 ms after the OnsetTime. This is achieved by filling in a **Delay** (in ms) of **10**.

![AddTaskEvents](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/6dcce584-2454-4805-a698-92e9060951bf)
