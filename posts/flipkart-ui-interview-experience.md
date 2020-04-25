🎙️ Flipkart UI Engineer interview experience

Over the last year, some people have asked me about the UI interview process at Flipkart. I might have said I will write back in some time, but never got around to writing. Sorry for that! 
Here's a detailed version -
📞 A recruiting agency (interviewbit) contacted me and setup the onsite. There was no screening round, it was directly onsite where about 10 candidates were invited. ✳️ Flipkart loves this group interviews through agency, and that is your best shot of getting to the door. Most of my colleagues also got in through s

The interview was a one-day process - morning to late evening. First, they gather everyone in a meeting room and explain the machine coding task (1st round). Next rounds are ps/ds (problem solving + data structure), ui + Javascript and finally, manager round. 

Machine coding - This is a ui you have to build using plain html/css/js - no ui library like react or vue is allowed. Think beforehand how you want to build using plain js, keeping it modular and everything.
There is a list of reqd and good to have features. Complete atleast some of them. But more focus is on clean coding, best practices and logic, rather than on the ui look. Have a scaffolding with import/export support ready (webpack or something).

Example task -  Consider a list and detail view of credit cards. There is a existing list of cards, clicking on one of them opens a edit form (for the card details) below. This details form has dynamic validation. When the user is typing card number, detect the type of card and change validation (or remove if reqd) other card details fields like cvv, expiry, etc. 

They provide a json api which gives list of types of cards (amex/visa) and regex/field rules for each of them. It will have a regex to check if this card number is say, amex. And then for amex, details of other card fields - cvv required?, length of cvv, expiry date max range, etc. 

Ps/ds - 1 problem solving question (leetcode easy problem - sorting, find from both sides, etc) + 1 data structure question (ex. tree traversal starting from leaf, stack, etc) . 
This is not so hard (usually) for ui roles, but i have heard if you are unlucky interviewer might go full-blown sde level like red-black tree.
There is officially no distinction for psds round for ui roles, but practically there is

UI+Js - most imp round (i felt), expect somewhat advanced js + browser related questions (debounce, event handling with delegation, event capturing phase, scope, let/var, promise + promise.all, async, how does a page load from start, etc) 

Manager round - Depends. Some HR questions (why you want to move / join), mostly tech questions.
For me it was - how authentication, cookies, what all cookie contains, react new version, how do you learn, etc
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwNzI5MDk4MiwxNjQwNDMyNTg0XX0=
-->