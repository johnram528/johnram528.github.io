---
layout: post
title:  "Fitbook App and Serialization"
date:   2017-03-09 20:07:27 +0000
---


Intro

In my last post I talked about the fitness app I built on rails. This post will discuss a few things I discovered while adding Jquery and AJAX to my existing fitness app. This project is another example of something I visualized being very simple, turning into a very frustrating experience at times, but ultimately a great learning experience. I really thought I could add all the Jquery and AJAX in an hour or two, but it ended up taking two days, with a lot of long walks and meditation sessions(curling up in a ball on my couch) in between. However, as always, I'm grateful for the challenge and walked away with some cool new tricks, one of which I'll be discussing below. 

Active Model Serializer

It hit me when working on this project that the way I'll be using Rails going forward will be much different than what I've been used to for the last few months. In theory it made perfect sense to use Rails as an API server and Javascript for the client side, but it in practice this meant do a few interesting things to be able to produce all the data I wanted to be available for Javascript. After serializing my routine model and creating a JSON for the model it was clear that I was missing key data need to recreate the views I previously rendered with Rails. The serializer had access to all my database attributes, but what about the convenience methods I created? A key part of my views is showing the estimated time it takes to complete each routine. This is displayed using a simple helper method - show_estimated_time

```
module ApplicationHelper
  def show_estimated_time(model)
    if model.estimated_time > 60
      "#{model.estimated_time/60} min"
    else
      "#{model.estimated_time} sec"
    end
  end
end
```

My issue when working with my original JSON was that Javascript doesn't have access to this helper, and I could calculate it again, but I don't want to do that with Javascript especially if it already exits in the Rails part of my app. Fortunately active model serializer gives you an easy way to serialize custom attributes.

```
class RoutineSerializer < ActiveModel::Serializer
  include ApplicationHelper

  attributes :id, :name, :estimated_time, :muscles
  has_many :exercises
  belongs_to :user

  def estimated_time
    show_estimated_time(object)
  end

  def muscles
    object.muscles
  end
end
```

What I did here is create a estimated_time method, which simply uses the show_estimated_time helper on the object. The object of course is the new Routine, but a key thing to take note of here is how I included ApplicationHelper above. Without this the RoutineSerializer class won't have access to the method. This allows for a lot of flexibility and produces some very powerful JSON. 

I used a similar method for my user model. I wanted to have access to the url for each user who created a routine, but url is not an active record attribute. 

```
class UserSerializer < ActiveModel::Serializer
  include Rails.application.routes.url_helpers
  attributes :id, :email, :url

  def url
    user_routines_path(object)
  end
end
```

Here I included Rails.application.routes.url_helpers, giving me access to all my applications routes!



