Thank you for the interview, my colleagues were positive about the conversation. Therefore we would like to share our technical case with you:

Jamie's vacation is about to end. She works at an international company, so she can choose another office to work from: either Amsterdam, Madrid, or Budapest. Help her choose which office to go to – she’d like someplace with good weather or cheap flights (or both).

If you'd like to use an API (it's optional), you could try the AccuWeather API ( https://developer.accuweather.com ), the Kiwi API ( https://docs.kiwi.com ), or another of your choosing.

You have full control over how you want to present your data, but keep in mind this is a client-side application and Jamie needs to have enough information to make her choice. Make sure to publish your result on GitHub and provide an online demo. You can pick a framework, and the browser compatibility is up to you. We really value creativity, and we encourage you to create your own requirements while working on this assignment. It would be great if you could finish this within one week.

We recommend that you do not spend more than 4 hours on the assignment, and it is obviously not possible to implement all aspects of a production-ready application in this timeframe. This is why we suggest to focus on delivering a functional solution, while showcasing your strengths within the time indication.

Some of example focus areas are:

Technical excellence
Jaw-dropping UX
Extremely secure app
Framework guru (Vue, React or other)
We also encourage you to document your choices and possible improvement areas, in order to give us an insight in your line of thought, and help us evaluate your assignment accordingly.

-------------

Thankyou for offering me this technical challenge, I really enjoyed the task, below are the links to view the project and a report on Google Docs detailing my decisions. I look forward to discussing the project in more detail.

Live test URL
http://adyentest.com.s216490.gridserver.com/

Github URL
https://github.com/fangaas/adyen-technical-test

Report document
https://docs.google.com/document/d/1l2N90KFQZdHrECpEsdKah3VJcnBr3mjY27MBVs2K8ks/edit?usp=sharing



My chosen solution

The problem given was quite similar to what I went through when I moved to Amsterdam, I also researched moving around to multiple different cities. And when I first moved here, I flew home 6 times in the first year.

As such I thought something useful for Jamie would be a web app to display a general overview of the flight prices for returning home to visit, and flying back to work again, for the upcoming year.

To simplify things I assumed Jamie lived in London and only allowed dates grouped by month for the upcoming year to be selected. I also limited the results returned to the top 5 cheapest flights, and did not permit any other flight options than the month the flight is in, which destination, and which direction.


What I would show with my development

- Displaying my proficiency with the Vue framework as a core focus
- Use minimal, clean CSS with Stylus
- Use linted, clean ES6 JS code
- Creating a user friendly, modern and concise UI


Extras I wanted to show if possible

- Animations using GSAP timelines
- Use of some extra libraries where relevant


What I left out

- Extensive browser compatibility
- Responsive design (though flex would be still be used throughout)
- Developing with a view to expanding the UI 


What I used for my solution

- Latest Vue CLI to create the SPA
- ESLint for code quality and consistency
- Stylus for CSS preprocessing 
- Moment JS for date time parsing
- Axios for HTTP requests
- Greensock/GSAP for the flight list animation

Developed on Windows 10 using Chrome

Some difficulties I overcame

- The sample Kiwi API requests didn't seem to work so I worked back from the requests being put out from their testing tool in the Tequila API UI
- Had too many ideas as to what to do for this assignment initially
- Took plenty of time to visualise the UI before any construction took place

In total the core functionality of the app took around 4 hours to put together, this work is displayed in the 'Complete core app functionality' commit. I was really enjoying the project and wanted it to look complete and pretty, so took extra time to add new features, do some tidying and write some notes.

I discovered unfortunately later that the data being returned is actually not too useful, as the flight prices are all generally the same a few months in advance, so it is hard to get a view on what the flight prices are like throughout the year if you were to book one or two months before. 

Were I to redo this project with unlimited time, I would display historical flight data to and from the locations for the previous year in a graph.