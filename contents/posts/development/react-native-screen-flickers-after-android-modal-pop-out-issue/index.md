---
title: React Native Screen Flickers After Android modal pop out Issue
date: 2021-01-22 10:02:08
update: 2021-01-22 10:02:08
tags:
 - development
description:
---

### Screen flickers after Google Sign In or In app purchase in only android

[https://stackoverflow.com/questions/56484002/screen-flickers-after-google-sign-in-dialog-dismiss-android-studio/57887749#57887749?newreg=fc9ab4a57cec45e1952c30f6c8f38043](https://stackoverflow.com/questions/56484002/screen-flickers-after-google-sign-in-dialog-dismiss-android-studio/57887749#57887749?newreg=fc9ab4a57cec45e1952c30f6c8f38043)

위에서 적어준 fade_in fade_out.xml 파일은 app/src/main/res/anim/fade_in.xml 과 같이 들어가야 한다.

`<item name="android:activityOpenEnterAnimation">@anim/fade_in</item>` 에서 말하는

@anim/fade_in 에서 @이가 res 를 의미하는 것 같다.
