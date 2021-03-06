# <bindingName> Binding

This binding allows you to use IP cameras in Openhab2 directly so long as they either have ONVIF or the ability to fetch a snapshot via a http link. Some brands of camera have much better support and will have motion, audio, and other alarms working that can be used to trigger Openhab rules and do much more cool stuff so choose your camera wisely by looking at what the APIs allow you to do. Keep a copy of the API documents as they have a habit of disappearing off manufacturers web sites.

In Alphabetical order:

AMCREST

https://s3.amazonaws.com/amcrest-files/Amcrest+HTTP+API+3.2017.pdf

DAHUA

ftp://ftp.wintel.fi/drivers/dahua/SDK-HTTP_ohjelmointi/DAHUA_IPC_HTTP_API_V1.00x.pdf

FOSCAM

https://www.foscam.es/descarga/Foscam-IPCamera-CGI-User-Guide-AllPlatforms-2015.11.06.pdf

HIKVISION

oversea-download.hikvision.com/uploadfile/Leaflet/ISAPI/HIKVISION%20ISAPI_2.0-IPMD%20Service.pdf

INSTAR

https://wikiold.instar.de/index.php/List_of_CGI_commands_(HD)



## Supported Things

If doing manual text configuration and/or when needing to setup HABPANEL or your sitemap you are going to need to know what your camera has as a "thing type". These are listed in BOLD below and are only a single word. Example: The thing type for a generic onvif camera is "ONVIF". 

HTTPONLY: For any camera that is not ONVIF compatible, and has the ability to fetch a snapshot with a url.

ONVIF: Use for all ONVIF Cameras from any brand that do not have an API.

AMCREST: Use for all current Amcrest Cameras as they support an API as well as ONVIF.

AXIS: Use for all current Axis Cameras as they support ONVIF.

DAHUA: Use for all current Dahua Cameras as they support an API as well as ONVIF.

FOSCAM: Use for all current FOSCAM Cameras as they support an API as well as ONVIF.

HIKVISION: Use for all current HIKVISION Cameras as they support an API as well as ONVIF.

INSTAR: Use for all current INSTAR Cameras as they support an API as well as ONVIF.


## Discovery

Auto discovery is not supported currently and I would love a PR if someone has experience finding cameras on a network. ONVIF documents a way to use UDP multicast to find cameras. Currently you need to manually add the IP camera either via PaperUI or textual configuration which is covered below in more detail. Once the camera is added you then need to supply the IP address and port settings you wish to use. Optionally a username and password can also be filled in if the camera is secured with these which I highly recommend. Clicking on the pencil icon in PaperUI is how you reach these parameters and how you make all the settings unless you have chosen to use manual text configuration. You can not mix manual and PaperUI methods, but it is handy to see and read the descriptions of all the controls in PaperUI.

## Binding Configuration

The binding can be configured with PaperUI by clicking on the pencil icon of any of the cameras that you have manually added via the PaperUI inbox by pressing on the PLUS (+) icon. 

It can also be manually configured with text files by doing the following. DO NOT try and change a setting using PaperUI after using textual configuration as the two will conflict as the text file locks the settings preventing them from changing. Because the binding is changing so much at the moment I would recommend you use textual configuration, as each time Openhab restarts it removes and adds the camera so you automatically gain any extra channels that I add. If using PaperUI, each time I add a new channel you need to remove and re-add the camera which then gives it a new UID number (Unique ID number), which in turn can break your sitemap and HABPanel setups. Textual configuration has its advantages and locks the camera to use a simple UID.

The parameters that can be used in textual configuration are:

IPADDRESS

PORT

ONVIF_PORT

USERNAME

PASSWORD

ONVIF_MEDIA_PROFILE

POLL_CAMERA_MS

SNAPSHOT_URL_OVERIDE

IMAGE_UPDATE_EVENTS



Create a file called 'ipcamera.things' and save it to your things folder. Inside this file enter this in plain text and modify it to your needs...


```

Thing ipcamera:ONVIF:001 [ IPADDRESS="192.168.1.21", PASSWORD="suitcase123456", USERNAME="Admin", ONVIF_PORT=80, PORT=80]

```

