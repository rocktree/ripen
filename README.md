Ripen
============

If you web development machine is a fruit, Ripen is the thing that makes it not
so green, and maybe a little soft, and kind of sweet. If your web development
machine is not a fruit, you may have other issues.

In different words, Ripen is a bash script that helps you go from an empty
Debian/Ubuntu machine to a working web development or production environment,
per your configuration.

Requirements & Assumptions
------------

I assume the following in getting this script to work properly:

* You are running Ubuntu 14.04. This comes with Bash 4.3. If you're using an
  earlier version of Ubuntu, you should have Bash 4 installed.
* You have installed `openssh-server` and are able to login to your server via
  SSH.
* You login to your server and run this command as the `root` user. In other
  words, you have administrative rights over the server and are running as a
  user which those rights.

While it is possible the script will still work if you stray from these
assumptions, your mileage may vary.

> Note: If you've had success or failures on other operating systems, please
> let me know. It may be easy to tweak for expanded support.

We're not doing much heavy lifting here. A machine with 2GB of RAM should be
more than enough to get up and running. Plus, one option is to add a swapfile
to your system.

Running the Script
------------

First, you'll want to download the config file so you can change it per your
settings. You can do this from anywhere on the machine, but probably best to
just stay in your `root` user's home directory.

> Again, note we assume **you are logged in as the root user.**

```text
$ wget https://raw.githubusercontent.com/rocktree/ripen/master/ripen.conf
```

Open this file and edit to your preferences. I use `vim`, but you can use any
editor you prefer.

See the next section for configuration options.

```text
$ vim ripen.conf
```

Once you're ready, save and close and we can run the script. We can run the
script directly from the url. All you need to ensure is **you are in the same
directory as `ripen.conf`.**

```text
$ bash <(curl -s https://raw.githubusercontent.com/rocktree/ripen/master/ripen)
```

Now, wait and watch.

When it finishes, if it went smoothly, **DELETE YOUR CONFIG FILE** (it has
sensitive data in it). If you ran into an exception, [report
it](https://github.com/rocktree/ripen/issues/new).

Configuration Options
------------

We have lots of options you can configure to make the results meet your needs
(which is the whole point of this thing).

> If you don't see an option you'd like, or either [create a new
> issue](https://github.com/rocktree/ripen/issues/new) with the feature
> request, or [contribute to the
> project](https://github.com/rocktree/ripen/issues/new).

Here are the options, broken up by action block.

### System Updates

* `update_system`: (default: `true`) Run `apt-get update` **and** `apt-get
  upgrade`. Before adding packages to Ubuntu, it's good to update the system.
* `reboot`: (default: `true`) Automatically restart the server upon finishing
  the task, which is usually required depending on what you updated and which
  packages/programs you've added.

### User Account

* `add_user`: (default: `true`) Add a new user to the system.
* `user`: The user's attributes
    * `username`: (default:
      [`burtmacklin`](http://media2.giphy.com/media/RaS5rHxJhwnPq/200_s.gif))
      Username of the new user.
    * `password`: (default: `password`) The **unencrypted** password of the new
      user.
    * `options`: (default: `'--create-home --user-group --shell /bin/bash'`):
      Add options to the `useradd` command. [Learn
      more.](http://linux.die.net/man/8/useradd)
    * `sudo`: (default: `true`): Grant the new user full root level
      permissions.
* `username`: (default: `burtmacklin`) The non-root, sudo user where we should
  install *some* of our packages.

> **IMPORTANT: If you choose `false` for `user[sudo]` then your `username`
> needs to be a user with root permissions. Otherwise, ensure the usernames
> match.**

### Bash Config

You probably want to add your own bash config, especially if you are creating a
new user. Here we install the `.bashrc` file and get rid of other potential
conflicts.

* `adjust_bash_config`: (default: `true`) Replace the current bash config.
* `bashrc_url`: (default:
  `https://raw.githubusercontent.com/rocktree/ripen/master/bashrc`): A fully-
  qualified URL from which to pull the bash config. *Needs to be a plain text
  file*.

### Git

* `install_git`: (default: `true`) Install Git.
* `git`: Attribute of the Git user.
    * `name`: (default: `My Server`) Name of the Git user.
    * `email`: (default: `myserver@example.com`) Email of the Git user.
    * `colors`: (default: `true`) Color the Git output.

### Nginx

* `install_nginx`: (default: `true`) Install Nginx. This doesn't configure
  anything, but will start the nginx server.

### MySQL

* `install_mysql`: (default: `true`) Install MySQL server.
* `mysql`: MySQL root user attributes.
    * `root_password`: (default: `password`) Password for root user.

### PostgreSQL

* `install_postgresql`: (default: `true`) Install PostgreSQL. PostgreSQL comes
  with a `postgres` user out of the box and can be accessed and adjust from
  there. It also automatically starts the PostgreSQL server.

### Ruby

At this time, this script only supports installing Ruby via rbenv. If you'd
rather use rvm, then you should set this installation to false and install rvm
manually (or you can contribute to this script!).

* `install_rbenv`: (default: `true`) Install Ruby via `rbenv`
* `ruby_version`: (default: `2.1.4`) The version of Ruby to install. We're
  going to start with just one installation of ruby. Ignore if `install_rbenv`
  is `false`.
* `install_bundler`: (default: `true`) Install Bundler once `rbenv` and `ruby`
  are ready. Ignore if `install_rbenv` is `false`.


Contribution Guidelines
------------

The script is quite simple, and adding features is really easy.

The script itself contains helpers and self-sustained sections. Every section
needs to be toggleable via an option. And each section needs to function
*autonomously* and *with all actions*.

### 01: Add Options

First, add the options for the feature you are adding. You can see the standard
that's being followed. Be sure to add comments explaining your option.

> Note: If you added an associative array, you need to declare it in the
> script.

### 02: Add Actions

Add your commands in the script. Ensure they are toggleable via an option.

### 03: Test It

All you need to do to test is to spin up a new blank server (I use [Digital
Ocean](https://www.digitalocean.com/)). Run the script twice.

1. Run with **only your action running**.
2. Run with **everything set to `true`**.

### 04: Do The Git Thing

1. Fork it
2. Create your feature branch (git checkout -b my-new-feature)
3. Commit your changes (git commit -am 'Add some feature')
4. Push to the branch (git push origin my-new-feature)
5. Create new Pull Request
