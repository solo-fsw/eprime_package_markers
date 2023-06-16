# ![markers](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/a3594027-f20a-43e5-8995-39d6e4ed78dd) E-Prime Markers package
E-Prime package for sending markers through UsbParMarker or Eva device.

## Installation
These download instructions will make the package available per user. When different users need to use the package, they must download the package separately.
- Download the Markers package here.
- Unzip or extract the folder.
- Place the Markers folder in **C:\Users\%USERNAME%\Documents\My Experiments\3.0\Packages**. When the Packages folder does not exist, and you receive the message "Windows can't find C:\Users\%USERNAME%\Documents\My Experiments\3.0\Packages", navigate to C:\Users\%USERNAME%\Documents\My Experiments\3.0 and create a new folder called "Packages" here. Then, place the Markers folder in the Packages folder.

![PackageLocation2](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/f4db2d36-97e3-4e34-adf7-15651a47c0e2)

- Open E-Studio
- Go to Edit -> Experiment -> Packages and click Add. The Markers package should be listed under Available Packages. Click the Markers package and click Ok and again Ok to add the package to your experiment.
- The package can now be used by inserting a PackageCall E-Object into your experiment and selecting the desired Routine (see How to use below for further instructions)

![AddMarkerPackage5](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/200c26ab-16cc-4f27-958d-c3e40b1885c0)

