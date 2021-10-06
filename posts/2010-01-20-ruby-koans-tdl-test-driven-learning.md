---
layout: post
status: publish
published: true
title: Ruby Koans - TDL (Test Driven Learning)
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 12
wordpress_url: http://www.erichumphrey.com/2010/01/ruby-koans-tdl-test-driven-learning/
date: '2010-01-20 15:42:00 -0600'
date_gmt: '2010-01-20 21:42:00 -0600'
categories:
- learning
- ruby
tags: []
comments: []
---
<p>This past weekend I attended the <a href="http://www.wearemicrosoft.com/">We Are Microsoft</a> <a href="http://givecamp.org/">Give Camp</a> in Dallas with some friends. We live 6 hours away, but did it last year and decided to again this year. When we stopped for lunch <a href="http://www.jaysmith.us/">Jay Smith</a> brought up <a href="http://github.com/edgecase/ruby_koans">Ruby Koans</a>, a project designed to teach Ruby by using TDD. (What is a <a href="http://en.wikipedia.org/wiki/Koan">Koan</a>?) There was some good discussion on the topic which piqued my interest.</p>
<p>Since I do app / web development as well as SQL, I decided when I got home to download it and give it a shot. So far, I think it does a good job of teaching Ruby, with tests first. I think sometimes the answers are a little too apparent, but it essentially acts as a truly interactive tutorial.</p>
<p>All was not simple though. I did have some trouble with the initial. First, I chose to download Ruby 1.9.1 as there is nothing on the Koans site to say I should use the 1.8 series, several <a href="http://svn.ruby-lang.org/repos/ruby/tags/v1_9_1_0/NEWS">breaking changes</a> were in the 1.9 series. Also, the test-unit gem is not included by default in the 1.9.x builds so I had to manually install it, <a href="http://github.com/edgecase/ruby_koans/issues/unreads#issue/3">not very difficult</a>. I ran into <a href="http://github.com/edgecase/ruby_koans/issues#issue/12">another problem</a> after I made it through several sections and resolved it by just commenting out that test; one of the breaking changes made it invalid.</p>
<p>Overall, I like the idea of learning the TDL. In our lunch discussion mentioned earlier, we had talked about migrating this concept to other languages. We're particular to C# and I wonder if I could do this for SQL as well.</p>
