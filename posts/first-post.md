“Don’t worry, you’ll figure it out in time”.  
“It’s just one of those things you figure out how to do.”  
“Give it a few years and you’ll have it figured out”.

It’s just one of those things that we live with. The constant cloud that hangs over us and confronts us at our lowest, worst of times -- “I’m the only one here who doesn’t know what they’re doing.”, “She’s way smarter than me, what am I doing here?”.  
  
Imposter syndrome, feeling like you don’t belong and that you’re a fraud. While not exclusive to, it’s a feeling that’s prevalent in the software industry. That new hire is a whiz when it comes to understanding our testing framework, or the person who has been there just as long as you always seems to be picking up context a bit quicker than you.  

News flash, we’re all thinking the same thing, we all go through it and none of us are talking about it. I’ve been there and that I feel that way often. 

I won’t go into what causes it, and I won’t go into how to overcome it, but it’s something that’s been covered at length by others and I’d recommend checking out the blogs linked below. But what I would like to share is the beginning of a semi-regular series that documents the things that I’ve screwed up since working on my project, Quartermaster, as an engineer working in FordLabs.  

My hope is to humanize the efforts and blunders that all of us make while doing our best to deliver a great product. Maybe the mistakes we’ve made will seem obvious, perhaps they’ll be something that prevents you from making them in the future. Regardless, I believe that we’re always trying to do what seems right in the given situation -- but hopefully by sharing my honest experiences working in development, you can all feel a little better knowing you didn't screw stuff up as badly as I have.

https://medium.com/learn-love-code/developers-how-to-overcome-imposter-syndrome-48edee803cf4
https://www.laserfiche.com/simplicity/shut-up-imposter-syndrome-i-can-too-program/

POSTS: 

- Try to bring the tone up a little bit, "I want to talk about my mistakes...", "Here's why this will help you", 



Tuesday, April 10th 2018

Today we deleted all of our environment variables in production and broke our application right before myself and Lee left to get noodles (they were delicious). 

# What went wrong

Our task was to implement a [blue-green deployment strategy](https://docs.cloudfoundry.org/devguide/deploy-apps/blue-green.html) for our application living in PCF. We had a fairly robust, single environment deployment process that ran smoke tests and gave us fairly high confidence that we didn't break anything.  

We were investigating a [plugin](https://github.com/pivotalservices/ya-cf-app-gradle-plugin) that would make our lives easier and prevent us from having to script out all of the individual steps that go into a blue-green deploy. Luckily for us, the plugin worked with a minimal amount of setup and we were pretty pleased with how it deployed our app, so after a quick glance at our UI we thought "Hey it works, let's grab lunch". 

What we didn't realize is that this plugin didn't seem to care too much about our existing environment variables in PCF (such as config settings, application ids, etc.) and it just pushed and started our app without any of them. Bummer. 

While staring into a large, steaming bowl of Pho, I got a message in Slack -- 'Hello the Login into GitHub is not currently working on our prod site'. This was just one of the things affected, but our app [Quartermaster](https://quartermaster.apps-pcf02i.cf.ford.com/) will allow you to create a Spring Boot App and will push it to Ford's GitHub for you, except that it won't if you don't have our environment variables and you'll be hit with a lovely 404 from GitHub. [!](IMAGE GOES HERE) Essentially, prod is broken.


# How we fixed it

Well, the first thing we did was put those environment variables back. Our `blue` app had them still so all we had to do was a few quick copy and pastes to get the application into a good state.  

We ended up taking and modifying the plugin we mentioned above and [adding to it](https://github.ford.com/Quartermaster/ya-cf-app-gradle-plugin) the ability to persist user-provided environment variables when doing blue-green deploys. This solved the problem for us and gave us a nice way to test-drive out the functionality that we needed. 

We tossed around the idea of using [Spring Cloud Config Server](https://github.com/spring-cloud/spring-cloud-config), but we wanted something that we could bundle up and offer to others through [Quartermaster](https://quartermaster.apps-pcf02i.cf.ford.com/). 

# What would have prevented this

I guess we could have caught this a number of ways. Our smoke test just hits our endpoints and follows a happy path through the app, but it's not responsible for testing the end-to-end flow. We could've had a more robust test suite in place, maybe one that clicked through the UI and played the role of a good user. We also could have been more diligent in our manual testing, like maybe, you know, actually clicking a button instead of just looking at the screen saying "Yup, looks good". Maybe we would've done that had we not been so hungry.

# Outcomes

It's never fun to take one on the chin, and this was definitely an item to retro on and talk about as a team how we can not do this again. One of the things that a team needs to be successful is a strong sense of trust, and breaking prod right before lunch does not help to add to it. But luckily we caught the error fast enough thanks to some helpful users, and our new plugin has some great and thoughtful tests that give us confidence that it was a one-time growing pain.

So, if you're interested in adding blue-green deploys to your app, [Quartermaster](https://quartermaster.apps-pcf02i.cf.ford.com/) now offers that option. It's basically one line, and does all of the blue-green stuff that you would expect. 

Thanks for reading.   

- Personalize last sentence in 'what went wrong'
- So what happened to us didn't happen to you
- "At least we didn't take half of AWS down"