---
title: "Flap, flap, flap"
layout: post
date: 2017-01-15 12:19
tag:
- c#
- game
- project
image:
headerImage: false
projects: true
hidden: true # don't count this post in blog pagination
description: "This is the Mac version of famous game Flappy Bird."
author: You Li
externalLink: false
---

![Screenshot](/assets/meetup-screenshot.png)


*We flap, and we laugh.*

<!-- <a href="/assets/Downloads/Flappy Bird(Mac).dmg" download>Click to download</a> the .dmg version and enjoy the game! -->
<div id="showCount"></div>
<input type="button" value="Download Now!" onclick="window.location = 'https://drive.google.com/open?id=0Bx4t2At4V-PJdllCckE2eFN1c0U'; CountFun();">
<script>
   var cnt=0;
   function CountFun(){
    cnt=parseInt(cnt)+parseInt(1);
    var divData=document.getElementById("showCount");
    divData.innerHTML="Number of Downloads: ("+cnt +")";//this part has been edited

   }
 </script>

Report bugs or any of your ideas in the comments!

Special thanks to Unity3D tutorials and the creator of Flappy Bird and all other games for inspiration.
