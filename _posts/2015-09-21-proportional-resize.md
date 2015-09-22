---
layout: post
title: Proportionally Resizing Images Using Autolayout
---

# Laying out the scene around the image

Recently I came up against a problem where I needed to position elements relative to a large horizontally centered image. Since using a scrollview was not on option, I needed a solution that would resize a UIImageView  depending on how much screen real estate was available. I wanted the image to resize, pushing the text below it either up or down, but maintaining the aspect ratio of the image. Using a simple app for view images as an example, we can spec out the design like so:

![Problem]({{site.url}}/assets/2015-09-21-problem.png)

1. Image must have a minimum of 28 pts margin on left and right (see below for how we came up with those numbers)
1. Image can downscale but must maintain aspect ratio of 6:5
1. Image title label is positioned relative to the bottom of the image
1. Button is positioned relative to the bottom of the screen

# Choosing a design size

We are going to design for the iPhone 6, scale the image down on bigger phones and and keep the image centered horizontally for the 6 plus.

On the 6 we want some margin on either side of the image so we are going with a width 320 points and a height of 266 points.

# Aspect ratio

We are going with an aspect ratio of 6/5. If you are working with a designer or are designing multiple screens it may be helpful to use this to estimate where things will be laid out for various screen sizes, since we are positioning text under the image.

# Setting the constraints

## Maintain the aspect ratio

Select the image in the scene and set the aspect ratio of the image.

![Aspect Ratio 1]({{site.url}}/assets/2015-09-21-aspect-ratio-1.png)

Then select the new constraint and set the multiplier to 6:5 or whatever your aspect ratio is.

![Aspect Ratio 2]({{site.url}}/assets/2015-09-21-aspect-ratio-2.png)

## Center image 

Create a constraint centering the image horizontally. Make sure the priority is set to 1000

## Add leading and trailing margins

On the iPhone 6 and larger we want to set trailing and leading space greater than or equal to (screen width - image size) / 2 so that the image stays horizontally centered. So we get (375 - 320) / 2 = 27.5 for each constraint. To stop these constraints from conflicting with the horizontal centering we assign each of them a <em>lower priority</em> of 999.

![Leading Space Constraint]({{site.url}}/assets/2015-09-21-leading-space.png)

## Resulting Constraints

Finally, your scene should look like this:

![All Constraints]({{site.url}}/assets/2015-09-21-final-constraints.png)

# On Device Appearance

1. 4s has margins at 28 and the image has been shrunk
1. 6 has the same layout we designed with
1. 6s has additional margin on the left and right of the image

![On Device]({{site.url}}/assets/2015-09-21-devices.jpg)



