# Heroku Buildpack: Python (now with rpy2)

The basic Heroku build process fails for rpy2. The solution that I have found consists of the following:
* Set up the [typical multi-buildpacks](https://github.com/ddollar/heroku-buildpack-multi.git). This means running
    $ heroku buildpacks:set https://github.com/ddollar/heroku-buildpack-multi.git
* Add the following to .buildpacks
    http://github.com/ddimmery/heroku-buildpack-r.git#cedar-14
    https://github.com/ddimmery/heroku-buildpack-python
* These two buildpacks are slightly modified from the default versions. In particular,
  * The R buildpack is modified to ensure that the resulting $PATH environment variable is set to use the correct python executable
  * The Python buildpack is modified to ensure that R is visible and usable when running `pip -r requirements.txt`
  * That's basically it. I have no doubt that there's a better way to do this, but I was unable to see how.

![python](https://cloud.githubusercontent.com/assets/51578/13712821/b68a42ce-e793-11e5-96b0-d8eb978137ba.png)

This is forked from the official [Heroku buildpack](https://devcenter.heroku.com/articles/buildpacks) for Python apps, powered by [pip](https://pip.pypa.io/) and other excellent software.

Recommended web frameworks include **Django** and **Flask**. The recommended webserver is **Gunicorn**. There are no restrictions around what software can be used (as long as it's pip-installable). Web processes must bind to `$PORT`, and only the HTTP protocol is permitted for incoming connections.

Some Python packages with obscure C dependencies (e.g. scipy) are [not compatible](https://devcenter.heroku.com/articles/python-c-deps). 

See it in Action
----------------

Deploying a Python application couldn't be easier:

    $ ls
    Procfile  requirements.txt  web.py

    $ heroku create --buildpack heroku/python

    $ git push heroku master
    ...
    -----> Python app detected
    -----> Installing python-2.7.11
         $ pip install -r requirements.txt
           Collecting requests (from -r requirements.txt (line 1))
             Downloading requests-2.9.1-py2.py3-none-any.whl (501kB)
           Installing collected packages: requests
           Successfully installed requests-2.9.1
           
    -----> Discovering process types
           Procfile declares types -> (none)

A `requirements.txt` file must be present at the root of your application's repository.

You can also specify the latest production release of this buildpack for upcoming builds of an existing application:

    $ heroku buildpacks:set heroku/python


Specify a Python Runtime
------------------------

Specific versions of the Python runtime can be specified with a `runtime.txt` file:

    $ cat runtime.txt
    python-3.5.1

Runtime options include:

- `python-2.7.11`
- `python-3.5.1`
- `pypy-4.0.1` (unsupported, experimental)

Other [unsupported runtimes](https://github.com/heroku/heroku-buildpack-python/tree/master/builds/runtimes) are available as well. Use at your own risk. 
