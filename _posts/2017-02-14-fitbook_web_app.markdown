---
layout: post
title:  "Fitbook Web App"
date:   2017-02-14 22:20:58 +0000
---



I'm very pleased to have just finished my rails portfolio project. Choosing an app idea wasn't easy, since I wanted to come up with an original idea rather than use one of the suggested ideas. I find that when I'm working on an original idea I'm much more inspired, which makes for much better coding sessions. After sleeping on it I eventually decided to build a fitness app where users can create edit and track workout routines, while also updating their health status (BMI). Building a project from scratch is a lot of fun, but also makes you realize why you need a team of developers. Even though my main focus was meeting the requirements for the project, I couldn't help but think of new features for the app that I wanted to add, but I had to stop myself before it got out of hand. What I ended up was a nice simple site that directs you to edit your height and weight upon signing up. Once you enter this info you will be redirected to the user show page which displays your BMI and a automated message depending on the BMI number. From there you can start creating workout routines.
A routine has many exercises and exercises has many routines. I could've made exercises belong_to routines, but this would limit what you could do with the data going forward. One thing I would do if I choose to keep working on this app is allow users to add other users workouts or individual exercises to their own routines. 

Click [here](https://youtu.be/HSgN9vme1co) for a video walkthrough of the app. 


As always with these portolio projects, I couldn't have anticipated how much I was going to learn. Here are just a few of the cool things I've learn from this project. 

Facebook owns all books - When it came to implementing omniauth into the app I decided to with facebook, since I have experience with the developer site and like the flow. However I was in for a surprise when facebook wouldn't let me use Fitbook as the name for my app on their site. Apparently any app with book attached to it's name can be seen as a potential threat to the brand. 

Nice clean way to add amounts - In order to display the amount of time you should expect a workout to last, I had to come up with a clean way to add the estimated rep times of each exercises associated with a routine. Not a difficult task at all, but I did discover a nice clean solution I wasn't aware of. The solution is actually straight forward, but what I like about it is the syntax. Here is my routine.estimated_time method:

exercises.map(&:estimated_time).inject(45, :+) unless exercises.empty? 

The first part makes use of Symbol#to_proc. When Ruby sees & next to any object it turns it into a block. 
I then use inject to add all the times and 45 in between each time. This is perfect for what I was doing, since I wanted a way to account for breaks between exercises. 

Another thing I learned, which I probably had read about before but clearly slipped my mind was that in order to diplay validation errors you need to render the page instead of redirecting. 


