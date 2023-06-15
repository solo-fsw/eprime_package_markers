# Markers package sample tasks
The following sample tasks demonstrate how the Markers package is used:
- Markers_Sample_Task
- Markers_Flanker_Task

The following sample task demonstrates how two marker devices can be used (without making use of the Markers package):
- Multiple_Marker_Devices

## Markers Sample Task
The Markers_Sample_Task demonstrates sending markers with the Markers package. 

An OpenMarkerDevice object was added to the start of the experiment to open the marker device. In this sample task the following settings in OpenMarkerDevice are used:
- DeviceType = "UsbParMarker"
- CrashOnMarkerErrors = True
- DummyMode = False
- GenMarkerFile = True
- Flash 255 = False

A CloseMarkerDevice object was added to the end of the experiment. No settings are required in the CloseMarkerDevice object.

The task consists of three blocks that demonstrate different ways of sending markers. The trials within each block are all the same, except for the way the markers are sent. Each trial consists of a Fixation cross for 500 ms, a Stimulus that lasts until a key was pressed, a ShowResp object that shows which button was pressed and which marker was sent with a duration of 1000 ms. The checkExit inlines at the end of the trials check whether the q was pressed and the task needs to be terminated. 

Short explanation of the blocks:
- SendMarkerTaskEventProc demonstrates how to use the SendMarkerTaskEvent routine to send markers as Task Event. The SendMarkerStim object sets up the marker to be sent at the OnsetTime of the Stimulus object, and reset to 0 at the OffsetTime of the Stimulus object. The marker value is stored in the MarkerValue attribute in the TrialList.
- SendMarkerProc demonstrates how to use the SendMarker routine. The SendMarkerValue sends marker with value stored in the MarkerValue attribute in TrialList2. The marker is sent just before the Stimulus object. The SendMarker0 object resets the marker value to 0 after the Stimulus object.
- SendMarkerBlockingProc demonstartes how to use the SendMarker routine with a duration. The SendMarkerValueBlocking object sends a marker with a value that is stored in the FixMarkerValue attribute in TrialList3, then waits for 200 ms, and then resets the value to 0. The SendMarkerValueBlocking object is blocking, thus, during the execution of this object, the task just waits. Because the Marker objects do not have a visual component, the Fixation cross is shown during these 200 ms and the Fixation object itself has a duration of 0 ms. 

**Whenever possible, it is advised to use the SendMarkerTaskEvent method.** This method allows the marker to be sent as closely to the onset of a Stimulus as possible. When the SendMarker object is used in the same way as the SendMarkerProc, there will be a slight delay between the marker and the actual onset of the Stimulus. In this delay, the Stimulus item is prepared. How large this delay is, depends on the Generate PreRun setting of the Stimulus, and the content of the Stimulus object (e.g. when the stimulus is visually taxing, the preparation time will be longer).

## Markers Flanker Task
The Markers_Flanker_Task demonstrates how the markers package can used in a simple Flanker task and points out some considerations when sending markers.

In this task, participants are presented with the following:
- **Fixation**: A fixation cross for 200 ms
- **Stimulus**: A Stimulus consisting of 5 letters (HHHHH, HHSHH, SSSSS, SSHSS). The participant needs to respond to the letter in the middle as fast as possible, while ignoring the flanking letters. The Stimulus is presented for a maximum duration of 800 ms, or until the participant responds.
- **Response**: Feedback consisting of a green (correct) or red (incorrect or no response) fixation cross for a duration of 1500 ms.
- **ISI**: An inter stimulus interval that is just a blank screen for a duration of 500 ms.
 
A marker is sent during the Stimulus object. This marker conveys which Stimulus was presented (HHHHH, HHSHH, SSSSS, SSHSS). Another marker is sent during the Response object. This marker conveys whether the response was correct, incorrect, or no response was given.

The following marker related objects were added to the task:
- **SendMarkerStim**: The stimulus marker is set up here. At the OnsetTime of the Stimulus object a marker with a value that is stored in the StimMarker attribute is sent. The marker is not reset to 0 at the Offset of the Stimulus object, because the duration of the Stimulus object is set to the participant's response. In theory, it is possible that the participant responds within 1 ms of the Stimulus duration. When this happens, the marker will end almost immediately after Stimulus onset, and it is possible that the marker will not be picked up by the physiology equipment (BIOPAC, BioSemi). Thus, the marker is not reset to 0 at the Offset of the Stimulus.
- **Wait10ms**: A Wait object of 10 ms was added to make sure the marker has a duration of at least 10 ms. For example, in the unlikely but possible scenario where the participant's response on the Stimulus object is within 1 ms, the task will wait here for 10 ms, and then resets the marker value to 0 in the next object.
- **SendMarker0**: Resets the marker value to 0. This object has a duration of 10 ms. This is because a new marker will be sent right after this object. When the duration is 0, the marker value will change from the value sent during the Stimulus directly to the value sent during the Response. However, some analysis software only detect markers at the start of the marker, that is, when the marker signal changes from 0 to a non-zero value. It is thus important to reset the marker signal to 0 for at least 10 ms before sending the next marker.
- **SetRespMarker**: Based on the response on the Stimulus object the marker value that is sent during the Response object is determined and saved in the RespMarker attribute.
- **SendMarkerResp**: Sets-up the marker that is sent during the Response object. A marker with a value that is set in the RespMarker attribute is sent at the OnsetTime of the Response object and reset to 0 at the OffsetTime of the Response object. Here, resetting the marker value to 0 at the OffsetTime works, because the Response object has a fixed duration of 1500 ms. Also, there is no need to add a wait to make sure the marker signal is 0 for at least a few ms, because the Response object is followed by an object during which no marker is sent.

## Multiple Marker Devices
