# dokku-app-predeploy-tasks

For use with [dokku](https://github.com/progrium/dokku)

A common requirement with Dokku deployments is you need to run a command on a newly built container,
before an app is deployed. Common use cases include:

* Checking a database is initialised
* Running database migrations
* Any commands required to set up the server (e.g. something like a Django collecstatic)


## Notes

Currently runs anything that isn't of proctype web, worker, urgentworker or clock

## Installation

On the dokku server, you need to install the plugin in the standard Dokku way. Specifically:

```
cd /var/lib/dokku/plugins
git clone https://github.com/michaelshobbs/dokku-app-predeploy-tasks.git
dokku plugins-install
```

## Usage

In our applications Procfile, add a line of the form

```
name:cmd
```

For example, for a Django application, a common requirement is to collect the static files. To do this automatically
add the following line to your applications Procfile

```
static: python manage.py collectstatic
```

The name field is just a name you give the task. This can be anything except 'web', 'worker', 'urgentworker' or 'clock'
that have special meanins in Dokku/Heroku.

With this line installed you should on future git pushes see the output, for example:

```
$ git push dokku master
Counting objects: 9, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 736 bytes | 0 bytes/s, done.
Total 6 (delta 1), reused 0 (delta 0)
-----> Cleaning up ...
remote: Cloning into '/tmp/tmp.uXy7naFE5n'...
-----> Building hellodjango ...
remote: done.
remote: HEAD is now at 5f24346... Fixed gitignore
       Python app detected
-----> No runtime.txt provided; assuming python-2.7.6.
-----> Using Python runtime (python-2.7.6)
-----> Installing dependencies using Pip (1.5.4)
       Cleaning up...
<b>-----> Collecting static files
       69 static files copied to '/app/staticfiles'.</b>
```

The commands will be run in the order that they occur in the Procfile.


## Thanks
Thanks to [dokku-logging-supervisord](https://github.com/sehrope/dokku-logging-supervisord) for the pattern and helpers.
