Tuesday, April 10th 2018

Greetings and welcome to the first post of possibly many posts around things that the Quartermaster development team has screwed up and gotten wrong since we kicked off our project sometime around Februrary of 2018. This one is about how we deleted all of our environment variables in production and broke our application right before myself and Lee left to get noodles (they were delicious). 

# What went wrong

Our task was to implement a blue-green deployment strategy (INSERT LINK) for our application living in PCF. We had a fairly robust, single environment deployment process that ran smoke tests and gave us fairly high confidence that we didn't break anything.  

We were investigating a plugin (LINK GOES HERE ) that would make our lives easier and prevent us from having to script out all of the individual steps that go into a blue-green deploy. Luckily for us, the plugin worked with a minimal amount of setup and we were pretty pleased with how it deployed our app, so after a quick glance at our UI we thought "Hey it works, let's grab lunch". 

What we didn't realize is that this plugin didn't seem to care too much about our existing environment variables in PCF (such as config settings, application ids, etc.) and it just pushed and started our app without any of them. Bummer. 

While staring into a large, steaming bowl of Pho, I got a message in Slack -- 'Hello the Login into GitHub is not currently working on our prod site'. This was just one of the things affected, but our app Quartermaste (LINK HERE PLZ) will allow you to create a Spring Boot App and will push it to Ford's GitHub for you, except that it won't if you don't have our environment variables and you'll be hit with a lovely 404 from GitHub. (IMAGE GOES HERE) Essentially, prod is broken.


# What we did to prevent this from happening again

Well, the first thing we did was put those environment variables back. Our `blue` app had them still so all we had to do was a few quick copy and pastes to get the application into a good state.  

We ended up taking and modifying the plugin we mentioned above and adding to it (LINK TO THE PAST) the ability to persist user-provided environment variables when doing blue-green deploys. This solved the problem for us and gave us a nice way to test-drive out the functionality that we needed. 

We tossed around the idea of using Spring Cloud Config Server (LINK), but we wanted something that we could bundle up and offer to others through Quartermaster (LONK). 

# What would have prevented this

I guess we could have caught this a number of ways. Our smoke test just hits our endpoints and follows a happy path through the app, but it's not responsible for testing the end-to-end flow. We could've had a more robust test suite in place, maybe one that clicked through the UI and played the role of a good user. We also could have been more diligent in our manual testing, like maybe, you know, actually clicking a button instead of just looking at the screen saying "Yup, looks good". Maybe we would've done that had we not been so hungry.

# Outcomes

It's never fun to take one on the chin, and this was definitely an item to retro on and talk about as a team how we can not do this again. One of the things that a team needs to be successful is a strong sense of trust, and breaking prod right before lunch does not help to add to it. But luckily we caught the error fast enough thanks to some helpful users, and our new plugin has some great and thoughtful tests that give us confidence that it was a one-time growing pain.

So, if you're interested in adding blue-green deploys to your app, Quartermaster now offers that option. It's basically one line, and does all of the blue-green stuff that you would expect. 

Thanks for reading.   