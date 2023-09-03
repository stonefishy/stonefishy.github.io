---

title: "How to fix Gem FilePermissionError"
date: 2016-01-12 23:31:35 +0800
comments: true
categories: Backend
tags: [Ruby, Tool]
---

Sometimes we face the erorr "Gem::FilePermissionError: You don't have write permissions for the /Library/Ruby/Gems/x.x.x directory". This error message indicates that the user doesn't have the necessary permissions to install Ruby gems system-wide. This is because the system Ruby installation requires administrative privileges to modify its gem directory.

To resolve this issue, there is a few options:

### 1. Use a Ruby Version Manager (Recommended)

Instead of installing gems globally, consider using a Ruby version manager like `rbenv` or `RVM`. These tools allow you to manage multiple Ruby versions and gems without requiring administrative permissions.

#### Using rbenv
    1. Install rbenv if you haven't already.
    2. Install the desired version of Ruby using rbenv.
    3. Install gems without needing elevated permissions.

#### Using RVM
    1. Install RVM if you haven't already.
    2. Install the desired version of Ruby using RVM.
    3. Use the installed Ruby version without needing elevated permissions to install gems.

<!-- more -->
### 2. Install Gems Locally
If you want to install gems for the system Ruby without using a version manager, you'll need administrative privileges. You can use the sudo command to install gems as the superuser:

``` bash
sudo gem install gem_name
```
However, keep in mind that modifying the system Ruby environment might affect other applications, and it's generally recommended to use a version manager to manage Ruby environments.

### 3. Use --user-install Option
If you want to install gems locally without administrative privileges, you can use the **--user-install** option:

``` bash
gem install gem_name --user-install
```
This installs the gem in your user's home directory, avoiding the need for system-wide modifications.

### 4. Install to Custom Directory
You can configure gems to be installed in a specific directory where you have write permissions. This involves configuring the **GEM_HOME** and **GEM_PATH** environment variables:

``` bash
export GEM_HOME=$HOME/gems
export GEM_PATH=$GEM_HOME:/path/to/system/gems
gem install gem_name
```
Make sure to adjust the paths according to your preferences.

In general, using a Ruby version manager like **rbenv** or **RVM** is recommended, as it provides a more flexible and isolated way to manage Ruby versions and gems without affecting system-wide configurations or requiring administrative permissions.


