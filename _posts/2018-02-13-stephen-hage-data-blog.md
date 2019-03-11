---
layout: default
title:  "Intersection of My Interests and Data"
date:   2018-02-13 14:46:43 -0600
categories: content
tags: datascience
---

# Introducing Myself
Welcome to my website. I'm an incredibly average guy, and so I figure if I'm really interested in something, there are probably several others who are as well. Where I think I can be interesting is where I turn an interesting question into a data problem. Sometimes I find my intuition was right, but other times I'm amazed at what the data really  say.

I want to use this page to think out loud, to use my data skills to find better conclusions. So what kinds of questions will I be addressing?

# My Interests
One guilty pleasure of mine is sports. I love my alma mater, [Indiana University](https://www.iuhoosiers.com), and I follow football, basketball, and baseball particularly closely. I'll undoubtedly attempt to write up an occasional scouting report, or team comparisons, with them as a primary subject. I also love my hometown [Pacers](http://www.nba.com/pacers/) and [Colts](http://www.colts.com/). I'll occasionally get into peripheral activities like graduation rates, the drafts, broadcasts, and maybe even officiating.

I'm also a nerd about learning new cultures. As an undergrad I was fortunate to study in Milan, Italy, and just barely missed passing proficiency in Level 3 [CILS](https://en.wikipedia.org/wiki/Certification_of_Italian_as_a_Foreign_Language) in Italian. I also minored in French, and have more than a passing interest in European history. Lately, I've become much more interested in Asian cultures, since I married into a filipino family and my youngest brother lives in South Korea. I have begun some analyses on similarities in world cultures through the lens of food (to be published soon), and on my road map is a study of similarities in Olympic excellence and government structure.

Undoubtedly, as time passes, other interests will emerge on this page. I'm sure, as elections approach, I'll do some political analysis. Living in Chicago, I'll surely look at crime statistics and try to propose solutions. If I have my way, in the future I'll build some tools to study media bias.

[![WestNileVirus Presentation](https://raw.githubusercontent.com/stephenhage/stephenhage.github.io/master/images/WNV/SMARRT_Vid_Thumbnail.PNG)](https://youtu.be/Gl2StkLlVqU "SMARRT Consulting WNV")

# Data Skills


<ul>
  {% for post in site.posts %}
    <li>
      <a href="{{ post.url }}">{{ post.title }}</a>
    </li>
  {% endfor %}
</ul>
