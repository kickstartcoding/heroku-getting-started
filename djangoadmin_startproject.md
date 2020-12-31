# Launching to Heroku

This is how you deploy a Django applications to Heroku, if you started it using
`django-admin startproject`, but not using
[django-kcproject-starter](https://github.com/michaelpb/django-kcproject-starter/).
For an example project that was created using `django-admin startproject` and
deployed, see here:
[github.com/michaelpb/djtestproj](https://github.com/michaelpb/djtestproj)


1. Go to the directory of your Django project

2. Create a file called `Procfile` with the following contents:
```bash
web: python manage.py runserver 0.0.0.0:$PORT
```

3. Heroku requires you that your project is in a git repository in a standard
way: The `Procfile`, `Pipfile` (or `requirements.txt`), and `manage.py` are all
at the "top level" next to `README.md` and `.git`, etc.  Check the location of
the `.git` directory with `ls -a`. Refer to the `djtestproj` example project if
you are uncertain what it should look like.

4. Allow Django to be served on any domain, by changing the list called
`ALLOWED_HOSTS` in your `settings.py`, as such:

```python
ALLOWED_HOSTS = ["*"]
```

5. Login to Heroku (should only have to do this once):

```bash
heroku login
```

6. Create a new Heroku Application on your account for your project:

```
heroku create
```

7. Configure this Heroku app to skip a step which likely will break:

```bash
heroku config:set DISABLE_COLLECTSTATIC=1
```

8. Do do a git add and git commit. You will have to do this before every launch
to Heroku:

```bash
git add -A
git comit -m "your commit note goes here...."
```

9. Launch your site on Heroku:
```bash
git push heroku master
```

If it's working, you should see text like "Python app detected", then
after a 30 seconds or so, something like:

```
remote: -----> Launching...
remote:        Released v4
remote:        https://pure-crag-68.herokuapp.com/ deployed to
```

That strange URL "pure-crag-68" will be a different combo of random words and
numbers for you. Click on that link or copy and paste it into your browser to
see your application live for the world to see.

While things will be mostly working now, it's using the default SQLite database
which has many drawbacks and is not intended for "use in production" or use 
outside of development on your local computer. Keep on reading on how to solve this.


## Tip: Postgres on Heroku

By default, Django is set-up to use SQLite. This has a big drawback: Every time
you push to Heroku, it will clear your database. You may want to choose to use
Postgres instead.


### Provisioning on Heroku



1. Local installation (only need to do once)
    * Ubuntu Linux: `sudo apt-get install postgresql-client`
    * macOS: `brew install postgres`

2. Creating a new Heroku Postgres Database (only need to do this once per
Heroku):

```bash
heroku create # Create a Heroku app (only run this if you haven't already)
heroku addons:create heroku-postgresql:hobby-dev --app pure-crag-68
```

3. Confirm that the database is working by connecting to it with the
command-line client:

```bash
heroku pg:psql --app pure-crag-68
```

*Note:* For the these last commands, you will need to change "pure-crag-68" to
the similarly random name your app got when you created it. Also, use Ctrl+D to
exit the psql prompt.


### Configuring Django to use Postgres

* *If you used `django-admin startproject`*, then follow this guide:
  [`devcenter.heroku.com/articles/django-app-configuration`](https://devcenter.heroku.com/articles/django-app-configuration)

* Once Heroku & Django is properly configured for Django, you'll need to run
  the migrations on the remote Postgres database. That can be done as follows:

```bash
heroku run python manage.py migrate
```


## Tip: Using Heroku

* To push to GitHub, you will use `git push` as before.

* Going forward, you will only need to repeat repeat step 10 & 11 (`git push
  heroku master`) to create a new git commit and release it to the world. The
  rest of the steps you won't have to repeat.

* If stuff still isn't working, use the `heroku logs` command to see error
  messages.

* Consider using the Heroku online interface to change the app name from the
  random nonsense name to be something more appropriate.



----------------------

# Setting up individual features

This last bit is about how to add more cool features to a Django project that
was started using the default `django-admin startproject` template. **However,
if you are starting from scratch, you may even want to just use a better
project starter template.** We'd suggest using [Kickstart Coding's Django
Project Starter](https://github.com/kickstartcoding/django-kcproject-starter),
which comes with various niceties already configured (notably signup / login
pages, Debug Toolbar, and separate production / dev settings).

## Security

This guide's recommendations, while expedient, are not very secure.  If you
want to tighten up the security of your web app to a bare minimum, then do the
following.


### Disabling `DEBUG` and enabling `ALLOWED_HOSTS`

If DEBUG is enabled, it exposes potentially private information when 404 or 500
errors occurs.  Configure Django to turn off DEBUG while on Heroku. Add the
following to `settings.py`:

```python
if 'DYNO' in os.environ:
    # Is on Heroku, use production settings
    ALLOWED_HOSTS = ['NAME_GOES_HERE.herokuapp.com']
    DEBUG = False
else:
    # Not on Heroku, use development settings
    ALLOWED_HOSTS = []
    DEBUG = True
```

- Note: Replace `NAME_GOES_HERE.herouapp.com` with your domain-name.


### Create a secret key

1. Add a Django secret. The only requirement for this is it should be random and
long, you will never have to remember it or type it. Mashing your keyboard is
good enough:

```python
heroku config:add SECRET_KEY=dkljfaojtoerinsgp984tgiusnfd
```

2. Configure Django to use that secret in `settings.py`:

```python
SECRET_KEY = os.environ.get(
    'SECRET_KEY',
    '^n+&a@x-m$o@$-tte*254fpqtf%u18!)nz-kdnyn^-f=6cgxqm',
)
```

**NOTE:** The second argument should be just the default series of characters,
NOT the random mashed keyboard you did above.



## Mailgun

1. Add env variables to Heroku as such:

```
heroku config:set MAILGUN_API_KEY=(YOUR API KEY AS SPECIFIED BY MAILGUN)
heroku config:set MAILGUN_DOMAIN=(YOUR DOMAIN AS SPECFIEID BY MAILGUN)
```

2. Install anymail

```
pipenv install django-anymail
```

3. Then, add the following to your `settings.py`:


        # Anymail (Mailgun)
        # ------------------------------------------------------------------------------
        # https://anymail.readthedocs.io/en/stable/installation/#installing-anymail
        INSTALLED_APPS += ['anymail']
        EMAIL_BACKEND = 'anymail.backends.mailgun.EmailBackend'
        # https://anymail.readthedocs.io/en/stable/installation/#anymail-settings-reference
        ANYMAIL = {
            'MAILGUN_API_KEY': os.environ.get('MAILGUN_API_KEY'),
            'MAILGUN_SENDER_DOMAIN': os.environ.get('MAILGUN_DOMAIN')
        }



### AWS S3

If you want to be able to upload files and not have them be wiped out each time
you launch, you'll need to set up Amazon S3 or an equivalent competing Object
Store (such as Digital Ocean's Object Store).

Setup AWS and get your keys by following this guide:

<https://devcenter.heroku.com/articles/s3>

