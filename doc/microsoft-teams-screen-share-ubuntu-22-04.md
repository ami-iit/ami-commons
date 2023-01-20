# How to share your screen with Microsoft Teams on Ubuntu 22.04

## Problem
As of January 2023, the Microsoft Teams app you download from https://www.microsoft.com/en-us/microsoft-teams/download-app 
does not permit to do scren share on the default installation of Ubuntu 22.04, due to incompatibilities with Wayland. To solve this problem, please
choose **only one** of the following two solutions:
* S1: Use the web version of Microsoft Teams with Chromium
* S2: Disable Wayland and use Xorg

## S1: Use the web version of Microsoft Teams with Chromium

First of all, uninstall the desktop version:
~~~
sudo apt remove microsoft-teams
~~~

The advantage of using the web version of Microsoft Teams is that it also provides more features with respect to the Desktop one.

To use it, first of all install Chromium with the command:
~~~
sudo apt install chromium-browser
~~~

After that, launch Chromium, and first of all open the url [`chrome://flags/#enable-webrtc-pipewire-capturer`](chrome://flags/#enable-webrtc-pipewire-capturer) 
and enable the WebRTC PipeWire support option.

Then you can navigate to [`https://teams.microsoft.com`](https://teams.microsoft.com) and use Microsoft Teams as usual.

For better integration with the operating system, you can also install the Web App in your system by clicking on the download icon near the address:

![instalwebapp](https://user-images.githubusercontent.com/1857049/213678462-4edae1a7-808a-443f-86f7-4c5cc02f6c75.png)


## S2: Disable Wayland and use Xorg

If you did not have installed, install the Desktop Microsoft Teams application from https://www.microsoft.com/en-us/microsoft-teams/download-app .

Then, when you log in Ubuntu before inserting your username and password, click on the small button on the bottom right, and select the "Ubuntu on Xorg" option:

![213658742-ac0f7b17-e2f5-4a2e-95fe-f1f15e69aa9b](https://user-images.githubusercontent.com/1857049/213679097-c112502e-4f6c-4b2d-bb2f-317db57ad6d5.png)

After doing this, use the Desktop app as usual: you should be able to share your screen normally.

## References

* https://feedbackportal.microsoft.com/feedback/idea/7376fc49-4763-ec11-a819-6045bd78001e
* https://learn.microsoft.com/en-us/answers/questions/829747/how-to-enable-screen-sharing-in-ms-teams-desktop-o 
* https://askubuntu.com/questions/1329667/microsoft-teams-and-wayland-will-screen-sharing-be-fixed-in-21-04
* https://github.com/electron/electron/issues/23063
* https://techcommunity.microsoft.com/t5/microsoft-teams-blog/microsoft-teams-progressive-web-app-now-available-on-linux/ba-p/3669846
* https://linuxconfig.org/how-to-enable-disable-wayland-on-ubuntu-22-04-desktop
