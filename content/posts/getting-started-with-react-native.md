+++
author = "Sandarenu"
comments = true
date = "2016-03-22T21:53:17+05:30"
draft = false
featured_image = "images/reactive-nativingitup.png.800x600_q96.png"
menu = ""
share = true
slug = "getting-started-with-react-native"
tags = ["Programming", "React", "React-Native"]
title = "Getting Started with React Native"

+++

[React Native](https://facebook.github.io/react-native/) is a framework that allows us to create Native Android and iOS applications using JavaScripts by Facebook. It is a extention of [React JavaScript framework](http://facebook.github.io/react/) available for Web application development. With React-Native we can create native android or iOS applications without knowing much about native application development. It opens up the native application development to web developers who knows JavaScript and CSS. 

Main advantage of React-Native becomes very clear when we won't to develop an application which requires a normal Web interface and native Android and iOS applications. By using React and React-Native we can reuse most of the code between three variations of our application. Previously we would have to maintain three separate code bases, but by using React we can use single code base and create all three types of applications; web, android and iOS.

When I was first started using React-Native, I had zero experiance with Android application development. But I was able to start developing andorid application without having much knowledge on android native applictions. React Native site has [good guide](https://facebook.github.io/react-native/docs/android-setup.html) on how to get start android application development. But when I started setting up my Ubuntu machine, I faced few deficulties. So I'm doing this post on how to overcome those issues and to the setup smoothly. 

* Follow the instructions given in the react guide to setup android SDK if you have not installed it yet. After installing SDK you'll have to include that to your `$PATH`. Update you `~/.profile` with following. 

```
export ANDROID_HOME=/home/sandarenu/software/android-sdk-linux
export PATH=$PATH:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools
```

* Install [Genemotion Andoid Emulator](https://www.genymotion.com/). It has a free for personal usage package. It has some limitaions than the paid verions, but features available in free version is enough for us. After installing Genemotion, . Create soft link to  `genymotion` executable to `/usr/bin`.

```
sudo ln -s /home/sandarenu/software/genymotion/genymotion /usr/bin/genymotion
```

* To use genemotions we need Oracle Virtual Box as well. If you don't have it installed [install it](https://www.virtualbox.org/wiki/Linux_Downloads). 

* If you are using 64-bit Ubuntu, like I'm (does anybody uses 32-bit anymore?...), then you have to install an additional dependency to use android SDK. Otherwise it throws following error when builing the application.

```
java.io.IOException: Cannot run program "/home/sandarenu/software/android-sdk-linux/build-tools/23.0.1/aapt": error=2, No such file or directory
```

To fix that I have to install following library.

```
sudo apt-get install lib32stdc++6 lib32z1
```
 Refer [stackoverflow discussion](http://stackoverflow.com/questions/18928164/android-studio-cannot-find-aapt/18930424#18930424) for more information on this.

 * Then install NodeJS if you have not installed it yet. React Native [site has the instructions on this](https://facebook.github.io/react-native/docs/getting-started-linux.html). I already had an old version of Node installed in my matchin. I had to update it to the latest version. To do that I used following.

```
sudo npm cache clean -f
sudo npm install -g n
sudo n stable
```
I also had to update my simlink. 
```
sudo ln -sf /usr/local/n/versions/node/<VERSION>/bin/node /usr/bin/node 
 ```

 * We also have to install `watchman` tool by facebook as well. Instuction on how to do that is also in the above guide. 

 * Then install React Native

 ```
 sudo npm install -g react-native-cli
 ```

 Now your setup is ready. We can create our first react native android application.

 ```
 react-native init HelloReactNative
 ```

 Go inside `HelloReactNative` folder. Running react native andoid application is a two step process. First we have to build the native application and then we have to start the react native development sever. Actually there is another step, you have to start your emulator first. 

 To build android appication use following commands, after you fire up your emulator.
 ```
 npm install
 react-native run-android
 ```
Build will take few minutes. First time build will take more time since npm and gradle have to download its dependencies. While native build is running we can start react native dev server. 
```
react-native start
```

If everything goes well you should be able to see your first android application powered by react-native in your emulator. 