A second example, this time for a camera that does not have API or ONVIF support.


```

Thing ipcamera:HTTPONLY:002 [ IPADDRESS="192.168.1.22", PASSWORD="suitcase123456", USERNAME="admin", SNAPSHOT_URL_OVERIDE="http://192.168.1.22/cgi-bin/CGIProxy.fcgi?cmd=snapPicture2&usr=admin&pwd=suitcase123456", PORT=80]

```



Here you see the format is: bindingID:THINGTYPE:UID [param1="string",param2=x,param3=x]


BindingID: is always ipcamera.

THINGTYPE: is found listed above under the heading "supported things"

UID: Can be made up but it must be UNIQUE, hence why it is called uniqueID. If you use PaperUI you will notice the UID will be something like "0A78687F" which is not very nice when using it in sitemaps and rules, also paperui will choose a new random ID each time you remove and add the camera causing you to edit your rules, items and sitemaps to make them match. Consider learning textual config which is covered above.


## Thing Configuration

IMAGE_UPDATE_EVENTS

Need to add a description on how to use this in textual config.

## Channels

See PaperUI for a full list of channels and the descriptions. Each camera brand will have different channels depending on how much of the support for an API has been added. The channels are kept consistent as much as possible from brand to brand when possible to make upgrading to a different branded camera easier without the need to edit your rules as much.

## Full Example

Use the following examples to base your setup on to save some time. NOTE: If your camera is secured with a user and password the links will not work and you will have to use the IMAGE channel to see a picture. FOSCAM cameras are the exception to this as they use the user and pass in plain text in the URL. In the example below you need to leave a fake address in the "Image url=" line otherwise it does not work, the item= overrides the url. Feel free to let me know if this is wrong or if you find a better way.

NOTE: You need to ensure the 001 is replaced with the cameras UID which may look like "0A78687F" if you used PaperUI to add the camera. Also replace AMCREST or HIKVISION with the name of the supported thing you are using from the list above.


                

*.things


```

Thing ipcamera:AMCREST:001 [IPADDRESS="192.168.3.2", PASSWORD="suitcase123456", USERNAME="DVadar", POLL_CAMERA_MS=5000]
Thing ipcamera:HIKVISION:002 [IPADDRESS="192.168.3.6", PASSWORD="suitcase123456", USERNAME="LukeS", POLL_CAMERA_MS=5000]

```



*.items

```

Image BabyCamImage { channel="ipcamera:AMCREST:001:image" }
Switch BabyCamUpdateImage "Get new picture" { channel="ipcamera:AMCREST:001:updateImageNow" }
Dimmer BabyCamPan "Pan left/right" { channel="ipcamera:AMCREST:001:pan" }
Dimmer BabyCamTilt "Tilt up/down" { channel="ipcamera:AMCREST:001:tilt" }
Dimmer BabyCamZoom "Zoom in/out" { channel="ipcamera:AMCREST:001:zoom" }
Switch BabyCamEnableMotion "MotionAlarm on/off" { channel="ipcamera:AMCREST:001:enableMotionAlarm" }
Switch BabyCamMotionAlarm "Motion detected" { channel="ipcamera:AMCREST:001:motionAlarm" }
Switch BabyCamAudioAlarm "Audio detected" { channel="ipcamera:AMCREST:001:audioAlarm" }

Image CamImage { channel="ipcamera:HIKVISION:002:image" }
Switch CamUpdateImage "Get new picture" { channel="ipcamera:HIKVISION:002:updateImageNow" }
Switch CamEnableMotionAlarm "MotionAlarm on/off" { channel="ipcamera:HIKVISION:002:enableMotionAlarm" }
Switch CamMotionAlarm "Motion detected" { channel="ipcamera:HIKVISION:002:motionAlarm" }
Switch CamEnableLineAlarm "LineAlarm on/off" { channel="ipcamera:HIKVISION:002:enableLineCrossingAlarm" }
Switch CamLineAlarm "Line Alarm detected" { channel="ipcamera:HIKVISION:002:lineCrossingAlarm" }

```


