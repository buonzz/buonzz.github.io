---
layout: post
title: Inspecting Dynamically manipulated HTML elements
date: 2020-06-14 00:00:00.000000000 +08:00
permalink: inspecting-dynamically-manipulated-html-elements
header-img: "img/20586158743_46b96ef40b_k.jpg"
subtitle: "Do you ever experienced inspecting an element that changes its styling, child, attributes  etc when it gets hovered? By the time you move your mouse to DevTools to inspect it, the element you are trying to inspect already had changed its state! "
tags:
    - debugging jquery elements on mouseover
    - inspect a hover element
    - chrome dev tool inspect on hover
    - debug SPA elements
    - chrome dev tools tips
---

<p>Chrome Devtools "Elements" tab allows you to inspect elements currently displayed in the webpage. But the problem is, what if you want to inspect an element in the state wherein it is currently being "hovered" by your mouse?</p>

<p>This is due to in able to "inspect" the element, you need to move your mouse into Elements tab itself, triggering the "onmouseleave" event in the very element you are trying to inspect!</p>

<h3>The Solution</h3>

<ol>
    <li>Open up Developer Tools and go to "Sources" tab</li>
    <li>Hover your mouse to the element you are trying to inspect</li>
    <li>Without moving the mouse, hit F8 (You should see a "Paused in debugger" in your webpage)</li>
    <li>Click on the "Elements" tab. Now, the styling, attributes etc is displayed properly in "Styles", "Computed" panel in the right side. So go ahead and do your thing.</li>
    <li>Once done, click the "Resume script execution" button</li>
</ol>


