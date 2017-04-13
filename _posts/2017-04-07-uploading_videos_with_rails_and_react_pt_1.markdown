---
layout: post
title:  "Uploading Videos with Rails and React pt. 1"
date:   2017-04-07 20:45:22 +0000
---


I just finished the building the first version of a video [platform](https://github.com/johnram528/tavew-video-platform) built with Rails as the API server and React on the client side. You can take a look at the code here, and a video walkthrough of the app here. This was my first time working with video processing and uploads, so I wasn't sure what to expect, but was surpised to discover that setting up upload processing on the server side would be much easier than setting up uploads from the client side. Still, there are a few key things you need to do in order to get this set up properly and there aren't that many resources online documenting the necessary steps, so I'm hoping this article will be usefull to anyone wanting to upload videos to their app. This is going to be a two part series, with the first part focusing on the server side implementation and the second part focusing on uploading from the client side. 

# Gems
I've worked with paperclip in the past to upload image files, so I figured it could work with videos. After doing some digging I discovered that uploading videos with Paperclip is very similar to image uploads, but you will also need two additional gems: paperclip-av-transcoder, and delayed-paperclip. Installation is very straightforward: 

```
Gemfile

gem 'paperclip'
gem 'paperclip-av-transcoder'
gem 'delayed_paperclip'
```

The next step is to run bundle install and set up your model that will be handling the uploads. In my case it was a Video model that requires a file attachment for validation. It's very easy to set your model up using the paperclip migration generator:

`rails generate paperclip video file`

My video migration file looks like this:

```
class CreateVideos < ActiveRecord::Migration[5.0]
  def change
    create_table :videos do |t|
      t.attachment :file
      t.string :title
      t.text :description
      t.decimal :length

      t.timestamps
    end
  end
end
```

and my Video model looks like this:
```
class Video < ApplicationRecord



  has_attached_file :file, :styles => {
    :mp4 => {
      :format => 'mp4',
      :geometry => "1200x700#",
      :convert_options => {
        :input => {},
        :output => {
          :vcodec => 'libx264',
          :movflags => '+faststart',
          :strict => :experimental
        }
      }
    },
    :webm => {
      :format => 'webm',
      :geometry => "1200x700#",
      :convert_options => {
        :input => {},
        :output => {
          :vcodec => 'libvpx',
          :acodec => 'libvorbis',
          'cpu-used' => -10,
          :deadline => :realtime,
          :strict => :experimental
        }
      }
    },



    :preview => {
      :format => :jpg,
      :geometry => "1200x700#",
      :convert_options => {
        :output => {
          :vframes => 1,
          :s => "1200x675",
          :ss => '00:00:02'
        }
      }
    },
    :thumb => {
      :format => :jpg,
      :geometry => "300x169#",
      :convert_options => {
        :output => {
          :vframes => 1,
          :s => '300x169',
          :ss => '00:00:02'
        }
      }
    },
  },
  :processors => [:transcoder]

validates_attachment_size :file, less_than: 1.gigabytes
validates_attachment_content_type :file, :content_type => ["video/mp4", "video/quicktime", "video/x-flv", "video/x-msvideo", "video/x-ms-wmv", "video/webm"]
validates_presence_of :title, :description, :file
process_in_background :file
end
```

This might look like a lot, but it's not as scary as it looks and I'll break down the key parts:

The first thing to note is the :has_attached_file macro. This tells paperclip where to find the attachment. The second part of this is the style method, which is provided by paperclip-av-transcoder. Basically what we're doing here is giving the expected media type and telling giving telling the transcoder what kind of output you want. Feel free to play around with these settings to accomplish you're desired results. In my case, I had to chang the preview section, which sets the preview image, or thumbnail that people will see before starting you're video. 

I originally had it setup like this: 

```
:preview => {
      :format => :jpg,
      :geometry => "1200x675#",
      :convert_options => {
        :output => {
          :vframes => 1,
          :s => "1200x675",
          :ss => '00:00:02'
        }
      }
    },
```
but the transcoder was sending back an error about the geometry of the previw not being divisible by 2. This was pretty strange, but I just change it to `"1200x700` and it ran smoothly after that. 

The `process_in_background :file` macro at the bottom of the page is provided by delayed-paperclip. Processing videos is a slow operations, so you'll want this done in the background. You can also process the preview image files in the background, but this isn't something I had an issue with. However, it is something you want to keep in mind as you work with larger files. 

Once you have this set up, the rest is pretty straightforward in terms of getting the server side up and running. The next steps would be to set up the create action in your controller and test out creating new videos from your console. 





