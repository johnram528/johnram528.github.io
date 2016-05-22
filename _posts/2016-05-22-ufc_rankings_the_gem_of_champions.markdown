---
layout: post
title:  "UFC Rankings. The gem of champions."
date:   2016-05-22 01:48:32 +0000
---



In the past several months I've gone from never watching a UFC fight, to becomming an obssesed fan. I've watched the last 3 pay per view events and have been closely following the fighter rankings. So, when it came time to create my first gem I immediately knew I wanted to build something that worked with scraped data from the UFC's [website](http://www.ufc.com/). My new obsession, and newly acquired scraping skills resulted in the [ufc_rankings ](https://rubygems.org/gems/ufc_rankings) gem. This project jas been a blast to work on and an amazing learning experience. I've been learning and becomming a better coder everyday, but this was the first time I used my newly acquired skills in such a creative way. 


Getting started was the hardest part of this project for me. I'm used to working on labs where I worked on existing repositories to make code work, so starting something from scratch was simaltaneously scary and exciting. Thankfully, Avi posted a link to an extremely helpful video [walkthrough](https://www.youtube.com/watch?v=_lDExWIhYKI) on creating a gem. I would've saved a few hours had I watched that video earlier. The two biggest things I learned from Avi's video were: 

* ** Building a gem with bundler **- I think the best advice to anyone creating their first gem is to [create it with bundler](http://railscasts.com/episodes/245-new-gem-with-bundler). It's not that the guide on rubygems.org is difficult to follow, but it can, and in my opinion should be replaced by 7 simple bash commands. Although, it never hurts to understand how something works.
* **Working Backwards**- The first point was what I think is the best advice on creating gem's, but this next point is what has been the most meaningful thing I've learned in my short time coding - Work Backwards. Before watching the Daily Deal video, I started by creating all the folders and files I thought I'd need from scratch. Once I had my folders set up I decided that the first place I'd start with my code was in my scraper class. I would then build my other classes, cli and finally my executable. My quick frustration and lack of a sense of direction is what led me to the Avi's video, and his method of keeping things simple. Avi advocated started with the one line of code in the executable, and using pseudocode to make it work. Once the code works, you can replace the psuedocode with your actual code. This singlehandedly changed my energy on this project, and the work was so much fun. 

**So, how does ufc_rankings work?**
First, it scrapes the UFC site for rankings in each weight class, as well as their pound for pound rankings, storing the fighters in an array for each weight class. Each weight class has a method uses the data stored in that weight class array to create a ranking list for that class. The CLI shows the user each weightclass and prompts them to select the weight class they'd like to see rankings of. The user input is what determines what weight class method to activate. I decided to go with a case method for this: 
```
  def show_rankings(num)
    case num
    when 1
      puts "Pound for Pound Rankings"
      UfcRankings::Rankings.p4p
    when 2
      puts "Flyweight Rankings"
      UfcRankings::Rankings.fly
```

![ufc_rankings demo](<iframe width="420" height="315" src="https://www.youtube.com/embed/9OXF5uL5I2E" frameborder="0" allowfullscreen></iframe>)
If you're a fan of the UFC you might want to check out my gem.

