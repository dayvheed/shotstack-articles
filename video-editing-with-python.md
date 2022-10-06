# Video Editing Guide using Python

Video editing is stressful. It takes a great deal of time to meticulously apply effects to a video, combine clips, add overlays, and whatnot. It becomes even more tedious when you have multiple videos to edit. In such cases, editing manually can not only be mentally exhausting but also time-consuming and grossly inefficient. In this tutorial, you will learn a more effective way of editing videos using Python.

By the end of this tutorial, you would have understood how to automate video editing tasks with Python. Let’s jump in.


## Getting the Basics

First off, to follow along in this tutorial, you will require a decent knowledge of Python. We’d be using a special video editing library called Shotstock. Shotstack is a **video editing Python library** thatprovides video editing API and rendering infrastructure, enabling one to build workflows and automate video generation using code.

To use Shotstack [Python video editing SDK](https://github.com/shotstack/shotstack-sdk-python), you’d first need to create a Shotstack account. Head over to [Shotstack's official website](https://shotstack.io/) and sign up. Once you have an account created, click on your name in the top-right corner, and then the ‘API keys’ button. You’d find your staging API key and your production API key. For this tutorial, we shall use the staging API key so have that copied somewhere safe.


![](https://lh6.googleusercontent.com/PXh8FzztRrdEkZNqLb2yshXQZx0DeYysreNChYLkHpKok1EEZD0Be5WKw44l4zPWHmEqq3S3Mvj1G2rs_GfiiwIh9A7g1McKwqoAs0RYfy1UA-1oYZTZ1_YUC6jRkOeWyAVkreT35bZngeP3AaQX4ctAAwsdRGTn6XS5z1Cu8JcHEA7cfRwWl3tAww)


Next, you’d need to install the [Shotstack Python SDK](https://pypi.org/project/shotstack-sdk/) using pip. Open Shell or Terminal and type in the command below.


```
pip install shotstack-sdk
```


Before we delve into Python codes, let’s discuss the fundamental steps when using the Shotstack Python SDK.


## Steps to Using Shotstack SDK


### Step 1: Import the relevant Shotstack methods. 

For now, we shall import the shotstack method and the edit_api method using the import statements below.


```
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
```



### Step 2: Define the API endpoint. 

For Shotstack, it is "[https://api.shotstack.io/stage](https://api.shotstack.io/stage)".


```
host = "https://api.shotstack.io/stage"
```



### Step 3: Create a configuration object and store your API key. 

You’d need to instantiate a variable from the configuration class that stores your API staging key.


```
configuration = shotstack.Configuration(host=host)
configuration.api_key["DeveloperKey"] = {insert your API staging key here as a string}
```


 


### Step 4: Create an API client and an instance of API calls. 

The API client defines who is making the API requests. To create an API client, the `ApiClient()` class is called and the configuration object is passed as an argument. This process allows Shotshock to identify you as the one sending the API calls.

After creating the API client, you’d need to create an instance of the API calls inside the with block. Since we are performing editing operations, the `edit_api()` method is used. It takes the API client earlier defined as a parameter.


```
#properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this creates an API client
   api_instance = edit_api.EditApi(api_client) #this creates an instance of API calls.
```


We use the with statement in Python to properly access the resources in the API. It also ensures the resources are called once we are done with our API requests.

Note that the rest of the code will be inside the with statement block.


### Step 5: Create an asset. 

An asset is the file you wish to edit. If you want to edit a video file, a `VideoAsset()` class is used to create a video asset. Conversely, the `ImageAsset()` class creates an image asset while the `HtmlAsset()` creates an HTML asset.


### Step 6: Create a clip. 

A clip is the portion of the asset you wish to import to Shotstack. The `Clip()` class is used to create a clip.


### Step 7: Create a track. 

A track is simply a combination of clips. The `Track()` class which is used to create a track requires a list of clips as an argument.


### Step 8: Create a timeline. 

A timeline defines how the clips are displayed per time. A timeline can contain one or multiple tracks with one or multiple clips. Note that clips in different tracks can overlap and when they do, the overlapping clips are played concurrently.

For illustration purposes, the screenshot below is an example of a timeline in a video editing software. As seen, there are three tracks. Track 1 has Clip 1 that plays from the 0.0 seconds. Clip 2 and 3 plays at a later time in Track 2. Track 3 is empty.


![](https://lh6.googleusercontent.com/clG52XRBDM2O9_eQhNfQRsEhbMymmJtlzDND4AyUt1nQDlOM_PyG586bjXClXjl31Z3WCsp-KwgnNojwu_Pn-uz9OQBcY8WIGYrzqbYJwjhiNyLblIUc0695DrwujuTOj4V8mOs4IOrmS6wDbkr1svbu_O-ogbN4-KrKx-Ut3OxgMf26uiMwEE_WUw)



### Step 9: Define the output file settings. 

After creating the timeline, the next step is to define how you want the outfile file to be. If it is a video file, you may define the format of the video, whether mp4, gif, etc or the resolution of the output video. Meanwhile, the Shotstack SDK tool can also [convert MP4 to GIF](https://shotstack.notion.site/Video-editing-guide-with-Python-636f11569c1048df9afca6dbc87f59a4#:~:text=https%3A//shotstack.io/learn/convert%2Dmp4%2Dto%2Dgif%2Dpython/).


### Step 10: Define the final edit object. 

Finally, you would need to capture your timeline and output settings in one object. The `Edit()` class is used to create the edit object. 


### Step 11: Render the edit object. 

Once you have an edit object that captures the timeline and output, you will require the `post_render()` method of the `api_instance()` function to render the edit. 


```
api_instance.post_render({insert the edit object here})
```



### Step 12: Assess the render file from your [Shotstack Dashboard](https://dashboard.shotstack.io/renders). 

You can check the status of your rendered files and access the rendered files from your dashboard. And that’s all there is to it!

Looking back, Step 1 to Step 12 is the framework for making video edits with the Shotstack SDK. As you may have guessed, Steps 1 to 4, and Step 11 remain the same irrespective of the edit you wish to perform. Step 5 to 11 defines the type of edit you wish to perform. Going forward, we will limit our discussion to these steps, elaborating on the important ones for a given task.


## Trimming a Video

Trimming simply defines where the imported video should begin. The `VideoAsset()` class which is used to create a video takes the source `src` as a required argument. It also takes the trim argument which defines the time (in seconds) the video should be trimmed. To trim a video, pass the trim argument when calling `VideoAsset()` class.

The source is the link to the video, hosted in a public repository. We’d use Amazon AWS S3 to host a [sample video](https://shotstackvideo.s3.amazonaws.com/video.mp4). Let’s say we’d like to trim the first 5 seconds of the video, we set trim to be 5.0


```
from shotstack_sdk.model.video_asset import VideoAsset
video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )
```


Next, we define the clip and pass the video_asset as an argument. The start and length variables are also required when calling the `Clip()` class. They define the seconds the video would start playing and how long the video will last for. If the start argument is set to a value greater than 0, the video displays a black screen until that time in seconds is reached.


```
from shotstack_sdk.model.clip import Clip
video_clip = Clip(
       asset = video_asset,
       start = 0.0,
       length = 20.0
   )
```


Next, we define the track and pass this single video_clip variable as a list.


```
from shotstack_sdk.model.track import Track
track = Track(clips = [video_clip])
```


Afterwards, we define the timeline and pass a list of tracks. For now, we have one one track.


```
from shotstack_sdk.model.track import Timeline
timeline = Timeline(tracks=[track])
```


Next, we define the output. Let’s say we want an mp4 file with an SD resolution.


```
from shotstack_sdk.model.output import Output
output = Output(
       format = "mp4",
       resolution = "sd"
)
```


We then define the edit object and pass the timeline and output as arguments.


```
from shotstack_sdk.model.edit import Edit
edit = Edit(
       timeline = timeline,
       output = output
   )
```


Finally, we render the edit with the `post_render()` method and pass the edit object as argument. To be sure we don’t break our code due to errors, we can wrap this step in a try-except block as shown below.


```
#render the video edit
try:
    api_response = api_instance.post_render(edit)
    print(f"{api_response.response.message}")
    print(f"{api_response.response.id}")
    print()


except Exception as e:
    print(f"An error occurred: {e}")
```


After running the code, the video is rendered and can be accessed on your Shotstack dashboard. 

The rendered video can be found circled in the screenshot and is shown [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/3e9e13ab-6b06-451c-bef5-373a31dacf74.mp4?_ga=2.25325181.1979803603.1664818817-660097783.1663657741&_gl=1*wqvtq4*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDkyMzc4Ny4xMi4xLjE2NjQ5MjM4MDUuMC4wLjA.).


![](https://lh5.googleusercontent.com/J2tY93U8bXXK8w0x53SwNeLU08JxyUfvveDy5DLWwyfe1cwv9N7d5rZcMU9liZMtd8YrnzoOeMOkpH-eH5jHGVCfrpstVhiLhcW_0dl_g9toDgI1h3fxaZc7lU94ZH5o45TPFHlefUGS4FqhVLny01dWdeOIlN5nIMXfLieDGHpPgRr2WgZvffossg)


Putting it all together, here is the entire code for this example.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this defines the API client
   api_instance = edit_api.EditApi(api_client) #this is an instance of the API call.


   # create a video asset trimming the first 5 seconds
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )
   # create a video clip from the asset
   video_clip = Clip(
       asset = video_asset,
       start = 0.0, #blank before this
       length = 20.0
   )


   # create a video track
   track = Track(clips = [video_clip])


   # add the track to the timeline
   timeline = Timeline(tracks=[track])


   # define the output format
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   # define the edit details
   edit = Edit(
       timeline = timeline,
       output = output
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Using the Merging Asset

The `MergeAsset()` class is used to create reusable templates for videos. Let’s say you want to make a video that begins with a personalized welcome message with specific names. You can use the `MergeAsset()` class to dynamically replace the names of the receiver.

To do this, we’d first instantiate a title variable for the video with the `TitleAsset()` class which takes a text as a compulsory argument. The placeholder is represented with double curly braces {{}}. You can also customize the position of the text, style, background, size, color, etc. 


```
from shotstack_sdk.model.title_asset import TitleAsset
title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )
```


This title asset must then be added to a clip. If we want the title to be displayed for the first two minutes, the start argument is set to 0, and the length is set to 2.


```
title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 2.0
   )
```


The `Clip()` class has many other non-compulsory arguments. Check out [Shotstack video editing Python documentation](https://github.com/shotstack/shotstack-sdk-python) to determine other non-compulsory arguments that can be passed.

Next, we add both title clip and video clip to the track. 


```
track = Track(clips = [video_clip, title_clip])
```


We then instantiate the `MergeAsset()` class. The class takes two required arguments: `key` and `replace`. Key is simply the field you want replaced while replace is what you want to replace it with. In our case, the `key` is NAME and if the video is to show a text, “Welcome, David”, the `replace` is set to be David.


```
from shotstack_sdk.model.merge_field import MergeField
merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )
```


Finally, you add the mergeField variable to the `edit()` function as a list. 


```
edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )
```


Adding these blocks of codes to the initial code, the rendered video is shown [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/c2145927-4d47-4653-948e-dbbdfc884e71.mp4?_ga=2.4395379.1979803603.1664818817-660097783.1663657741&_gl=1*1aqetls*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDk3MjQ0OC4xNC4xLjE2NjQ5NzU2NzguMC4wLjA.) as expected.

The full code for this exercise is shown below.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5
   )


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track = Track(clips = [video_clip, title_clip])


   #add the track to the timeline
   timeline = Timeline(tracks=[track])


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Adding Transitions

Transitions define what happens at the beginning and end of a clip. In the last example, we did not define any transition in the title clip and video clip. Thus, the video clip appeared sharply after the title clip. Let’s change that. If, say, we want the title and video clip to fade in and out, this is defined in the `Transition()` class.


```
from shotstack_sdk.model.transition import Transition

transition = Transition(
_in = 'fade',
out = 'fade'
)
```


Note that the `in` argument has a trailing underscore `_in` because `in` is a reserved keyword in Python. 

Now that the fade transition has been defined, we can add it as an argument in both video and title clips.


```
video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition
   )


title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )
```


After running the code, the rendered video now has a transition as shown [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/d6e569ae-998f-4a22-a1e1-251ad15dca61.mp4?_gl=1*2mbapy*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDk3MjQ0OC4xNC4xLjE2NjQ5NzY1MzIuMC4wLjA.&_ga=2.96548447.1979803603.1664818817-660097783.1663657741).

The entire code for this example is shown below.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField
from shotstack_sdk.model.transition import Transition

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #add a fade in and fade out transition
   transition = Transition(
   _in = 'fade',
   out = 'fade'
   )

   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track = Track(clips = [video_clip, title_clip])


   #add the track to the timeline
   timeline = Timeline(tracks=[track])


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Adding a Soundtrack

You can add a soundtrack to a video clip by instantiating the `Soundtrack()` class. The class takes the source `src` of the soundtrack as a required argument. Once again, the music file should be hosted online with its link publicly accessible. You may also define the effect and the volume of the soundtrack. Let’s define the soundtrack to fade in and fade out, and then set the volume to 1.


```
from shotstack_sdk.model.soundtrack import Soundtrack   
soundtrack = Soundtrack(
   src    = 'https://shotstackvideo.s3.amazonaws.com/electronic-future-beats-117997.mp3',
   effect = 'fadeInFadeOut',
   volume = 1.0
)
```


To ensure the soundtrack reflects in the rendered video, we’d need to add it to the timeline.


```
timeline = Timeline(tracks=[track],
                      soundtrack=soundtrack)
```


Rendering this produces the video with a soundtrack faded in and out. The rendered video is found [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/240dd0eb-5429-4059-8f12-137953540275.mp4?_ga=2.67780305.1979803603.1664818817-660097783.1663657741&_gl=1*7ah544*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDk4MDUxMi4xNS4xLjE2NjQ5ODA1MTguMC4wLjA.).

The entire code for this example is shown below.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField
from shotstack_sdk.model.transition import Transition
from shotstack_sdk.model.soundtrack import Soundtrack

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #add a soundtrack
   soundtrack = Soundtrack(
   src    = 'https://shotstackvideo.s3.amazonaws.com/electronic-future-beats-117997.mp3',
   effect = 'fadeInFadeOut',
   volume = 1.0
)



   #add a fade in and fade out transition
   transition = Transition(
   _in = 'fade',
   out = 'fade'
   )



   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track = Track(clips = [video_clip, title_clip])


   #add the track and soundtrack to the timeline
   timeline = Timeline(tracks=[track],
                      soundtrack=soundtrack)


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Cropping a Video

To crop video, we use the `Crop()` class. It takes optional arguments: top, bottom, left and right. Each represents the length of the video that would be cropped. Let’s say we wish to crop the video from top and bottom by 0.2 inches and, left and right by 0.05 inches. We can write the following code.


```
from shotstack_sdk.model.crop import Crop

crop = Crop(
 top   = 0.2,
 bottom= 0.2,
 left  = 0.05,
 right = 0.05
)
```


This crop variable has to be passed as an argument in the `VideoAsset()` object we want to crop. 


```
video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition,
       crop = crop
   ) 
```


The entire code can be found here


```
# import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField
from shotstack_sdk.model.transition import Transition
from shotstack_sdk.model.soundtrack import Soundtrack
from shotstack_sdk.model.crop import Crop

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #add a soundtrack
   soundtrack = Soundtrack(
   src    = 'https://shotstackvideo.s3.amazonaws.com/electronic-future-beats-117997.mp3',
   effect = 'fadeInFadeOut',
   volume = 1.0
)



   #add a fade in and fade out transition
   transition = Transition(
   _in = 'fade',
   out = 'fade'
   )


   crop = Crop(
       top   = 0.15,
       bottom= 0.15,
       left  = 0.0,
       right = 0.0
)


   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition,
       crop = crop
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track = Track(clips = [video_clip, title_clip])


   #add the track and soundtrack to the timeline
   timeline = Timeline(tracks=[track],
                      soundtrack=soundtrack)


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Transforming a Video

The `Transformation()` class is used to transform a video. It takes rotate, skew and flip as arguments. These objects must however be created beforehand using the `RotateTransformation()`, `SkewTransformation()`, and `FlipTransformation()` class respectively. For now, we’d only define a rotation of 10 degrees with the `RotateTransformation()` class. You can check these [Python demos](https://github.com/shotstack/python-demos) to learn how to define the other transformations.


```
from shotstack_sdk.model.rotate_transformation import RotateTransformation
rotate = RotateTransformation(
 angle = 10
)
```


Now, we can instantiate the `Transformation()` class and pass the transformation variable.


```
from shotstack_sdk.model.transformation import Transformation
transformation = Transformation(
           rotate  = rotate
       )
```


And then finally, we add the transformation object to the video clip.


```
video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition,
       transform = transformation
   )
```


When you run the code, the output is rotated as expected. The result is shown [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/c53f3638-b62e-471b-bed9-b0fd0d5d54e6.mp4?_ga=2.95499740.1979803603.1664818817-660097783.1663657741&_gl=1*1wed3qb*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDk4NTg1OS4xNy4xLjE2NjQ5ODYxOTguMC4wLjA.).

The entire code for this example is shown below.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField
from shotstack_sdk.model.transition import Transition
from shotstack_sdk.model.soundtrack import Soundtrack
from shotstack_sdk.model.crop import Crop
from shotstack_sdk.model.rotate_transformation import RotateTransformation
from shotstack_sdk.model.transformation         import Transformation

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #add a soundtrack
   soundtrack = Soundtrack(
   src    = 'https://shotstackvideo.s3.amazonaws.com/electronic-future-beats-117997.mp3',
   effect = 'fadeInFadeOut',
   volume = 1.0
)



   #add a fade in and fade out transition
   transition = Transition(
   _in = 'fade',
   out = 'fade'
   )


   #add a rotation angle
   rotate = RotateTransformation(
   angle = 10
)


   #add the rotation to the transformation object
   transformation = Transformation(
           rotate  = rotate
       )

   crop = Crop(
       top   = 0.15,
       bottom= 0.15,
       left  = 0.0,
       right = 0.0
)


   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition,
       transform = transformation
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track = Track(clips = [video_clip, title_clip])


   #add the track and soundtrack to the timeline
   timeline = Timeline(tracks=[track],
                      soundtrack=soundtrack)


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Adding Overlays

Adding overlays means you wish to have more than two tracks with different clips. To achieve this, we would need to create another track and add a clip. Let’s say we want to have the image shown below as a faint background. 


![](https://lh3.googleusercontent.com/jkyf3DDmFTRIQjLMlNESZ7_5yxZcaVUMMpbtWpqrcvVbHmrBKQK0_FG9VzrdGd1i39OWPc4HzsXinkh05bD8WFQJ4TxPO4krDNy9qmrTNYPGKeg2YAq6tuMVfCswdDs_fm-lEXMan90NNea15vbJFtNOFxlDvyNPvZjsDXEvWUAIQQBcjONYvGa7kw)

We’d need to create an `ImageAsset()` and add the image.


```
from shotstack_sdk.model.image_asset import ImageAsset
image_asset = ImageAsset(
   src = "https://shotstackvideo.s3.amazonaws.com/simone-hutsch-rvqkKbx-dVo-unsplash.jpg"
   )
```


Next, we create another clip with this image and pass the image_asset as argument. The opacity will also be set to a low value (say 0.2) since we want the image to faintly appear in the background.


```
image_clip = Clip(
   asset = image_asset,
   start = 1.5,
   length = 20.0,
   opacity = 0.2)
```


We’d then need to create another track and add the image_clip. Let’s call it `track_2` while the existing video and title clip will be `track_1`.


```
#combine both video and title clip
track_1 = Track(clips = [video_clip, title_clip])
track_2 = Track(clips = [image_clip])
```


Finally, we need to add both tracks to the timeline as a list. Note that the order of the tracks matter. Since we want the image as a background while the video plays, the image_clip (in `track_2`) will be the first element of the list. 


```
timeline = Timeline(tracks=[track_2, track_1],
                      soundtrack=soundtrack)
```


Running the code, the rendered video is rotated as expected and is shown [here](https://cdn.shotstack.io/au/stage/kkmmg0oca0/563914fa-0cff-4f5a-91a5-e627abde4899.mp4?_ga=2.67894993.1979803603.1664818817-660097783.1663657741&_gl=1*1aqkvdo*_ga*NjYwMDk3NzgzLjE2NjM2NTc3NDE.*_ga_0KPVTRT370*MTY2NDk4NTg1OS4xNy4xLjE2NjQ5ODc5MTguMC4wLjA.).

The entire code for this example is shown below.


```
#import necessary libraries
import shotstack_sdk as shotstack
from shotstack_sdk.api import edit_api
from shotstack_sdk.model.clip import Clip
from shotstack_sdk.model.track import Track
from shotstack_sdk.model.timeline import Timeline
from shotstack_sdk.model.output import Output
from shotstack_sdk.model.edit import Edit
from shotstack_sdk.model.video_asset import VideoAsset
from shotstack_sdk.model.title_asset import TitleAsset
from shotstack_sdk.model.merge_field import MergeField
from shotstack_sdk.model.transition import Transition
from shotstack_sdk.model.soundtrack import Soundtrack
from shotstack_sdk.model.crop import Crop
from shotstack_sdk.model.rotate_transformation import RotateTransformation
from shotstack_sdk.model.transformation import Transformation
from shotstack_sdk.model.image_asset import ImageAsset

# define shotstack API endpoint
host = "https://api.shotstack.io/stage"

configuration = shotstack.Configuration(host=host)

configuration.api_key["DeveloperKey"] = "insert your API key"

# properly access the resources in the API
with shotstack.ApiClient(configuration) as api_client: #this encapsulate API calls
   api_instance = edit_api.EditApi(api_client) #this is the API call.


   #add the video asset
   video_asset = VideoAsset(
       src = "https://shotstackvideo.s3.amazonaws.com/video.mp4",
       trim = 5.0
   )


   #add the text asset
   title_asset = TitleAsset(
       text  = "Welcome, {{NAME}}",
       style      = 'blockbuster',
       color      = '#ffffff',
       size       = 'large',
       background = '#000000',
       position   = 'center'
   )


   #add the image asset
   image_asset = ImageAsset(
   src = "https://shotstackvideo.s3.amazonaws.com/simone-hutsch-rvqkKbx-dVo-unsplash.jpg"
   )


   #add a soundtrack
   soundtrack = Soundtrack(
   src    = 'https://shotstackvideo.s3.amazonaws.com/electronic-future-beats-117997.mp3',
   effect = 'fadeInFadeOut',
   volume = 1.0
)



   #add a fade in and fade out transition
   transition = Transition(
   _in = 'fade',
   out = 'fade'
   )




   #show the video asset from the 1.5th second to the 20th seconds
   video_clip = Clip(
       asset = video_asset,
       start = 1.5, #the title text shows before this
       length = 20.0,
       transition = transition
   )


   #show the title asset in the first 1.5 seconds
   title_clip = Clip(
       asset = title_asset,
       start = 0.0,
       length = 1.5,
       transition = transition
   )


   #create an image clip
   image_clip = Clip(
   asset = image_asset,
   start = 1.5,
   length = 20.0,
   opacity = 0.2)


   #dynamically add David as the name to display
   merge_field = MergeField(
   find    ='NAME',
   replace   ='David'
   )


   #combine both video and title clip
   track_1 = Track(clips = [video_clip, title_clip])


   #add the image clip to a different track
   track_2 = Track(clips = [image_clip])


   #add the track_1, track_2 and soundtrack to the timeline
   timeline = Timeline(tracks=[track_2, track_1],
                      soundtrack=soundtrack)


   #define the output properties
   output = Output(
       format = "mp4",
       resolution = "sd"
   )


   #define the edit object
   edit = Edit(
       timeline = timeline,
       output = output,
       merge = [merge_field]
   )


   #render the video edit
   try:
       api_response = api_instance.post_render(edit)
       print(f"{api_response.response.message}")
       print(f"{api_response.response.id}")
       print()


   except Exception as e:
       print(f"An error occurred: {e}")


```



## Conclusion

In this tutorial, we have seen how to use Python for video editing. We learned how to perform several video editing operations using the Shotstack API. To learn more about the various arguments in the classes, check out [Shotstack SDK Python](https://github.com/shotstack/shotstack-sdk-python) documentation. 

You can also see [other Python demos](https://github.com/shotstack/python-demos) or check out our other video editing Python articles such as [generating slideshow videos using Python](https://shotstack.io/learn/turn-images-to-slideshow-video-using-python/).
