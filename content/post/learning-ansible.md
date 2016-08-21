+++
author = ""
comments = true
date = "2016-07-31T21:26:30+10:00"
draft = false
menu = ""
image = "images/post-cover.jpg"
share = true
slug = "learning-ansible"
tags = ["DevOps", "Ansible", "tech", "english"]
title = "Learning Ansible the hard way"
comments = true
share = true
+++
<br>
**The challenge:** Nothing gets installed unless a script does it.

I had to replace my old Mac and setup a new one. Absolutely boring. I decided to make it not boring by automating the entire process with [**Ansible**](https://docs.ansible.com/) <!--more--> which I was somewhat familiar with; I had read Ansible code before but never actually written any myself. I needed an excuse to learn it and setting up my new laptop was the perfect one.

My goal is to install and configure all the applications I normally use, from Chrome and Spotify, to my password manager or preferred text editor. To do so, I plan to use [Homebrew Cask](https://caskroom.github.io/), one of my favourite tools in the Mac world, which allows me to install most of the desktop Apps through the command line.

**My Plan:**

0. Install Ansible
0. Install [Homebrew](http://brew.sh/) (my favourite package manager).
0. Install [Homebrew Cask](https://caskroom.github.io/)
0. Setup all the things

**The result:** A working computer, a [Github repo](https://github.com/pameck/osx-setup-ansible/), and a happy dev!
<br><br>

### 1. Install Ansible
**Tools I have:** OSX terminal and vi.

Python 2.7 comes out of the box with El Capitan, so I install pip, python’s package manager, and use it to install Ansible.

**First mistake:** I ran all the commands directly in the terminal. After 10 seconds I had broken my rule already, I wasn’t running a script. So I step back, uninstall pip, and create a bash script with the same commands.

Script for installing Ansible done, first task down!

```
sudo easy_install pip
pip install --upgrade setuptools --user python
sudo pip install ansible
```
I would rather be testing this in a vm, but at this point I just have the terminal, vi, pip and Ansible, so ¯\_(ツ)_/¯
<br><br>

### 2. Install Homebrew
[Homebrew](http://brew.sh/) provides a simple command to install it, which made my life easier.

I found [**this guide**](http://ansible.pickle.io/post/86598332429/running-ansible-playbook-in-localhost) to be a simple and easy way to start. After running a couple of “hello world” examples and reading some basic docs my first playbook is ready:

```
---
- hosts: all
  tasks:
    - name: check if homebrew is installed
      command: brew -v
      register: brew_check
    - name: install homebrew with curl
      command: /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
      when: brew_check.stdout.find('command not found') > -1
```

And I need to update the bash script with a command to run the playbook on localhost:

```
ansible-playbook -i “localhost,” -c local setup-all-the-things.yml
```

I also add a check to verify that if Homebrew is already installed, the command wouldn’t run again. I suspect there are better ways to do this than:

```
when: brew_check.stdout.find(‘command not found’) > -1
```

but at this point my goal is to get things done, I’ll worry about a more elegant solution later.
<br><br>

### 3. Install Homebrew Cask
**Tools I have:** OSX terminal, vi, Ansible and Homebrew.

I’m finding Ansible easy to use. It provides a lot of modules which are libraries that encapsulate functionality such as [reading/writing files](https://docs.ansible.com/ansible/list_of_files_modules.html), [managing ssh keys](https://docs.ansible.com/ansible/authorized_key_module.html), etc. [Homebrew](https://docs.ansible.com/ansible/homebrew_module.html) is one of them, and it made installing Homebrew Cask pretty straightforward.

```
- name: Install Homebrew Cask using homebrew
      homebrew:
       name: cask
       state: present
```

### 4. Setup all the things
Installing applications with Cask takes a single command, and there is an Ansible module for it. The module checks if the application already exists, and if it doesn’t, it downloads it and installs it (no need of sudo or any fancy trick). All I need to know is if there is a “cask formula” for the application I want to download.

The development cycle from this point forward is just validating if there is a formula for the app:

```
$ brew cask search spotify
==> Exact match
spotify
==> Partial matches
spotify-notifications
```

and adding it to my playbook.

```
- name: Spotify
  homebrew_cask: name=spotify state=present
```
<br>
### Tidying up
Soon, my script becomes a long list of homebrew_cask commands, hard to read and hard to maintain.

I split the script in smaller and more readable chunks using Roles. And my long list of tasks becomes a small main file and smaller role files.

```
---
- hosts: all
  tasks:
  - name: Install Homebrew Cask using homebrew
    homebrew:
     name: cask
     state: present
roles:
      - { role: dev-tools }
      - { role: mac-preferences }
      - { role: comms }
      - { role: entertainment }
      - { role: git }
      - { role: productivity }
      - { role: security }
```

The file structure looks something like this:

```
- osx-setup-ansible
|_ setup.sh
|_ setup-all-the-things.yml
|_ roles
  |_ git
  |   |_ defaults
  |   |   |_ main.yml
  |   |_ tasks
  |   |   |_ main.yml
  |   |_ templates
  |       |_ git_config
  |
  |_ mac-preferences
      |_ tasks
          |_ main.yml
```

#### Installing and configuring Git
Some applications prove to be more interesting to setup, Git being one good example. Setting up Git involves creating a global config file (.gitconfig) in my home folder and adding some command aliases to my bash_profile.

First I create a global variable with my home folder’s location. Ansible’s lookup module allows me to read any environment variable, so in the main playbook file I add a vars section, create the variable and assign the value of **$HOME** to it. That way I can use the **home_dir** anywhere in my script.

```
---
- hosts: all
  vars:
    home_dir: "{{ lookup('env', 'HOME') }}"
  tasks:
    .....
  roles:
      - { role: git }
```

Then I create a template for the config file

```
[user]
        name = {{ name }}
        email = {{ email }}
[alias]
        co = checkout
        gs = status
        st = status
[push]
        default = simple
[color]
  ui = true
```

and use the template module to add the file in my home folder
```
- name: Add global git congif settings
  template: src=templates/git_config dest={{home_dir}}/.gitconfig
```
Finally, I create a bash_profile in case there isn’t one:
```
- name: Create bash_profile if it does not exist
  file: state=touch path={{home_dir}}/.bash_profile
```
And add the git command aliases to it with the blockinfile module
```
- name: Add aliases to bash_profile
  blockinfile:
    dest: "{{home_dir}}/.bash_profile"
    content: |
      #Git Alias
      alias gs='git status'
      alias gpr='git pull --rebase'
      alias push='git push'
      alias gds='git diff --staged'
      alias gap='git add -p'
      alias gcm='git commit -m'
```
After giving Ansible a try, I am quite pleased about how easy it is to pick up. Ansible does a good job at encapsulating complexity behind simple modules, turning what could have been a long imperative script into a more declarative and easier to read playbook.

My challenge is complete*, I am not an Ansible expert but this exercise gave me a pretty good understanding on how the tool works and the things I can do with it.

*It will never be a 100% complete. My current approach is still to install as much as I can using Ansible, updating the script as I go. One of the things I am very excited about is automating the config for every tool, even the OS preferences, that should make (I hope!) changing computers from now on seamless!

I’m happy to take comments for improvements or new features, the code can be found [here](https://github.com/pameck/osx-setup-ansible/). I added a couple of [issues](https://github.com/pameck/osx-setup-ansible/issues) already.