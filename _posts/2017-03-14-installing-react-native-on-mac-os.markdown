---

title: Getting Started with React Native on Mac OS
date: '2017-03-14 00:57:59'
---

I've been dabbling in some React Native recently with a goal of building a small mobile app for centralizing the management/control of my various IoT devices around the house. It's still a work in progress and sometimes a bit of time goes by between dev sessions, so I thought it would be useful to document my steps to get up and running.

Fortunately, the React Native [Getting Started docs](https://facebook.github.io/react-native/docs/getting-started.html) are great. I found everything needed to get going. Since I have an Android device, I installed [Android Studio](https://developer.android.com/studio/index.html) to develop using the Android emulator.

The biggest gotcha I encountered was the emulator would randomly crash after starting. There are some known [VirtualBox / Android Virtual Device (AVD) issues](https://www.virtualbox.org/ticket/14294), so make sure no VirtualBox VMs are running. Unfortunately, even after double-checking that I had no VMs running and completely closing VirtualBox, I still had issues with the AVD crashing or appearing to start but not actually starting. 

Turns out, if Docker is running, AVD will appear to start but will not be recognized as a connected device and crash randomly. Docker starts automatically on my Mac, so to do any React Native development I have to quit Docker first. 

Once Docker was shut down, I had no problems starting AVD.
Create a new AVD profile, start it, and after it boots up,  run <code>react-native run-android</code> to start developing!

