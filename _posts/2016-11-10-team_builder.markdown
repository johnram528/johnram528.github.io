---
layout: post
title:  "Team Builder"
date:   2016-11-10 02:27:36 +0000
---

I just finished up my second Learn portfolio project - [Team Builder](http://https://www.youtube.com/watch?v=Keas_dtuEns&t=6s). It's amazing how being in control of every aspect of a project from start to finish can test you, and in my case it helps to bring out my creative side a little more. While coding labs are a great learning tool, I feel like I focus a little more on getting the tests to pass. This project definetely brought out the perfectionist in me and made me focus on the smaller details more than I usually would. I built Team Builder with little leaugue baseball coaches in mind. It's no secret that kids who play in little league usually idolize their favorite professional players and frequent their player profiles online. I think it'd be cool for kids to be able to see and even edit features of their own profiles online. I think I will go back to this project in the future and build it out more. Currently, coaches are the only user group, but I would like players to be users as well, so they could add a bio and follow other players. I'd also add stats, but that would become robust very quick. The site currently allows anyone to look at all of the teams (including the roster) in the system. A coach can login and edit their team, which is created when they coach first signs up. 

### Models
My 3 models are:

Coach - A Coach has name, user_name, password and team_id attributes. Coach belongs_to a Team, and has_many Players through a Team.

Team - A Team has name, city, mascot, and field attributes. Team is the parent and has_one Coach, and has_many Players.

Player - A Player has a name, position, age, depth and team_id attributes. Player belongs to Team. 

I added the depth table to Players so I could have an easy way to differentiate the starters from the bench players when displaying a team's roster. 

### New Tools

I noticed on the second day of working on the site that pages were noticiablly slower to load. I planned on continuing to add features and figured (hoped,really) pages would load faster once I cleaned my code up in the final stages. Luckily for me, in the middle of working on this project a received a code review for another sinatra app and my Instructer gave a great pointer, telling me I should use memoization (conditionally setting an instance variable OR returning it) for my current_user helper method. That method went from 

User.find(session[:id) if session[:id]
      to
@current_user ||= User.find(session[:id) if session[:id]


Now current_user is cached and only hits the database once. I was relieved to see that this immediately improved my load times. 

ActiveRecord Validations - Another great tip I picked up from that code review which were conveniently applied to Team Builder was using built in validations that AR provides instead of the ones I was creating in my controller. Now all of my models validate the presence of each of its attributes, making it impossible for new objects to be created with a blank form field. I also used validates_uniqueness_of :user_name for Coach.

Please check out the Team Builder app and maybe ever start your own team!
