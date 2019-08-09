---
layout: post
title:  "How to publish and distribute your Android library"
date:   2019-05-28
images:
  - https://miro.medium.com/max/1400/0*6Th4syvZgyIhhACo
---

You have just built an awesome Android library but you don’t know how to publish it? You are in the right place; in this article, I will explain to you the process that I use to publish my two libraries.

First of all, let’s assume that you have already developed your Android library, so you have a library module in Android Studio.

![An example of a library module](https://cdn-images-1.medium.com/max/2000/1*FoYazVYDj5FlCpQuX88v9w.png)
<center>*An example of a library module*</center>

The library will be published to *jCenter, *a Maven repository hosted by [bintray.com](http://www.bintray.com). The process is simple: the library is built locally in Android Studio, the artifacts will be upload to *bintray* and then linked to *jCenter.*

![](https://cdn-images-1.medium.com/max/2000/1*iExRbQhqoflgn3mJjuwY7w.png)

In this way, the developers that want to use your library has only to declare it in the *build.gradle *file of their projects. For example:

```gradle
implementation "com.prof.rssparser:rssparser:2.0.4"
```

The first thing to do is to create a [Bintray](https://bintray.com/) account and for open source projects is FREE.

![Create a Bintray account](https://cdn-images-1.medium.com/max/2000/1*_tBskl_JTR6UbEYZD0sSpg.png)
<center>*Create a Bintray account*</center>

After the creation of the account, you have to choose a repository. Let’s go with Maven (I know, you are thinking “Why? I want to publish it to jCenter”. Well, as said above jCenter is a Maven repository so we have to choose Maven).

![Choose Maven as repository](https://cdn-images-1.medium.com/max/2000/1*iGCmnnoRg0WIAE8Qywaodg.png)
<center>*Choose Maven as repository*</center>

Now you can create a new package inside your Maven repository.

![Add a new package to the Maven repository](https://cdn-images-1.medium.com/max/2000/1*PP8lVgEaoTzjaOqmrC2Isw.png)
<center>*Add a new package to the Maven repository*</center>

![Add basic information of the library](https://cdn-images-1.medium.com/max/2000/1*dZhP4R7gzMTVzdkriYFzHw.png)
<center>*Add basic information of the library*</center>

After adding all the basic information of your library, like name, description, license, tags, etc., the repository of the library is ready.

![](https://cdn-images-1.medium.com/max/2000/1*CYoMvJQtZu3i3PSgiB74Bw.png)

Now, back to the code. I’ll take as reference, a library that I’ve developed, so you can dive into the real code if you want.

> [prof18/RSS-Parser](https://github.com/prof18/RSS-Parser)

First of all, you have to add some dependencies into the *build.gradle* file of your project.

```gradle
classpath 'com.github.dcendents:android-maven-gradle-plugin:2.0'
classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.4'
```

At this point, we are ready to create the publishing script. Inside the library project, create a *publish.gradle* file and apply it on the *build.gradle *file* *of your library.

```gradle
apply from: 'publish.gradle'
```

{{< gist prof18 b8783f870354d3e131cea887a2d6a6b4 >}}

Before explaining the whole script, a clarification of the library signature is mandatory. As mentioned earlier, the signature of your library has the following shape:

```gradle
com.prof.rssparser:rssparser:2.0.4
```

and it is composed of three parts:

* the Group ID (*com.prof.rssparser*);

* the Artifact ID (*rssparser*);

* the Version (*2.0.4*).


```
GROUP_ID:ARTIFACT_ID:VERSION
```

So, for the example library that we have created above, the Group ID is: “*com.your.awesome.lib*”, the Artifact ID is: “*awesomelib*” and the version is: “*1.0.0*”. These fields are arbitrary and you have to choose a name; keep in mind that with a new update, only the version will change in the signature.

```gradle
com.your.awesome.lib:awesomelib:1.0.0
```

Returning to the script, the lines [7–33] are responsible to build the library, generate the artifacts and link all the external dependencies that you are using.

The lines [38–58] instead are responsible for the uploading of the library. Here you have to specify your bintray username, bintray API key (you can retrieve and revoke it under *Edit Your Profile -> API Key*), the version, the artifact and the group of the library and other useful information. You can found all the different information that you can provide, in the documentation of the gradle bintray plugin.

> [bintray/gradle-bintray-plugin](https://github.com/bintray/gradle-bintray-plugin)

Remember to keep private the username and the private key. There are many ways, for example, I saved them in the *local.properties:*

```gradle
bintray.user=<your-name>
bintray.apikey=<your-key>
```

and loaded them in the *publish.gradle *script with a few simple lines of code.

```gradle
def properties = new Properties()
properties.load(new FileInputStream("local.properties"))

user = properties.getProperty("bintray.user")    
key = properties.getProperty("bintray.apikey")
```

Finally, it’s time to open the Terminal and launch the build and upload task (I recommend to use the *-- info *flag to have a better understanding of what it is going on).

```gradle
./gradlew clean build bintrayUpload --info
```

If everything went well, the artifacts have been uploaded to bintray but not yet published.

![New version uploaded but not yet published](https://cdn-images-1.medium.com/max/2000/1*mQwixMjhUz7JUiyyDnHPyw.png)
<center>*New version uploaded but not yet published*</center>

I have intentionally disabled the automatic publishing because it can save your life if there are some errors. But if you want, there is a flag that you can put in the *publish.gradle* file that automatically publishes the library as soon it has been uploaded.

To publish the library, you have to click the *Publish *button on a banner that appears in the repository page of the library.

![](https://cdn-images-1.medium.com/max/2000/1*-B3JOLzBAO6-sQu7A3nHKg.png)

And now the last step. You have to link the library to jCenter. This process must be done only the first time. At the right bottom of the library repository page, there is a “*Add to jCenter*”* *button.

![Link the library to jCenter](https://cdn-images-1.medium.com/max/2000/1*D9LScYGoESnPK3VJ-TRRjA.png)
<center>*Link the library to jCenter*</center>

Send the request and wait for the approval.

![](https://cdn-images-1.medium.com/max/2000/1*pgF7SwSRMTh_VfitSncemQ.png)

Within max two hours, your library is published and linked to jCenter, as you can see in the repository page.

![](https://cdn-images-1.medium.com/max/2000/1*Tmp88sDZCNhYphsK_fA29g.png)

Finally, you can tell the world about your awesome library and provide the signature to implement it.

```gradle
dependencies { 
    implementation ‘com.your.awesome.lib:awesomelib:1.0.0’ 
}
```

And that’s all folks! I agree with you that the process is not simple but I hope to have cleared all your doubts.

----

*Published also on [Medium](https://medium.com/swlh/how-to-publish-and-distribute-your-android-library-ce845c68c7f7)*