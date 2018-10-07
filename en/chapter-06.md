# Introduction

I was looking forward to starting a new course block focused on developing a **native C/C++ application in QML**. This block is not simply a change in programming language but has more implications behind it.

[UBports](https://ubports.com/) is doing an incredible job keeping the Ubuntu Touch project going. Not only do they continue with the updates, but they are also working on migrating the base system to Ubuntu 16.04 \(LTS\). This work, along with the future possibility of using Android applications in UBports can attract many users. If you want to financially support the project, you can make a small donation to their [Patreon's account](https://www.patreon.com/ubports).

**Programming an application takes a lot of time and effort**. Logically the programmer wants to reach the maximum number of users, and that, in UBports is difficult to achieve at the moment. Until now, a native application was programmed for a device and then tested on the computer. Why don't we change this way of working?

[Qt is a cross-platform development framework](https://www.qt.io/), which can work on mobile devices \(UBports, Android\) and desktops \(GNU/Linux, Windows\). You have good documentation and receive regular updates. The same code used on the desktop can work with relatively few modifications to other devices. This way you can work on the desktop with native tools and then adapt the application to UBports. We would have an application that could work in desktop and device mode. With a common base and a much larger number of users.

Logically things are not so simple and it would require a little more work to port the code to a mobile device taking into account the state of the SDK. Along with the changes UBports is making, we should not forget the development tools. At the moment the SDK is "complicated" to use. It has bugs that will not be corrected and you end up spending more time on the SDK than on programming the application.

In the course I will not forget to program for UBports. I'll just change the philosophy a little bit: the application will be programmed on the desktop and then adapted to the device. In this way, UBports users and those who want to learn how to program with Qt \(and have only the computer\) can follow the course.

Are you encouraged to participate in this madness?

