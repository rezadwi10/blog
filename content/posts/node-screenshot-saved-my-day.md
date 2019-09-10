+++
title = "Node Screenshot Saved My Day"
date = 2019-09-10T09:17:02+07:00
draft = false
image = "https://static.makeuseof.com/wp-content/uploads/2016/04/chrome-vs-firefox-2016-670x335.jpg"
showonlyimage = false
+++

Technical Documentation is a must-thing which I need to provide as a part of deliverables from technical team.
<!--more-->
It is not like when you done coding, making sure it works, write beautiful commit message, push
then just let the CI/CD do the works while hoping nothing's gonna bad happen in prod.

> Prod? the weirdest place where everything used to worked like charms

In my current organization, I was asked to write structured technical documentation explain purpose of the project?
What changes you made? How to compile and serve them? Lastly, a brief SIT scenario and the result.

When, I was assigned to do web-based project, sometimes, I need to clearly explain & capture what params I modified in
a scrolled screen. Actually, I used to steps-scrolled down the screen and then capture the region and then highlighting 
the changes and then <kbd>ctrl+c</kbd> & <kbd>ctrl+v</kbd> to the docs and keep doing this to the end of the screen.
It is too time consuming effort for cheap jobs, till I give up and start to find replacement software which can
do the job for me effortlessly. 

Then I found [sharex](https://getsharex.com/), it is light yet so powerfull. It contains area size and fullregion capture,
it contains screen recording and the most important it has its own image editor to hightlight the screenshot. ![Sharex][1]

It was good, but I found much more simple solution, I can even capture specific node / tag from the HTML directly from the
BROWSERS itself :(. Such a waste, since I tried so many utils. Here it is

### Chrome
Open page you want to capture, right click on the area you want capture then click Inspect element.
Select specific tag and then press <kbd>ctrl+shift+p</kbd>
Choose **Capture node screenshot**. The image will be saved to your drive.

![Node Chrome][2]

### Firefox
You can do this as well in firefox, it's about taste.
Open page you want to capture, inspect element, choose node and then right click to it. Choose **node screenshot**. Done.

![Node Firefox][3]

> for highlighting, you can use sharex

[1]: https://getsharex.com/img/ShareX_Animation.gif
[2]: /img/posts/chrome-node-screenshot.png
[3]: /img/posts/firefox-node-screenshot.png