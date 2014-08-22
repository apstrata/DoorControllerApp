DoorControllerApp
=================

Sample kinoma application that leverages [Apstrata's](http://www.apstrata.com) Device Mgt, Identity Mgt and Orchestration APIs. The door controller application is inspired from the Gogi Smartlock device. Its purpose is to simulate a device that can be used as a smart door lock that provides two main features:
•	Detection and authentication of nearby users (holders of the client application) and automatic unlocking of the door
•	Detection of intrusion attempts (simulated in the application by reaching a certain level of pressure on the screen) and automatic triggering of an intrusion management process.

**Note: you need to use this application in combination with [DoorControllerApp](https://github.com/apstrata/DoorControllerApp), [DoorControllerApp-backend](https://github.com/apstrata/DoorControllerApp-backend) and [kinoma-sdk](https://github.com/apstrata/kinoma-sdk)**

How to use
==========

* Import the DoorControllerApp project into Kinoma Studio
* Import the [kinoma-sdk](https://github.com/apstrata/kinoma-sdk) to Kinoma studio and link it to your project (or just copy the "apstrataClientModule.js" file 
* Make sure you have deployed the back-end scripts available in [DoorControllerApp-backend](https://github.com/apstrata/DoorControllerApp-backend) on your Apstrata application account
* Make sure that you have specified adequate values for your test in the "apstrata.kinoma.config" file (back-end)
* Make sure that you have specified adequate values for your test in the main.xml file (DoorControllerApp/src/main.xml)
* Right-click on "application.xml" > "Run as" > "Kinoma simulator"
* This opens a virtual Kinoma player and displays a white screen with a red lock with a generic title 
(Door Controller)
* To simulate a breaking in attempt, click on the screen for more than a second (or press a key on the keyboard for 2-3 seconds). This triggers a call to a process (script) executing in the back-end (Apstrata). The process mainly sends 
an email to a pre-configured user, with two links, one to discard the alert and the other to confirm it.

How it works
============

**Detection and authentication of nearby users**

* When the DoorControllerApp (hereafter referred to as "controller") is launched, it starts sharing itself on the 
local network (so it can be discovered by a DoorControllerClientApp instance). It also triggers a call to Apstrata's GetDevice API (part of Device Management APIs) in order to retrieve data about the controller device, notably its type
that it displays on the screen instead of the aforementioned generic title)
* When the DoorControllerClientApp instance (hereafter referred to as "client") discovers the controller, it sends
it a message ("/credentials" handler), providing it with a username (the user of the client device), an authentication
token (identifying that user against Apstrata) and an instruction (open)
* The controller uses the received token and username to sign a call to Apstrata's GetUser API and retrieve the user's
profile (if token is valid). It displays a welcome message customized with the user's name obtained from the profile.

**Detection of intrusion attempts**

* onTouchBegan/onTouchEnded and onKeyDown events are consumed by specific handlers in the controller app. Once a
given threashold (pre-configured in the app) is reached, the controller invokes Apstrata's RunScript API in order to 
execute the "apstrata.kinoma.api.HandleIntrusionManagement" script, which triggers the instrusion management process.
