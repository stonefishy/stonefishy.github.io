---
layout: post
title: "how to install packages in sublime text 3"
date: 2014-12-26 22:35:36 +0800
comments: true
categories: [Sublime Text, Tools]
---
`Sublime Text` is a sophisticated text editor for code, markup and prose. You can go to offical **[website](http://www.sublimetext.com/)** to download and see how to use it. It's a very powerful weapon for developer. And also it has so many packages to expand its functional.   

If you want to install packages in your sublime text. First of all, you need to install `Package Control` that is `Sublime Text` package manager that makes it exceedingly simple to find, install and keep packages up-to-date.  

Open Sublime Text 3 and choose `View > Show Console` menu. Paste below codes into console.
``` python
import urllib.request,os,hashlib; h = '2deb499853c4371624f5a07e27c334aa' + 'bf8c4e67d14fb0525ba4f89698a6d7e1'; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); by = urllib.request.urlopen( 'http://packagecontrol.io/' + pf.replace(' ', '%20')).read(); dh = hashlib.sha256(by).hexdigest(); print('Error validating download (got %s instead of %s), please try manual install' % (dh, h)) if dh != h else open(os.path.join( ipp, pf), 'wb' ).write(by)
```
<!--more-->
This code creates the Installed Packages folder for you (if necessary), and then downloads the `Package Control.sublime-package` into it.   

Since the `Package Control` install successfull, you can intall any packages which you want, such as less,less2css package. Press `Command+Shift+P` open the `Command palette` and find the `Package Control: Install Package`, like this:   
![command palette](/assets/images/command-palette.png)

Choose `Package Control: Install Package` menu, and wait a seconds it will prompt package installer, and then you can search packages what you want and install it.   
![package installer](/assets/images/package-installer.png) 





 