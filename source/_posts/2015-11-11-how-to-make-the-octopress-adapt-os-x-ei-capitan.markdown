---

title: "How to make the octopress adapt OS X EI Capitan"
date: 2015-11-11 22:46:35 +0800
comments: true
categories: Backend
tags: [Ruby, Tool, MacOS]
---
Octopress was broken after I upgraded Mac to OS X EI Capitan 10.11.1 in these days. The details of the problem is below.

Octopress is work well in Mac10.10. but when I upgraded to Mac10.11.1(OS X EI Capitan). And put *rake preview* in terminal to see the blog what looks like, got following error.

	Starting to watch source with Jekyll and Compass. Starting Rack on port 4000
	rake aborted!
	Errno::ENOENT: No such file or directory - compass
	/Users/user/git/octopress/Rakefile:85:in spawn
	/Users/user/git/octopress/Rakefile:85:in block in <top (required)>
	Tasks: TOP => preview
	(See full trace by running task with --trace)
	
<!-- more -->
Why we get this error, this error caused by rake command, so that means maybe it is ruby version problem. let's take a look it.

	~-> $ ruby -v
	~-> ruby 2.0.0p645 (2015-04-13 revision 50299) [universal.x86_64-darwin15]
	

The ruby newest version is 2.2.3. Ok, let's upgrade it. There are two ways to do it.

#### Using rbenv
If you don't have rbenv in your local machine. You need to install it firstly.    
1.Go to [https://github.com/sstephenson/rbenv](https://github.com/sstephenson/rbenv) to get it.   
2.install ruby-2.2.3
	
	rbenv install 2.2.3
	
3.In the root of your octopress blog, put below commands
	
	rbenv local 2.2.3
	
	gem install bundler
	
	bundle install
	
#### Using RVM
1.Clear git cache

	rm -rf /usr/local/.git
	
2.Install RVM

	curl -L https://get.rvm.io | bash -s stable --ruby
	
3.Install Ruby 2.2.3

	rvm install ruby-2.2.3
	
	rvm use 2.2.3
	
	rvm rubygems latest
	
After these steps, make sure you're using Ruby 2.2.3
4.Go to the root of the your octopress blog

	gem install bundler
	
	bundle install

Both ways can bring octopress back.

*Reference: [https://github.com/imathis/octopress/issues/1749](https://github.com/imathis/octopress/issues/1749)*