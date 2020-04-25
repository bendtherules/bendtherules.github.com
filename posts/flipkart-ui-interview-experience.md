🎙️ Flipkart UI Engineer 1 interview - My experience

Over the last year, some people have asked me about the UI interview process at Flipkart. I have probably said that I will write back in some time, but never got around to it. Sorry for that! 

Here's a detailed version -

📞  A recruiting agency (Interviewbit) contacted me for UI Engineer 1 opening in Flipkart and setup the onsite. There was no screening round, it was directly onsite. About 10-12 candidates were invited for the same role, on a weekend. 
(Flipkart loves this group interview format, where agencies shortlist a few candidates and send them to Flipkart for interview. This is your best shot at getting to the door. Its sad, but referral is almost useless here and gets ignored most of the time.) 

📅  The interview was mostly a one-day process - from morning to late evening. First, they asked us to gather in a meeting room and explained the machine coding task (1st round). Next rounds are ps/ds (problem solving + data structure), UI round and finally, manager round. There is probably one more system design round for UI 2 and above. 

💻 Machine coding - This is a UI you have to build using plain html/css/js - no ui library like react or vue is allowed. They will give you a sheet with all the details - reqd and good to have features, api endpoints, mockup. Complete atleast some of the reqd features. But focus more on clean coding, best practices and logic, rather than on the ui look. You'll have 1-2 hour to complete this task. But don't worry - it's not supposed to be complete-able in that timeframe. I was able to finish only half of the required features . So, prioritize the imp tasks and explain how you'll do the rest during review. 
Think beforehand how you want to build using plain js, keeping it modular and everything. Have a scaffolding with import/export support ready (webpack).

Example task - Build a todo app. You can add, edit, remove items. Allow sub-tasks within a task. There will be some form validation or api call. Also, persist/load todo items on reload. 

☯️ Ps/ds - There was 1 problem solving question (leetcode easy problem - search from both sides, basic dynamic programming, etc) and 1 data structure question (ex. tree traversal in various orders, stack, etc) . It took about 40-45min. You'll have to first explain your approach and then write the code on paper. 
This is usually not so hard for ui roles. But in some cases, interviewer might ask full-blown sde level questions like red-black tree, etc. There is officially no distinction for psds round between ui and sde roles, but practically there is.

🔶 UI+Javascript - I felt this was the most imp round. It went relatively smooth for me and lasted for about an hour. Expect moderate to advanced Javascript and some browser related questions.

Ex - debounce + throttling (very common nowadays), event handling with delegation, event capturing phase, scope, let/var, promise + promise.all, convert setTimeout to promise, async, how does a page load from start, how to make animations smooth, etc.

Most of these are standard questions. Some questions were asked indirectly using a UI scenario.  

😎 Hiring manager round - If you have reached this round, that's good news. HR will only present 2-3 potential candidates to the manager to save his/her time. 
What will be asked - Depends. Expect some HR questions (why do you want to move, what are you looking for in new role), and some tech questions (authentication, cookies in details, localstorage, new react features, some repeat questions from prev round, etc). Some of the HR questions are asked directly off the internal playbook, so don't read too much into them. 

🧶In general, interviewers were quite helpful and gave me hints when I got stuck. Internally, they have a option to reject any candidate immediately after their round (if it went really bad). Mostly though, they will just add their review and notes, and mention which level (say, UI 1 or UI 2) you are most suited for. 

I expect some of these rounds to change in the current remote interview situation (less touchpoint with interviewers, more hackerrank-oriented rounds), but the general structure should stay the same.
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA2NjM2Nzc3MywyMDM4NTA5MDE5LDExNz
c0MjUxMjEsLTEzMDE1MzI4OTAsLTk3MDgwMjE1NCwxNjQwNDMy
NTg0XX0=
-->