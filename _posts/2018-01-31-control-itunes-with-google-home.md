---
layout: "post"
title:  "Controlling Apple Music With Google Home"
date: 2018-01-31
categories: google-home itunes IFTTT Dropbox Automator tutorial applemusic smarthome
permalink: /blog/:year/:month/:day/:title/
author: "Vince Navarro"
---

I honestly just wanted my Google Home Mini to play a song from Apple Music. But with Google Home's *limited integration* to Apple Music, I had to take a different approach. That approach essentially has Google send a command to play a song/playlist on the iTunes app on my Macbook. So to make this work, I basically *frankensteined* a quick bandaid using IFTTT and Automator. Switching to **Spotify** would have been the easier option, but I digress.

## Pre-requisites
   - Have your Google Home and Google Assistant all setup, pretty much the bare minimum when initially setting up your Google Home.

## Let's Dive In

1. **[Sign up for Dropbox][1]** and then **[install the app for your computer][1a]**.
2. Open your Dropbox folder on your computer and create a new folder called **'HomeAutomation'**, _(I did this all in Finder)_
<br>
   * Now inside the 'HomeAutomation folder' create another folder called **'play'**
3. **[Sign up for IFTTT][2]**, _(You can do all the IFTTT steps from the phone app if you want)_
4. Click **My Applets** tab, and then click **New Applet**

<p align="center">
   <img src="/assets/control-itunes-with-google-home/this.png">
</p>


5. You will now see something that says 'if this then that', click on the blue **this**.
   * _**Choose a service**_ : Find/search for a service called **Google Assistant** and select it
   * _**Choose trigger**_ : Choose the option: **Say a phrase with a text ingredient**
   * _**Complete trigger fields**_ :
      * _**What do you want to say?**_ &nbsp;&nbsp; `bump $`
      * _**What do you want the Assistant to say in response?**_ &nbsp;&nbsp; `bumping $`
      * Now click **create trigger**
         * ** _Feel free to replace 'bump' with any trigger word you want_

<p align="center">
   <img src="/assets/control-itunes-with-google-home/that.png">
</p>

6. If everything went well, you're back to the 'if this then that' screen, so now press on the blue **that**
   * _**Choose action service -**_- Find/search for an action service called **Dropbox** and select it.
   * _**Choose action -**_- Choose the option: **Create a text file**
      * _**Complete action fields**_ :
         * _**File name**_ &nbsp;&nbsp; `song`
         * _**Content**_ &nbsp;&nbsp; `{{TextField}}`, or press **Add ingredient**, and select **TextField**
         * _**Dropbox folder path**_ &nbsp;&nbsp; `/HomeAutomation/play/`
         * Now press **Create action**
7. You can leave everything default here, but you can uncheck 'Receive notifications when this Applet runs'. And  **press Finish**
8. Time to check if dropbox is working:
   * Say **'Hey google bump test'**
   * Now if everything goes correctly, in a few seconds a file named `song.txt` will appear in your `HomeAutomation/play/` folder in Dropbox.
   * If you open it, it should say `test`,
   * So now we have to **DELETE song.txt** so it doesn't mess up our automator script, which we will implement below.
   and now we can do the automator code

   ![automator]({{"/assets/control-itunes-with-google-home/automator.png" | absolute_url }}){:width="100%"}

9. **Open automator** on your computer
   * Click on **File** up top, and then press **New**
   * Now choose **Folder Action** and then click **Choose**
10. A new Untitled.workflow should show up and you should see 'Folder Action receives files and folders added to'
   * Now **click on the drop down** and then **click Other..**.
   * At this point you want to **select the `play` folder** in your Dropbox
11. Now in the search bar to the right of the variables tab, **search for `Run AppleScript`**, and **double click `Run AppleScript`**
12. Now I have created a nice little AppleScript, so just **copy it and replace it with everything in the 'Run AppleScript' editor window.**

```sh
on run {input, parameters}

         -- Change to your HomeAutomation/play/ destination below
	set newFile to ("Users:YOURMACNAME:Dropbox:HomeAutomation:play:song.txt") --set newfile
	set songName to paragraphs of (read file newFile) --grab song name

	-- Have iTunes play the song
	tell application "iTunes"
		set songList to (every track whose name contains songName)
		repeat with bump in songList
			play bump
		end repeat
	end tell

	return input
end run
```

13. Now go back to the search and look for **Move Finder Items to Trash** and **double click it**
14. Pretty much done, now it's just house cleaning.
   * Click **File** and then **Save**, and name it whatever you like.
   * Now go back to **File** and click **Export**, and I would recommend saving it in your `HomeAutomation/` folder as a nice backup.
15. Now try saying the Google command, and it *should* work.

## Limitations
Since there is nothing scrubbing the text that you input through the command you say, sometimes you wont find a song.

**Example of what would happen:**
>Hey google bump lets get it on

The song won't play because the song's actual title in my library is `Let's Get It On`. That apostrophe pretty much ruins everything. Another limitation would be that the song has to be in your library, so it can't be a song you haven't added.

## Work arounds/TDL
   * Change the applescript to play a playlist instead, so there's less chance of playing errors
   * Include a possible fuzzy search to scrub the song name
   * SWITCH TO SPOTIFY
   * Add some pictures to this tutorial

## So what's really happening here?
What happens is IFTTT receives a command that creates a text file in your DropBox with the name of the song/playlist you want played. Then automator senses that a new file was added. It then runs an apple scripts that grabs the song name, and then tells iTunes to play it. And lastly, automator deletes the text file.

## Just... why?
At the current date of this post, Google Home integration with apple music is limited to iOS devices. Meaning only voice commands from within the Google Home app on your phone, not your actual Google Home, will trigger music commands. Not going to lie, this is a bit *hokie* in a sense. But hey it wasn't that bad making it. But yeah honestly, just switch that Apple Music membership for Spotify until there is full integration for Google Home.

[1]: https://www.dropbox.com/
[1a]: https://www.dropbox.com/install
[2]: https://ifttt.com
