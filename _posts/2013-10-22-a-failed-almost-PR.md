---
layout: post
category : lessons
tags : 
---
{% include JB/setup %}

### A failed almost PR

>So, I came across a scaffold generator for Ember called \(surprise!\) generator-ember. --Its just the convention for generators--. [generator-ember](https://github.com/yeoman/generator-ember)

>This generator is written using Yeoman and helper methods and when you say "yo ember" inside your brand new app directory, a whole ember application is created for you saving you so much of boiler plate. And believe me setting up an ember application from scratch is slightly cumbersome. So it's a welcome tool from my point of view.
>But here was the awesome part. Addy Osmani, one of the founders of this tool and of course, the uber famous JS icon, had raised an issue request for applications for maintainers for this project. Sounded like an opportunity. Somebody had already applied and Addy had suggested he look for potential solutions to the issues already raised and give his 2 cents on them. This sounded like a reasonable test to me. So I decided to test myself before applying.

>I was right to decide what I decided because I did not know shit about Yeoman or the generator. So I dutifully forked the project, cloned it took it home. That night I fired up sublime with my generator ember clone and started going through the code. It was just node code and I had some confidence.

>The structure seemed straight forward. For each of the generator the folder structure was 
* name of hook DIR  
	* template DIR
	* index.js

>index.js had all the code required to generate the structure described by the hook. I could see it used api from Yeoman's base to copy the files to the new folders in a structured manner, fill in the templates using Lo-dash and put those in to the respective folders too. I checked this file out for some time and then opened the templates folder.
templates folder had the whole structure of the hook that had to be mapped to the new folder as per the index.js code. Ok, so this gave me some context. It had the Gruntfile, package.json, bower.json, gitignore and all the other files that any app worth its js salt should have.

>Now I went to the yeoman generator tutorial at [Yeoman-generator](http://yeoman.io/generators.html)
>The page is very well done and the instructions were very clear as to how to create your own generator. These had been applied well into the generator-ember and I could see the corresponding similarities of structure and understand the yeoman base methods better through the tutorial. 

>I deemed myself ready to open the issues page and see if I can tackle one of them and send in a PR.
>So, I picked [Issue110](https://github.com/yeoman/generator-ember/issues/110). This was more of a gruntfile change than anything to do with yeoman. All I had to do was modify the grunt task of usemin to pick ember.prod.js while creating the concat subtask and replace the template in the app folder.
>I found grunt-string-replace and used it to replace instances of ember.js with ember.prod.js in a temporary index file. I then pointed useminPrepare to this temp index file and use that in the concat method it was creating. I would then later clean up this file. I tested it on the app, it worked like a charm. The addition of these replacement tasks looked a tad immature but then I anyway added it to the Gruntfile template, generated a new ember app and built it successfully with the prod version and push it to my fork.

>Now I want to create a pull request. In the list of pull requests, I find [PR-95](https://github.com/yeoman/generator-ember/pull/95) and my heart sinks. It's been already solved. In a much better way. This guy replaced the prod files for ember and ember data in the scripts array even before they were added to the html file. Good job and much cleaner. So I give up for the day and write this.