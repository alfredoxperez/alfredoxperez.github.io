---
title: "Blogging with Jekyll on Github User Pages"
tags: [Jekyll github]
---


There are many options out there to create a blog but github.com was an obvious choice.  I use it both at work and home and am pretty happy with it. Github also has [user "Pages"](https://pages.github.com/) that allow you to create a blog using Jekyll.

Creating the page on github is extremely simple so I'll skip that and focus on Jekyll and the the blog itself. I was hesitant to learn and use Jekyll at first but with the help of templates it was a great experience. 

[Jekyll](http://jekyllrb.com/) is a blog-aware, static site generator.  Great! But what does that mean?  In part it means that once you've created the site all you need to do is write a blog post with some YAML Front Matter tags, preview locally, and upload it to the "_posts" directory. Jekyll can take care of the formatting, headers, footers, and links!

I did run into some issues with getting [Jekyll installed](https://help.github.com/articles/using-jekyll-as-a-static-site-generator-with-github-pages/) by running "bundle install" on Mac OS X (10.10).  The error message was:

~~~bash
An error occurred while installing RedCloth (4.2.9), and Bundler cannot continue.
Make sure that `gem install RedCloth -v '4.2.9'` succeeds before bundling.
~~~


To resolve this I ultimately had to update xcode and reinstall ruby:

~~~bash
sudo xcodebuild -license
sudo rvm reinstall 1.9.3
~~~


Jekyll automatically gathers all your files and creates the website.  Because of this I decided to use markdown (.md) files instead of .html files:

  * Markdown is easier than HTML
  * Jekyll will convert files to .html
  * this initially helped to deliniate from files I can edit and files that where created by Jekyll
  * it was an opportunity to work with something new


I also chose [Markdown](http://daringfireball.net/projects/markdown/syntax), which has been a great alternative to raw HTML. "Markdown’s syntax is intended for one purpose: to be used as a format for writing for the web." For any markup that is not covered by Markdown’s syntax, you simply use HTML itself.

I now have the host and the framework but I didn't want to write a blog from scratch. For that I searched around for a template and found [autm-rb](https://github.com/railsr/autm-rb).  Some of the goodies found in autm-rb:

  * handful of directories / Jekyll use cases
  * scripts for Google Analytics and DISQUS comments
  * bootstrap-sass
  * many more 


Finally, I customized the template by changing the homepage, background, and colors. In a few hours I had my custom blog up and running!