## How to use
The Markers package is used to send markers to different Leiden Univ FSW marker devices ([UsbParMarker](https://researchwiki.solo.universiteitleiden.nl/xwiki/wiki/researchwiki.solo.universiteitleiden.nl/view/Hardware/Markers%20and%20Events/UsbParMarker/) and [Eva](https://researchwiki.solo.universiteitleiden.nl/xwiki/wiki/researchwiki.solo.universiteitleiden.nl/view/Hardware/Markers%20and%20Events/EVA/)). See [here](https://researchwiki.solo.universiteitleiden.nl/xwiki/wiki/researchwiki.solo.universiteitleiden.nl/view/Hardware/Markers%20and%20Events/) for more information on markers in general.

After properly downloading the package and adding the package to the experiment (as mentioned in Installation), the marker devices can be controlled by using PackageCall objects. Add a PackageCall object (found under E-Objects) to your experiment. Double-click on the PackageCall object to open its properties. Select Markers package from the Package drop-down menu and select the desired routine from the Routine drop-down menu. A description of the routine is now visible, including an explanation of all its parameters.

### ![openmarkerdevice](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/30215ef0-0ada-4140-aba5-96c71a26be15) Initializing the Marker Device
An **OpenMarkerDevice** 
routine should be added to the start of the experiment. This routine initializes the marker device and allows the user to set different settings for the device. This routine should be placed at the start of the experiment and must be used before a SendMarker routine can be used.

The OpenMarkerDevice routine uses the following parameters:
- **c:** The experiment context allows the user to add variables to the experiment. The routines in the PackageFile may optionally use the experiment context to retrieve the current values of attributes stored in the context. The context is typically the first parameter of every PackageFile routine. Leave at default c.
- **DeviceType:** Specify the Device Type: "UsbParMarker" or "Eva". The Device Type must be a String, so make sure to add the quotation marks.
- **CrashOnMarkerErrors:** Optional: set to True if the task needs to crash on the following marker errors: 
    - the marker is too short (< 10 ms)
    - the same marker value is sent twice 
    - something went wrong when sending the marker
    CrashOnMarkerErrors is set to True when undefined. Note that the task does not crash when connection with the marker device gets lost during the experiment! Always check your data to make sure markers are received correctly. When set to False and GenMarkerFile is set to True, marker errors will be saved in the Markers .tsv file. 
- **DummyMode:** Optional: set to True to use dummy mode and be able to run the task when no marker device is connected (set to False when undefined).
- **GenMarkerFile:** Optional: set to True to generate a .tsv file that contains information about the markers that were sent during the task and possibly the marker errors that occured (set to True when undefined).
- **Flash255:** Optional: set to True to send two pulses with value 255 at initialization (set to False when undefined).

![openmarkerdevice](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/320c8478-a174-4990-aaf1-05d037fdc11b)

### ![sendmarker](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/2a903d40-9b42-4655-ac9e-e4f8aef2b888) Sending a marker
The SendMarker routine sends a marker at the timepoint in the experiment where the object is placed. 

The SendMarker routine uses the following parameters:
- **c:** The experiment context allows the user to add variables to the experiment. The routines in the PackageFile may optionally use the experiment context to retrieve the current values of attributes stored in the context. The context is typically the first parameter of every PackageFile routine. Leave at default c.
- **MarkerValue:** The marker value should be an integer between 0 and 255.
- **ObjectDuration:** Optional: ObjectDuration in ms. This is the duration of the SendMarker object. This object is blocking, i.e. the task will pause for the duration of this object. This object does not have any visual component, thus, the Display is showing a previous object with a visual component. The ObjectDuration is not necessarily the same as the duration of the marker! Only when the ResetMarkerValueToZero is set to True, the object duration is the same as the marker duration. This setting is set to 0 (no duration) when undefined.
- **ResetMarkerValueToZero:** Optional: ResetMarkerValueToZero. This value can be set to True or False. When True, the marker value will automatically reset to 0 after the object duration. Note that the duration must be minimally 5 ms for this setting to take effect. This setting is set to False when undefined.

![sendmarker](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/f46c9fdf-d00b-40e2-8ef1-cfe40336e63a)

### ![sendmarkertaskevent](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/ad32afbe-e233-49b3-b9be-573db27bb30c) Sending a marker as task event
The SendMarkerTaskEvent routine sends one or two markers at certain events. Thus, sending the marker is set up as Task Event and with this routine a marker can be sent at the Onset and/or Offset of an object. A maximum of two task events can be set with this routine. Make sure the routine is placed before the strObject. See below an explanation of the parameters. For example, to send marker with value 1 at the onset of object "Stimulus" and reset to 0 at the offset of object "Stimulus", use the following parameters: c, "Stimulus", "OnsetTime", 1, 0, "OffsetTime", 0, 0.

Note that this routine cannot be used when one or more Task Events are already set-up for the strObject. In this case, the Task Events are executed, but the Marker as specified in the SendMarkerTaskEvent routine is *not* sent.

The SendMarkerTaskEvent routine uses the following parameters:
- **c:** The experiment context allows the user to add variables to the experiment. The routines in the PackageFile may optionally use the experiment context to retrieve the current values of attributes stored in the context. The context is typically the first parameter of every PackageFile routine. Leave at default c.
- **strObject:** The name of the object to which the marker task events will be added. This value must be a String. For example, "Stimulus".
- **MarkerTaskEvent1:** The task event where the first marker should be sent (e.g. "OnsetTime" or "OffsetTime"). This value must be a string (include quotation marks).
- **MarkerValue1:** The value of the first marker. The value must be an integer between 0 and 255.
- **MarkerTaskEventDelay1:** Optional: The task event delay of the first marker in ms. For example, when the task event is set to "OnsetTime" of the "Stimulus" object and the delay to 10, the marker will be sent 10 ms after Stimulus.OnsetTime. Default value is 0 (no delay). Note: use this setting with caution and make sure the object has a duration that is at least as long as this parameter.
- **MarkerTaskEvent2:** Optional: The task event where the second marker should be sent (e.g. "OnsetTime" or "OffsetTime"). This value must be a string (include quotation marks).
- **MarkerValue2:** Optional: The value of the second marker. The value must be an integer between 0 and 255.
- **MarkerTaskEventDelay2:** Optional: The task event delay of the second marker in ms. For example, when the task event is set to "OffsetTime" of the "Stimulus" object and the delay to 10, the marker will be sent 10 ms after Stimulus.OffsetTime. Default value is 0 (no delay). Note: use this setting with caution and when using the delay, make sure the marker is not overwritten by other markers sent after the object.

![sendmarkertaskevent](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/19061828-1372-438f-872b-db9f0f282383)

### ![closemarkerdevice](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/412c53bb-5fcf-431a-891e-6d3d985b77db) Closing the Marker Device
To properly close the device and save the Marker file (if applicable) it is necessary to add a **CloseMarkerDevice** at the end of the experiment. No parameter specification is required (leave c at default c).

![closemarkerdevice](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/d0afd8b7-2815-4729-a36d-4135742f9165)

### ![usbparmarkerleds](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/51c3f86d-1755-431e-a96a-2ea4f4130c9b) Changing the UsbParMarker Leds
For the UsbParMarker it is possible to switch the Leds off (and back on). For this, the **UsbParMarkerLeds** routine can be used. Note that this routine can only be used with the UsbParMarker, not with the Eva.

The UsbParMarkerLeds routine uses the following parameters:
- **c:** The experiment context allows the user to add variables to the experiment. The routines in the PackageFile may optionally use the experiment context to retrieve the current values of attributes stored in the context. The context is typically the first parameter of every PackageFile routine. Leave at default c.
- **Leds:** The leds on the UsbParMarker can be switched on (default) or off by specifying "On" or "Off". Or the leds can be tested by specifying "Test", then the leds will flash 2 times with 1000 ms interval. After the test, the leds are always turned on. Note: only works with UsbParMarker with hardware version 3 or higher.

![usbparmarkerleds](https://github.com/solo-fsw/eprime_package_markers/assets/56065641/34f0ed49-fdd7-40c1-b96e-ec95e141cdf5)

### Object Placement and Timing
It is important to note that the Markers routines do not have a visual component. Thus, during the execution of the Markers objects, what was already presented on the screen, will stay on the screen.

To obtain the most accurate timing of a marker (i.e. the marker is sent as closely near the actual event of interest as possible), it is advised to make use of the SendMarkerTaskEvent routine. With this routine, a marker can be sent at the on- or offset of an object.

### Using Multiple Marker Devices
It is not possible to use the Marker package with multiple marker devices. Only one marker device can be used with this package.

## Timing Test
Timing of the E-Prime markers package was tested by comparing the onset and offset of pulses sent with the markers package to the UsbParMarker, with the onset and offset of pulses sent to the LPT port. Both signals were recorded with BIOPAC AcqKnowledge (LPT with Digital input, UsbParMarker in Analog channel 1). 
A total of 90 pulses were sent to both devices. On average, a delay of 183.3 us was found when comparing both onsets and offsets, with a maximum delay of 550 us. 
Breaking it down, pulses were sent in three different ways, 1. first to the UsbParMarker, then to the LPT port, 2. first to the LPT port, then to the UsbParMarker, 3. both pulses were set as task events of a stimulus, and thus were set high at the onset of the stimulus, and low at the offset of the stimulus.
1.	When sending a pulse first to the UsbParMarker and then to the LPT port, an average difference of 301.7 us (max 550 us) was found when comparing the onset of the pulses and an average difference of 187.7 us (max 250 us) was found when comparing the offset of the pulses (tested with 30 pulses). 
2.	When sending a pulse first to the LPT port and then to the UsbParMarker, an average difference of 108.3 us (max 150 us) was found when comparing the onset of the pulses and an average difference of 203.3 us (max 350 us) was found when comparing the offset of the pulses (tested with 30 pulses).
3.	When sending pulses as task events, an average difference of 198.3 us (max 250 us) was found when comparing the onset of the pulses and an average difference of 101.7 us (max 150 us) was found when comparing the offset of the pulses (tested with 30 pulses).

The timing data can be found in the TimingTest folder.






