---
layout: post
title: "A New Way For Developing Web Application Front-End"
date: 2015-01-13 22:43:30 +0800
comments: true
categories: [Guard, LiveReload, Tools]
---
When we develop web application, if we write or modify the HTML structure, CSS style and javascript, we need to compile the code and deploy it, and then refresh page, finally we can see our changes. There are so many steps. So could we skip these steps and just see our changes on page directly when we save the codes. Yes, I can answer you now.    

In order to achieve this goal, we need to install `Guard`, `LiveReload` and related stuff, configure it in our web application project.  

<!-- more -->  

###Guard###
`Guard` is a command line tool to easily handle events on file system modifications. The offical website in [here](http://guardgem.org/).

+ File system changes handled by awesome Listen gem.
+ Support for visual system notifications.
+ Huge eco-system with more than 220 guard plugins, go to this [website](https://rubygems.org/search?query=guard-) to get plugins what you want.   

The simplest way to install guard is to use `Bundler`. Go to the guard offical website to see how to install it.    

###LiveReload###
`LiveReload` monitors changes in the file system. As soon as you save a file, it is preprocessed as needed, and the browser is refreshed.    
Even cooler, when you change a CSS file or an image, the browser is updated instantly without reloading the page.

And here, we use `guard-livereload` plugin to notificate the browser to reload page. this plugin base on `Guard` and `LiveReload`, you have to install Guard firstly. Visit this [website](https://github.com/guard/guard-livereload/) to see how to install it.

###LiveReload Safari/Chrome extension###
Finally, you need to install `LiveReload Safari Or Chrome Extension`, and here, we just use chrome to load web application. So go to Chrome Web Store and find livereload extension to install it on your chrome browser. And also you can use Safari browser.

And now the tools we need have been installed. So let's getting started.
```
$ guard init livereload
```
By runing above command line, it will add guard definition to your `Guardfile`. You can adapt your 'view' files like you want. And the default content of Guardfile by livereload initialize is below:    
``` ruby
guard 'livereload' do
  watch(%r{app/views/.+\.(erb|haml|slim)})
  watch(%r{app/helpers/.+\.rb})
  watch(%r{public/.+\.(css|js|html)})
  watch(%r{config/locales/.+\.yml})
  # Rails Assets Pipeline
  watch(%r{(app|vendor)(/assets/\w+/(.+\.(css|js|html))).*}) { |m| "/assets/#{m[3]}" }
end
```
And you can see that there has some watch command in the Guardfile. Changing it by your situation.

Now I will use a login page to test the guard and livereload how to work. This project contains script file, less file and html file. When we change the less file it will generate css file automatically, and here we use Sublime Text plugin to finished it, and also you can use `guard-less` plugin which is guard plugin.   
![](/assets/images/project-structure.png)

In test project directory, we can find the Gemfile and Guardfile.
``` ruby Gemfile
# A sample Gemfile
source "https://rubygems.org"

# gem "rails"
group :development do 
	gem 'guard'
	gem 'guard-livereload', require: false
end
```
And here we just only use `guard-livereload plugin`, we also can use `guard-less` to configure the less to auto generate css file. But here we use the `Sublime Text` development tools to develop this project, it supports plugin to generate less to css.    

Let's see the guard how to watch our project files. the content of Guardfile is below:
``` ruby Guardfile
# A sample Guardfile
# More info at https://github.com/guard/guard#readme

## Uncomment and set this to only include directories you want to watch
# directories %w(app lib config test spec feature)

## Uncomment to clear the screen before every task
# clearing :on

## Guard internally checks for changes in the Guardfile and exits.
## If you want Guard to automatically start up again, run guard in a
## shell loop, e.g.:
##
##  $ while bundle exec guard; do echo "Restarting Guard..."; done
##
## Note: if you are using the `directories` clause above and you are not
## watching the project directory ('.'), the you will want to move the Guardfile
## to a watched dir and symlink it back, e.g.
#
#  $ mkdir config
#  $ mv Guardfile config/
#  $ ln -s config/Guardfile .
#
# and, you'll have to watch "config/Guardfile" instead of "Guardfile"

guard 'livereload' do
	watch /.*/
end
```
In there, you will see that the all file in this project directory has been watched by guard-livereload. 

Type below commands in your terminal.
```
$ guard
```
It will prompt you that LiveReload is waiting for a browser to connect. And the Guard is now watching at your specific files.

Open the web application on your chrome browser, and click LiveReload extension. And now you will see the terminal has a message said Browser connected.    

So far, you have prepared all configurations. And now you can modify the source file and save it, then you will see the web application automatically refresh.   
![](/assets/images/auto-refresh-page.png)