*.sitemap

```

        Text label="BabyMonitor" icon="camera" 
        {   
        Image url="http://google.com/leaveLinkAsThis" item=BabyCamImage refresh=5000
        Switch item=BabyCamImage label="Get new picture"
        Slider item=BabyCamPan label="Pan left/right"
        Slider item=BabyCamTilt label="Tilt up/down"
        Slider item=BabyCamZoom label="Zoom in/out"
        Switch item=BabyCamEnableMotion label="Turn motion on/off"
        Switch item=BabyCamMotionAlarm label="Motion detected"
        Switch item=BabyCamAudioAlarm label="Audio detected"    
        }   
        Text label="Driveway Camera" icon="camera" 
        {   
            Image url="http://google.com/leaveLinkAsThis" item=CamImage refresh=5000
            Switch item=CamUpdateImage label="Fetch new picture of Driveway"
            Switch item=CamEnableMotionAlarm
            Switch item=CamMotionAlarm
            Switch item=CamEnableLineAlarm
            Switch item=CamLineAlarm        
        }

```



## Reducing log sizes

There are two log files discussed here, openhab.log and events.log please take the time to consider both logs if a fast and stable setup is something you care about. On some systems with slow disk access like SD cards the writing of a log file can greatly impact on performance. We can turn on/up logs to fault find issues, and then disable them to get the performance back when everything is working.


To watch the logs in realtime with openhabian setups use this linux command which can be done via SSH with a program called putty from a windows or mac machine. 

```

tail -f /var/log/openhab2/openhab.log -f /var/log/openhab2/events.log

```


CTRL+C will close the stream. You can also use SAMBA/network shares to open or copy the file directly, but my favorite way to view the logs is with "Frontail". Frontail is another UI that can be selected like paperUI, and can be installed using the openhabian config tool.


openhab.log This file displays the information from all bindings and can have the amount of information turned up or down on a per binding basis. The default level is INFO and is the middle level of 5 settings you can use. Openhab documentation goes into this in more detail. Using KARAF console you can use these commands to turn the logging up and down to suit your needs. If you are having issues with the binding not working with your camera, then TRACE will give me everything in DEBUG with the additional reply packets from the camera for me to use for fault finding.


```

log:set WARN org.openhab.binding.ipcamera

log:set INFO org.openhab.binding.ipcamera

log:set DEBUG org.openhab.binding.ipcamera

log:set TRACE org.openhab.binding.ipcamera

```


events.log By default Openhab will log all image updates as an event into a file called events.log, this file can quickly grow if you have multiple cameras all updating pictures every second. To reduce this if you do not want to switch to only updating the image on EVENTS like motion alarms, you then only have 1 option and that is to turn off all events. Openhab does not allow filtering at a binding level due to the log being a pure output from the event bus. To disable use this command in Karaf.


```

log:set WARN smarthome.event

```


To re-enable use the same command with INFO instead of WARN. 


## Roadmap for further development

Currently the focus is on stability, speed and creating a good framework that allows multiple brands to be used in RULES in a consistent way. What this means is it should be less work to add functions to this binding instead of people creating scripts which are not easy for new Openhab users to find or use. By consistent I mean if a camera breaks down and you wish to change brands, your rules with this binding should be easy to adapt to the new brand of camera with no/minimal changes. 


If you need a feature added that is in an API, please raise an issue ticket here at this github project with a sample of what a browser shows when you enter in the URL and it is usually very quick to add these features. 


If this binding becomes popular, I can look at extending the frame work to support:

RTSP streams to the image channel.

PTZ methods for continuous move.

FTP/NAS features to save the images and delete old files.

Auto find and setup cameras on your network.

ONVIF alarms

1 and 2 way audio.


If you wish to contribute then please create an issue ticket first to discuss how things will work before doing any coding on large amounts of changes, this is due to needing to keep things CONSISTENT between brands and also easy to maintain. This list of areas that could be added are a great place to start helping with this binding if you wish to contribute. Any feedback, push requests and ideas are welcome.

