🎙️ Flipkart UI Engineer interview - My experience

Over the last year, some people have asked me about the UI interview process at Flipkart. I have probably said that I will write back in some time, but never got around to it. Sorry for that! 

Here's a detailed version -
📞  A recruiting agency (Interviewbit) contacted me and setup the onsite. There was no screening round, it was directly onsite. About 10-12 candidates were invited to apply for the same role, on a weekend. 
(Flipkart loves this group interview thing through agencies, and that is your best shot at getting to the door. Most of the colleagues i have heard from also got in through similar route. Its sad, but referral is almost useless here and gets ignored most of the time.) 

📅  The interview was mostly a one-day process - from morning to late evening. First, they asked us to gather in a meeting room and explained the machine coding task (1st round). Next rounds are ps/ds (problem solving + data structure), UI round and finally, manager round. 

💻 Machine coding - This is a UI you have to build using plain html/css/js - no ui library like react or vue is allowed. They will give you a sheet with all the details - reqd and good to have features, api endpoints, mockup. Complete atleast some of the reqd features. But focus more on clean coding, best practices and logic, rather than on the ui look. You'll have 1-2 hour to complete this task. But don't worry - it's not supposed to be complete-able in that timeframe. I was able to finish only half of the required features . So, prioritize the imp tasks and explain how you'll do the rest during review. 
Think beforehand how you want to build using plain js, keeping it modular and everything. Have a scaffolding with import/export support ready (webpack).

Example task - Build a todo app. You can add, edit, remove items. Allow sub-tasks within a task. There will be some form validation or api call. Also, persist/load todo items on reload. 

☯️ Ps/ds - There was 1 problem solving question (leetcode easy problem - search from both sides, basic dynamic programming, etc) and 1 data structure question (ex. tree traversal in various orders, stack, etc) . It took about 40-45min. You'll have to first explain your approach and then write the code on paper. 
This is usually not so hard for ui roles. But in some cases, interviewer might ask full-blown sde level questions like red-black tree, etc. There is officially no distinction for psds round between ui and sde roles, but practically there is.

🔶 UI+Javascript - I felt this was the most imp round. It went relatively smooth for me and lasted for about an hour. Expect moderate advanced Javascript questions and some browser related questions. Most of these are standard questions. Some of the questions were asked indirectly through a UI scenario. 

Ex - debounce + throttling (very common nowadays), event handling with delegation, event capturing phase, scope, let/var, promise + promise.all, convert setTimeout to promise, async, how does a page load from start, how to make animations smooth, etc.

Manager round - Depends. Some HR questions (why you want to move / join), mostly tech questions.
For me it was - how authentication, cookies, what all cookie contains, react new version, how do you learn, etc
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM3OTA0ODY4OSwtOTcwODAyMTU0LDE2ND
A0MzI1ODRdfQ==
-->