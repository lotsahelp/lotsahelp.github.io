---
layout: post
status: publish
published: true
title: Missing Biometric devices Node in Device Manager
author:
  display_name: lotsahelp
  login: lotsahelp
  email: eric@erichumphrey.com
  url: ''
author_login: lotsahelp
author_email: eric@erichumphrey.com
wordpress_id: 336
wordpress_url: http://www.erichumphrey.com/?p=336
date: '2020-05-21 12:09:55 -0500'
date_gmt: '2020-05-21 18:09:55 -0500'
categories:
- Uncategorized
tags: []
comments: []
---
<p><!-- wp:paragraph --></p>
<p>If you have a Windows Hello compatible device (face or finger-print) and you are unable to use it, make sure Device Manager has a node for Biometric devices (see below). I have a Windows Hello Face capable laptop and lost the ability to login with my face. This node was no longer there. After some back and forth with tech support, they were finally able to fix it. I'm blogging this since I could never find a good answer searching on my own and want to document the fix.<br></p>
<p><!-- /wp:paragraph --></p>
<p><!-- wp:image {"id":339,"sizeSlug":"large"} --></p>
<figure class="wp-block-image size-large"><img src="https://www.erichumphrey.com/wp-content/uploads/2020/05/image.png" alt="" class="wp-image-339"/></figure>
<p><!-- /wp:image --></p>
<p><!-- wp:paragraph --></p>
<p><br>First make sure your camera still works using your preferred app. If not, you'll want to fix that first. Next browse to the folder "C:\Windows\System32\WinBioPlugIns\FaceDriver" and right-click the two .inf files (Setup Information) and Install them. Go back into Settings -> Accounts -> Sign-in Options and Windows Hello Face should be available again.</p>
<p><!-- /wp:paragraph --></p>
<p><!-- wp:image {"id":340,"sizeSlug":"large"} --></p>
<figure class="wp-block-image size-large"><img src="https://www.erichumphrey.com/wp-content/uploads/2020/05/image-1-1024x412.png" alt="" class="wp-image-340"/></figure>
<p><!-- /wp:image --></p>
<p><!-- wp:image {"id":341,"sizeSlug":"large"} --></p>
<figure class="wp-block-image size-large"><img src="https://www.erichumphrey.com/wp-content/uploads/2020/05/image-2-1024x613.png" alt="" class="wp-image-341"/></figure>
<p><!-- /wp:image --></p>
