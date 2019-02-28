![Kickstart Coding Logo](./logo.png)

# Kickstart Heroku: Getting Started

Tutorial for installing Heroku's command-line tool, and launch an example
Django website.

## Who is this guide for

* This is for **brand new Python users**, including **coding class students**
  who want a simple and to-the-point guide on getting Heroku working on their
  system to launch Python websites.

* This guide assumes you already have fundamental Bash knowledge.

* This guide *does not* support Windows. It assumes you use either **macOS** or
  **Ubuntu GNU/Linux**.

> This was original created for Kickstart Coding, the affordable,
> inclusive, and intensive coding course teaching cutting-edge Python /
> Django and JavaScript / React web development in Oakland, CA.
> [Learn more and enroll here.](http://kickstartcoding.com/?utm_source=github&utm_campaign=cheatsheets)


# What is Heroku?


**Heroku** is a company that will run an HTTP server application for you, and
expose it to the internet.

They will do the heavy lifting of installing PyPI dependencies and making sure
the right HTTP requests get to your servers (routing), you in turn need to only
make sure your application is properly configured. Also important: They have a
"freemium" payment model, where there is a "free-tier" that is great for
learning without spending any money.

Heroku is now a subsidiary of SalesForce, and in turn rents its server space
according to an agreement with Amazon Web Services.

### Key Terms

* **Heroku** (the business) - a company that will run your HTTP server
  application for you, and expose it to the web.

* `heroku` (the CLI command) - A tool that Heroku the company has developed
  which we will use for interacting with your Heroku account, and launching
  applications.

* heroku **app** - One particular application you are running on a Heroku
  server.

* heroku **add-on** - Extra stuff your application needs, such as databases or
  media file services. These might cost money, and might be provided by a third
  party.


# Guide

Now that you know a little about Heroku, it's time to install their CLI tool on
your system and get used to using it.

## Getting a Heroku account

* First, go to the [Heroku.com website](http://heroku.com/) and sign up for an
  account. Remember your username and password, you will be using it in a
  moment.

* You may want to add in your credit card information -- although optional, it
  unlocks more free apps and add-ons at once even if you never buy anything.

## Installing the Heroku Tool

### Ubuntu Linux

Either use the Ubuntu Software Center (search for Heroku and click Install), or
run the following command:

```bash
sudo snap install --classic heroku
```

### macOS

Run the following command:

```bash
brew install heroku/brew/heroku
```


## Logging in

Login to Heroku (should only have to do this once):

```bash
heroku login
```

## Using Heroku

Broad concepts:

* For every web project you are working on, if you want to publish it, you
  should create for it a *Heroku app*.

* Heroku acts a little like GitHub: They provide a *remote* Git server that we
  can push to. So, when you create an app, you are also creating a remote repo
  to store your code (much like GitHub).

* Whenever Heroku receives code from us via a `git push`, they will then try to
  run that code on their servers, and expose it to the world.

## Creating a new Heroku app

Every time you want to launch a brand new Python project, you will need to
create a new Heroku app for it.  These are the steps to both create a new
heroku app, and link your git repo to it.

**NOTE:** For this tutorial, this repo contains an example Django project you
can launch! Feel free to `git clone` this repo, and use it as your example
Django project to launch.

1. Go to the directory of your project.
    - Ensure your project has a `Procfile`. If you are using this example
      Django repo, you will already have a `Procfile`.  This tells Heroku which
      file is the main "entry point" to your server, in other words, which file
      to run to kick everything off.
    - Ensure your Django project has a `Pipfile` (or `requirements.txt`).
      Again, if you are using this example, you will already have a `Pipfile`.

2. *Important:* Your project MUST be in a git repository.
    - If you `git clone` this repo, then you are good on this front, also.
    - You can either do this the normal way by going to GitHub and creating a
      new repo, then cloning that, OR you can use the "git init" command to
      make a local-only repo. However you do it, Heroku absolutely requires
      your project to be in a single git repo, such that the `Procfile`,
      `Pipfile`, and manage.py are all at the "top level" next to `README.md`
      and `.git`, etc.

3. Create a new Heroku Application on your account for your project. This
command should be executed within your git project directory:

```bash
heroku create
```

4. Ensure that your git repo is now linked to a Heroku remote, in addition to
GitHub:

```bash
# You should see both "origin" twice and "heroku" twice (4 lines) when running
# the following git command:
git remote -v
```

## Launching your Heroku app

If all went well, you are good to go! Deploy your app to Heroku:

```bash
git push heroku master
```

If it's working, you should see text like "Python app detected", then
after a 30 seconds or so, something like:

```
remote: -----> Launching...
remote:        Released v4
remote:        https://secure-lake-12038.herokuapp.com/ deployed to
```

That strange URL "secure-lake-12038" will be a different combo of random words
and numbers for you, but will be the URL of your new app! Click on that link or
copy and paste it into your browser to see your application live for the world
to see.


## Making updates to your project

As you work on your project, after every change that you want to test on
Heroku, make a commit (`git add -A`, `git commit -m`) and then repeat the
previous last step (`git push heroku master`).


# Heroku Tips

* By default, they pick a weird, random domain name, but you can customize it
  later if you want to have a custom name, or even a purchased  domain name.

* If you want to launch code to Heroku, it must be in a brand new commit. So,
  whenever you want to relaunch to the world, you will need to create a new
  commit.
