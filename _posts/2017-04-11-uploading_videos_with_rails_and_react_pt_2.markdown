---
layout: post
title:  "Uploading Videos with Rails and React pt. 2"
date:   2017-04-11 17:50:56 +0000
---


Intro
In my last [post](http://ramoswebdev.com/2017/04/07/uploading_videos_with_rails_and_react_pt_1/) I walked through the process of handling video uploads through your Rails API. However, you still need a way to send the video from the client-side using, React, so that's what I'll be covering in this post. This can be a little tricky if you've never uploaded attachments through React, since you might be used to sending post request as JSON objects to your server. There are a few things we have to do differently, since we're sending a multi-part request, but once this is clear, it should be pretty straightforward. Throughout the post I'll be refering to the video [platform](https://github.com/johnram528) I recently built using React and Rails. 
State
In my project I had 3 main components: VideosPage, VideosShow, and VideosNew. I won't be discussing the first two components, the component that deals with video upload logic is VideosNew. If you can get your videos to upload to the API, then your other components should easy. In my project I'm using Redux, but the VideosNew component is the only place where I'm directly setting state. 

```
class VideosNew extends Component {
  constructor(props) {
    super(props)
    this.state = {
    
    }
  }
```

The reason I did this is to capture the inputs from the form you're about to see. We'll need to capture the state of those input changes so we can create a new object. Next you'll need to set up your form:

```
render() {
    console.log(this.state)
    return (
      <div className='form'>
      <form id="form" onSubmit={(event) => this.handleOnClick(event)}>
        <div className='input-group'>
          <input className='add-video-field'
          type="text" 
          name="title"
          placeholder='title'  
          onChange={(event) => this.handleInputChange(event)}/>
        </div>

        <div className='input-group'>
          <input className='add-video-field'
          type="text" 
          name="description"
          placeholder='description' 
          size='70'
          onChange={(event) => this.handleInputChange(event)}/>
        </div>
                
        <div className='input-group'>
          <input className='add-video-field'
          type="file" 
          name="file" 
          accept="video/mp4,video/x-m4v,video/*"
          onChange={(event) => this.handleOnAttach(event)}/>
        </div>

        <div className='input-group'>
          <input className='add-video-button'
          type="submit" 
          name="Upload Video" 
          />
        </div>
      </form>
      </div>
      )
  }
}
```

I'll go into the onChange functions below, but the only other thing to notice in the above code is that you can specify which file type you want to accept as an input. We've already set up which file types our database is going to accept, but this just adds an extra layer of protection and also makes the user experience better, since only video files will be highlighted. 

handleOnChange sets input changes on the title and description as state: 

```
  handleInputChange(event) {
    const { value, name } = event.target
    this.setState({
      [name]: value,
    })
  }
	
```
Very simple, but that won't work for the file you want to attach. Here is how I handled that: 

```
  handleOnAttach(event) { 
    const input = document.querySelector('input[type="file"]')
    this.setState({
      file: input.files[0],
    })
  }
```
Using querySelector like this gives you the actual file, and notice how I specified an index, because it's giving me all files. In my case I only have 1 file, but this could be very useful if you want to upload multiple files at a time. 

Our final function is the handleOnClick event which fires when the form is submitted. This is where we grab the entire state and use the data to create a new formObject. 

```
  handleOnClick(event) {
    event.preventDefault()
    const data = new FormData()
    data.append('video[title]', this.state.title)
    data.append('video[description]', this.state.description)
    data.append('video[file]', this.state.file)
    data.append('video[user_id]', this.state.user_id)

    this.props.actions.addVideo(data).then(() => {
      this.props.router.push('/videos')
    })


  }
```
To create a form data object we say const data = new FormData(). To add info to the new object you're going to append it. The first argument is the key, and the second is the value. The key you choose is VERY important. This part had me stuck for a while, because I was sending my formData object to the server, but the server wasn't accepting it. This was because of the strong params in my rails controller. 

```
   def video_params
      params.require(:video).permit(:file, :title, :description, :user_id, :likes)
    end
```

Since I'm requiring video, I need to write 'video[title]'. 

Once you have this set up you just need to create your addVideo() action:

```
export function addVideo(formData) {
  return function(dispatch) {
    return fetch('/api/videos', {
      method: 'POST',
      body: formData
    })
      .then(response => response.json)
      .then(video => { dispatch({type: 'ADD_VIDEO', video})
      })
  }
}
```

If you get stuck on anything feel free to take a look a my git repo, listed at the beginning of this post. 

