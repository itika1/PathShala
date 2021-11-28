# Make It Easy Call https://itika1.github.io/PathShala/
Live streaming website for everyone

## Important Credentials to join the stream
APP ID- 4cb5cbee30b24c1b8a9e8970fdd8b2d4 

TOKEN - 0064cb5cbee30b24c1b8a9e8970fdd8b2d4IACeqPOCdnVubqbcSNdRKxJO81tGqoOBRhEMxIxFmgCapldauFEAAAAAEAAUYhZw62ykYQEAAQDqbKRh
Channel Name - Basic
# The problem Make It Easy Call solves
This is a live video streaming website where people can join in a video call and start discussing important talks. This is absolutely free to start a video call and also it need low internet to run succesfully. All you need is that a agora account
# Challenges I ran into
First of all I've worked on such a project which is based on Agora technology and I'm very noob in this technology like I've heard one or two times before start of this hackathon but haven't applied before. So, initially I found it very difficult and I got confused where to start from and how to proceed. Also, as I'm working solo in HTM hackathon ( https://hackthemountain.tech/ )so also I was pretty worried about it. But in the meantime the HTM team helped me a lot through discord server ( https://discord.hackthemountain.tech/ ). Though I know html, css, js, npm, nodejs, git pretty well so I didn't find so much difficulties to start with the project. I've got the tutorial, documentations from the mentors of HTM. Also some documents are also available at agora official website ( https://www.agora.io/en/ ) 
As I start doing work on project I got stuck in the build time. As the path variable was not all set. So first I set those and fix the issues. Then I code the webpack file which is very important as we are running files using webpack. Quickly I install all the dependencies and then install also live server. 
To import AgoraRTC module in the project I used the following code
**import AgoraRTC from "agora-rtc-sdk-ng"**
## In package.json I added
```javascript
{
  "name": "agora_web_quickstart",
  "version": "1.0.0",
  "description": "",
  "main": "basicVideoCall.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "dependencies": {
    "agora-rtc-sdk-ng": "latest",
  },
  "author": "",
  "license": "ISC"
}
```
## In basicVideoCall.js file I added
```javascript
import AgoraRTC from "agora-rtc-sdk-ng"

let rtc = {
    localAudioTrack: null,
    localVideoTrack: null,
    client: null
};

let options = {
    // Pass your App ID here.
    appId: "Your App ID",
    // Set the channel name.
    channel: "test",
    // Pass your temp token here.
    token: "Your temp token",
    // Set the user ID.
    uid: 123456
};

async function startBasicCall() {
    // Create an AgoraRTCClient object.
    rtc.client = AgoraRTC.createClient({ mode: "rtc", codec: "vp8" });

    // Listen for the "user-published" event, from which you can get an AgoraRTCRemoteUser object.
    rtc.client.on("user-published", async (user, mediaType) => {
        // Subscribe to the remote user when the SDK triggers the "user-published" event
        await rtc.client.subscribe(user, mediaType);
        console.log("subscribe success");

        // If the remote user publishes a video track.
        if (mediaType === "video") {
            // Get the RemoteVideoTrack object in the AgoraRTCRemoteUser object.
            const remoteVideoTrack = user.videoTrack;
            // Dynamically create a container in the form of a DIV element for playing the remote video track.
            const remotePlayerContainer = document.createElement("div");
            // Specify the ID of the DIV container. You can use the uid of the remote user.
            remotePlayerContainer.id = user.uid.toString();
            remotePlayerContainer.textContent = "Remote user " + user.uid.toString();
            remotePlayerContainer.style.width = "640px";
            remotePlayerContainer.style.height = "480px";
            document.body.append(remotePlayerContainer);

            // Play the remote video track.
            // Pass the DIV container and the SDK dynamically creates a player in the container for playing the remote video track.
            remoteVideoTrack.play(remotePlayerContainer);

            // Or just pass the ID of the DIV container.
            // remoteVideoTrack.play(playerContainer.id);
        }

        // If the remote user publishes an audio track.
        if (mediaType === "audio") {
            // Get the RemoteAudioTrack object in the AgoraRTCRemoteUser object.
            const remoteAudioTrack = user.audioTrack;
            // Play the remote audio track. No need to pass any DOM element.
            remoteAudioTrack.play();
        }

        // Listen for the "user-unpublished" event
        rtc.client.on("user-unpublished", user => {
            // Get the dynamically created DIV container.
            const remotePlayerContainer = document.getElementById(user.uid);
            // Destroy the container.
            remotePlayerContainer.remove();
        });

    });

    window.onload = function () {

        document.getElementById("join").onclick = async function () {
            // Join an RTC channel.
            await rtc.client.join(options.appId, options.channel, options.token, options.uid);
            // Create a local audio track from the audio sampled by a microphone.
            rtc.localAudioTrack = await AgoraRTC.createMicrophoneAudioTrack();
            // Create a local video track from the video captured by a camera.
            rtc.localVideoTrack = await AgoraRTC.createCameraVideoTrack();
            // Publish the local audio and video tracks to the RTC channel.
            await rtc.client.publish([rtc.localAudioTrack, rtc.localVideoTrack]);
            // Dynamically create a container in the form of a DIV element for playing the local video track.
            const localPlayerContainer = document.createElement("div");
            // Specify the ID of the DIV container. You can use the uid of the local user.
            localPlayerContainer.id = options.uid;
            localPlayerContainer.textContent = "Local user " + options.uid;
            localPlayerContainer.style.width = "640px";
            localPlayerContainer.style.height = "480px";
            document.body.append(localPlayerContainer);

            // Play the local video track.
            // Pass the DIV container and the SDK dynamically creates a player in the container for playing the local video track.
            rtc.localVideoTrack.play(localPlayerContainer);
            console.log("publish success!");
        }

        document.getElementById("leave").onclick = async function () {
            // Destroy the local audio and video tracks.
            rtc.localAudioTrack.close();
            rtc.localVideoTrack.close();

            // Traverse all remote users.
            rtc.client.remoteUsers.forEach(user => {
                // Destroy the dynamically created DIV containers.
                const playerContainer = document.getElementById(user.uid);
                playerContainer && playerContainer.remove();
            });

            // Leave the channel![1624001261553](https://user-images.githubusercontent.com/52042964/123542570-f52e7d00-d767-11eb-8f41-9acfb60d77d6.png)
.
            await rtc.client.leave();
        }
    }
}

startBasicCall()
```
This is the way to test any agora sdk project. For that I ran 
1) npm run build
2) npm start run:dev 
Now I see that my project was running on the server. Then I go to my agora account and open my project that I've already built earlier. From there I copy the APP ID, Token and Channel Name which are needed to join a channel and then all done :)
Thanks to HTM team for supporting me and encouraging me to built a project with Agora.
# Technologies I used
1) Agora SDK
2) Agora API
3) Agora.io
4) HTML5
5) CSS
6) BOOTSTRAP
7) Javascript
8) Node js
9) NPM
10) git
