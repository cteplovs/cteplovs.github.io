---
author: christeplovs
comments: true
date: 2014-06-16 13:00:26+00:00
layout: post
link: https://christeplovs.wordpress.com/2014/06/16/using-dokku/
slug: using-dokku
title: Using dokku to deploy my visualization apps
wordpress_id: 513
categories:
- Work
---

[Note: this is more of a reminder-to-self at this point but I hope to revise it over the next while to make it more useful to others. I struggled with some of the details that I specify below so I hope this is useful to someone who is contemplating a slightly more complicated dokku setup.]

I was recently deploying a droplet on [DigitalOcean](https://www.digitalocean.com/) (interested in signing up?  use my [referral code](https://www.digitalocean.com/?refcode=1e6ffbbb62da) and we both get some credit!), had planned to use a standard LAMP stack, but got curious about some applications that I didn't recognize. Worrying that my operations talents were getting (really) rusty, I thought I should invest some time coming up to speed on some of these new goodies. I decided to check out '[dokku](https://github.com/progrium/dokku)'. Wow!

Simply put, dokku streamlines the use of linux containers to deploy applications (relatively) painlessly.  There are a lot of great articles available on dokku: [a great introduction to dokku written by its creator](http://progrium.com/blog/2013/06/19/dokku-the-smallest-paas-implementation-youve-ever-seen/), and a collection of articles on easy it is to deploy it on a DigitalOcean droplet: [here](https://www.andrewmunsell.com/blog/dokku-tutorial-digital-ocean), [here](https://www.digitalocean.com/community/tutorials/how-to-use-the-digitalocean-dokku-application), and [here](https://www.digitalocean.com/community/tutorials/how-to-use-the-dokku-one-click-digitalocean-image-to-run-a-node-js-app).

Part of my work involves creating dynamic, web-based visualizations of organizational networks.  The data that is used to generate these network diagrams comes from a variety of sources: questionnaires, behavioural traces (including the behaviour of participants in online discussion forums from learning management systems like Sakai or Moodle).  I enjoy the work, but I was missing a good deployment workflow and decided to see if I could use dokku to help.  The short answer: yes!

In the past I had used Apache as a web server to serve up static web pages.  I knew that that was a relatively heavyweight solution so I decided to use this opportunity to try out Node.js/Express.  The nice part about going that route (pun intended) is that there are a good number of tutorials on how to get a Node.js application deployed using dokku (see the above links).

Before showing examples of the various configuration files, let me share a bit more about the visualization workflow.  Getting the data from another system is somewhat of an onerous task.  Whereas I can describe the data format that I want, it's often beyond the programmers at "the other end" to actually assemble that data, so I wind up specifying a generic set of data that I then wrangle and reduce into the correct format.  This lends itself to a scaling problem:  if several hundred users are pounding the visualization tool, it's simply too expensive to call out to the native system for the raw data.  It's also not absolutely necessary to have completely up-to-date data:  being 5 or 10 minutes behind is completely acceptable.  To date, therefore, I typically wrote a tiny python script that would retrieve that raw data, wrangle it, write it out to a data file.  Think 'CGI'.

This represented the biggest challenge for me in my exploration of dokku, but it was solvable!

Let me start by describing the end result:  a dokku solution that is backed by a single-instance [MongoDB plugin](https://github.com/jeffutter/dokku-mongodb-plugin), with a MongoDB that is written to by a python process and read from via a Node.JS instance.  Uses [dokku-shoreman](https://github.com/statianzo/dokku-shoreman) to allow a non-web process to be specified in the Profile.  The python process runs periodically thanks to a distributed task queue ([celery](http://www.celeryproject.org/)), which is also backed by the same MongoDB instance. Because I'm using both python and  node.js, I had to figure out how to use [heroku-buildpack-multi](https://github.com/ddollar/heroku-buildpack-multi) to make it all work.

To understand how to get MongoDB working with Node.js, check out the great tutorials by [Christopher Buecheler](http://cwbuecheler.com/) available [here](http://cwbuecheler.com/web/tutorials/2013/node-express-mongo/) and [here](http://cwbuecheler.com/web/tutorials/2014/restful-web-app-node-express-mongodb/).

In the end, here's what my Profile looked like:

[sourcecode]
worker: celery -A tasks worker --loglevel=info --beat
web: node app.js
[/sourcecode]

The python-related worker thread (tasks.py) looks more or less like this:

[sourcecode]
#!/usr/bin/env python

import json
import collections
import itertools
import urllib2
import os
import time
from pymongo import MongoClient
from celery import Celery
from celery.task import periodic_task
from datetime import timedelta
import logging

BROKER_URL = os.environ.get('MONGO_URL') or 'mongodb://localhost:27017/jobs';
app = Celery('tasks', broker = BROKER_URL)
app.config_from_object('celeryconfig')

logger = logging.getLogger('tasks_info')
logger.info(' * tasks');

@periodic_task(run_every=timedelta(minutes=5))
def get_external_data():
        logger.info('starting')
        logger.info(os.environ.get('BROKER_URL'))
        print "Retrieving data",time.strftime("%c")
        logger.info("Retrieving data")
        client = MongoClient(os.environ.get('MONGO_URL') or 'mongodb://localhost:27017')
        db = client[os.environ.get('MONGODB_DATABASE') or 'test1']
        collection = db['foobar']
# ... stuff deleted
                post = {'...'}
                collection.save(post)
        client.close()
        print "Retrieved data",time.strftime("%c")
        logger.info( "Retrieved data.")
[/sourcecode]

The @periodic_task line tells celery to run that method every 5 minutes. And every time that task is run, the contents of the MongoDB are replaced. Here's my celeryconfig.py file:

[sourcecode]
import os

CELERY_ACCEPT_CONTENT = ['pickle', 'json', 'msgpack', 'yaml']
host = os.environ.get('MONGODB_HOST') or "127.0.0.1"
port = os.environ.get('MONGODB_PORT') or 27017
database = os.environ.get('MONGODB_DATABASE') or "jobs"

CELERY_RESULT_BACKEND = "mongodb"
CELERY_MONGODB_BACKEND_SETTINGS = {
    "host": host,
    "port": port,
    "database": database,
    "taskmeta_collection": "stock_taskmeta_collection",
}
[/sourcecode]

Celery gets upset about various things, hence the need to specify CELERY_ACCEPT_CONTENT. It also gets upset that it's running as root, so you need to update your .profile.d/setenv.sh file so it contains the line:

[sourcecode]
# to run celery as root
export C_FORCE_ROOT=1
[/sourcecode]

Always remember to use [virtualenv](http://docs.python-guide.org/en/latest/dev/virtualenvs/) when developing python code that will be deployed somewhere else, which will then allow you to do a 'pip freeze' to generate the contents of requirements.txt. Here's what mine looks like:

[sourcecode]
amqp==1.4.5
anyjson==0.3.3
billiard==3.3.0.17
celery==3.1.11
celery-with-mongodb==3.0
kombu==3.0.16
pymongo==2.7.1
pytz==2014.3
wsgiref==0.1.2
[/sourcecode]

And here's the actual series of shell commands that I used to set that up:

[sourcecode]
virtualenv venv
source venv/bin/activate
pip install celery
pip install pymongo
pip install -U celery-with-mongodb
pip freeze > requirements.txt
[/sourcecode]

On the node.js side of things the code is pretty close to what you see in the tutorials pointed to above:

[sourcecode]
var express = require('express');
var path = require('path');
var favicon = require('static-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
var compress = require('compression');

var mongo = require('mongodb');
var monk = require('monk');
var db = monk(process.env.MONGO_URL || 'localhost:27017/test1')

var livedata = require('./routes/livedata');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'jade');

app.use(favicon());
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded());
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'public')));
app.use(compress());

app.use(function(req,res,next) {
    req.db = db;
    next();
});

app.use('/livedata', livedata);

/// catch 404 and forwarding to error handler
app.use(function(req, res, next) {
    var err = new Error('Not Found');
    err.status = 404;
    next(err);
});

/// error handlers

// development error handler
// will print stacktrace
if (app.get('env') === 'development') {
    app.use(function(err, req, res, next) {
        res.status(err.status || 500);
        res.render('error', {
            message: err.message,
            error: err
        });
    });
}

// production error handler
// no stacktraces leaked to user
app.use(function(err, req, res, next) {
    res.status(err.status || 500);
    res.render('error', {
        message: err.message,
        error: {}
    });
});
app.set('port', (process.env.PORT || 5000));

app.listen(app.get('port'), function() {
  console.log("Node app is running at localhost:" + app.get('port'))
  console.log("MONGODB_DATABASE:" + process.env.MONGODB_DATABASE);
})

module.exports = app;
[/sourcecode]

In a lot of this code, you'll see the use of '||' (in the javascript stuff) and 'or' (in the python stuff). That's what allows me to use the same code in testing and production. The MongoDB plugin sets a bunch of environment variables, but I use a much simpler setup on my development machines where MongoDB is simply running normally.

The 'livedata' stuff above is the part that uses express to read the relevant data from the MongoDB. Here's the important part of the livedata.js file:

[sourcecode]
var express = require('express');
var router = express.Router();

/* GET data from db. */
router.get('/', function(req, res) {
  var db = req.db;
  var collection = db.get('foobar');
  collection.findOne({datakey:req.param("d")},function(e,docs) {
        if (docs) {
                res.send(docs.value);
        }
  })
});

module.exports = router;
[/sourcecode]

As I mentioned, because I use multiple buildpacks, I needed to create a .buildpacks file that looks something link this:

[sourcecode]
https://github.com/heroku/heroku-buildpack-python.git#94d311134
https://github.com/heroku/heroku-buildpack-nodejs.git
[/sourcecode]

Note that I needed to specify an earlier version of the python buildpack: the most up-to-date python buildpack didn't work with the DigitalOcean dokku droplet.

And finally, here's what my package.json file looks like:

[sourcecode]
{
  "name": "AwesomeApp",
  "version": "0.1.0",
  "private": true,
  "engines": {
    "node": "0.10.x"
  },
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "express": "~4.0.0",
    "compression": "*",
    "static-favicon": "~1.0.0",
    "morgan": "~1.0.0",
    "cookie-parser": "~1.0.1",
    "body-parser": "~1.0.0",
    "debug": "~0.7.4",
    "jade": "~1.3.0",
    "mongodb": "*",
    "monk": "*"
  }
}
[/sourcecode]

What this allows me to do is to use git to handle my local revisions, and then to push the appropriate version to whatever number of remote dokku instances I want.  This allows me, for example, to work on a development branch, quickly switch to the production branch to implement a quick fix, deploy that fix, then switch back to the development branch, finish my work, and merge those changes into the master branch right before pushing it out to my dokku instances.  It was the first time I actually felt like I was using branching and merging in git as it is [meant to be used](http://git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging)!

Here's what the output from a typical push to the dokku instance looks like:

[sourcecode]
$ git push dokkuone master
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 365 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
-----> Cleaning up ...
-----> Building zon ...
remote: Cloning into '/tmp/tmp.zuKTAzx168'...
remote: done.
remote: HEAD is now at 097b997... Things seem to be working; change to a more reasonable logging level
       Multipack app detected
=====> Downloading Buildpack: https://github.com/heroku/heroku-buildpack-python.git
=====> Detected Framework: Python
-----> No runtime.txt provided; assuming python-2.7.6.
-----> Using Python runtime (python-2.7.6)
-----> Installing dependencies using Pip (1.5.4)
       Cleaning up...
=====> Downloading Buildpack: https://github.com/heroku/heroku-buildpack-nodejs.git
=====> Detected Framework: Node.js
-----> Requested node range:  0.10.x
-----> Resolved node version: 0.10.28
-----> Downloading and installing node
-----> Found existing node_modules directory; skipping cache
-----> Rebuilding any native dependencies

       > bson@0.2.8 install /build/app/node_modules/mongodb/node_modules/bson
       > (node-gyp rebuild 2> builderror.log) || (exit 0)

       make: Entering directory `/build/app/node_modules/mongodb/node_modules/bson/build'
         CXX(target) Release/obj.target/bson/ext/bson.o
         SOLINK_MODULE(target) Release/obj.target/bson.node
         SOLINK_MODULE(target) Release/obj.target/bson.node: Finished
         COPY Release/bson.node
       make: Leaving directory `/build/app/node_modules/mongodb/node_modules/bson/build'

       > kerberos@0.0.3 install /build/app/node_modules/mongodb/node_modules/kerberos
       > (node-gyp rebuild 2> builderror.log) || (exit 0)

       make: Entering directory `/build/app/node_modules/mongodb/node_modules/kerberos/build'
         SOLINK_MODULE(target) Release/obj.target/kerberos.node
         SOLINK_MODULE(target) Release/obj.target/kerberos.node: Finished
         COPY Release/kerberos.node
       make: Leaving directory `/build/app/node_modules/mongodb/node_modules/kerberos/build'

       > bson@0.2.7 install /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/bson
       > (node-gyp rebuild 2> builderror.log) || (exit 0)

       make: Entering directory `/build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/bson/build'
         CXX(target) Release/obj.target/bson/ext/bson.o
         SOLINK_MODULE(target) Release/obj.target/bson.node
         SOLINK_MODULE(target) Release/obj.target/bson.node: Finished
         COPY Release/bson.node
       make: Leaving directory `/build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/bson/build'

       > kerberos@0.0.3 install /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/kerberos
       > (node-gyp rebuild 2> builderror.log) || (exit 0)

       make: Entering directory `/build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/kerberos/build'
         SOLINK_MODULE(target) Release/obj.target/kerberos.node
         SOLINK_MODULE(target) Release/obj.target/kerberos.node: Finished
         COPY Release/kerberos.node
       make: Leaving directory `/build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/kerberos/build'
       express@4.0.0 /build/app/node_modules/express
       parseurl@1.0.1 /build/app/node_modules/express/node_modules/parseurl
       accepts@1.0.0 /build/app/node_modules/express/node_modules/accepts
       mime@1.2.11 /build/app/node_modules/express/node_modules/accepts/node_modules/mime
       negotiator@0.3.0 /build/app/node_modules/express/node_modules/accepts/node_modules/negotiator
       type-is@1.0.0 /build/app/node_modules/express/node_modules/type-is
       mime@1.2.11 /build/app/node_modules/express/node_modules/type-is/node_modules/mime
       range-parser@1.0.0 /build/app/node_modules/express/node_modules/range-parser
       cookie@0.1.0 /build/app/node_modules/express/node_modules/cookie
       buffer-crc32@0.2.1 /build/app/node_modules/express/node_modules/buffer-crc32
       fresh@0.2.2 /build/app/node_modules/express/node_modules/fresh
       methods@0.1.0 /build/app/node_modules/express/node_modules/methods
       send@0.2.0 /build/app/node_modules/express/node_modules/send
       debug@0.7.4 /build/app/node_modules/debug
       mime@1.2.11 /build/app/node_modules/express/node_modules/send/node_modules/mime
       cookie-signature@1.0.3 /build/app/node_modules/express/node_modules/cookie-signature
       merge-descriptors@0.0.2 /build/app/node_modules/express/node_modules/merge-descriptors
       utils-merge@1.0.0 /build/app/node_modules/express/node_modules/utils-merge
       escape-html@1.0.1 /build/app/node_modules/express/node_modules/escape-html
       qs@0.6.6 /build/app/node_modules/express/node_modules/qs
       serve-static@1.0.1 /build/app/node_modules/express/node_modules/serve-static
       send@0.1.4 /build/app/node_modules/express/node_modules/serve-static/node_modules/send
       mime@1.2.11 /build/app/node_modules/express/node_modules/serve-static/node_modules/send/node_modules/mime
       fresh@0.2.0 /build/app/node_modules/express/node_modules/serve-static/node_modules/send/node_modules/fresh
       range-parser@0.0.4 /build/app/node_modules/express/node_modules/serve-static/node_modules/send/node_modules/range-parser
       path-to-regexp@0.1.2 /build/app/node_modules/express/node_modules/path-to-regexp
       compression@1.0.2 /build/app/node_modules/compression
       bytes@0.3.0 /build/app/node_modules/compression/node_modules/bytes
       negotiator@0.4.3 /build/app/node_modules/compression/node_modules/negotiator
       compressible@1.0.1 /build/app/node_modules/compression/node_modules/compressible
       static-favicon@1.0.2 /build/app/node_modules/static-favicon
       morgan@1.0.0 /build/app/node_modules/morgan
       bytes@0.2.1 /build/app/node_modules/morgan/node_modules/bytes
       cookie-parser@1.0.1 /build/app/node_modules/cookie-parser
       cookie@0.1.0 /build/app/node_modules/cookie-parser/node_modules/cookie
       cookie-signature@1.0.3 /build/app/node_modules/cookie-parser/node_modules/cookie-signature
       body-parser@1.0.2 /build/app/node_modules/body-parser
       type-is@1.1.0 /build/app/node_modules/body-parser/node_modules/type-is
       mime@1.2.11 /build/app/node_modules/body-parser/node_modules/type-is/node_modules/mime
       raw-body@1.1.4 /build/app/node_modules/body-parser/node_modules/raw-body
       bytes@0.3.0 /build/app/node_modules/body-parser/node_modules/raw-body/node_modules/bytes
       qs@0.6.6 /build/app/node_modules/body-parser/node_modules/qs
       jade@1.3.1 /build/app/node_modules/jade
       commander@2.1.0 /build/app/node_modules/jade/node_modules/commander
       mkdirp@0.3.5 /build/app/node_modules/jade/node_modules/mkdirp
       transformers@2.1.0 /build/app/node_modules/jade/node_modules/transformers
       promise@2.0.0 /build/app/node_modules/jade/node_modules/transformers/node_modules/promise
       is-promise@1.0.0 /build/app/node_modules/jade/node_modules/transformers/node_modules/promise/node_modules/is-promise
       css@1.0.8 /build/app/node_modules/jade/node_modules/transformers/node_modules/css
       css-parse@1.0.4 /build/app/node_modules/jade/node_modules/transformers/node_modules/css/node_modules/css-parse
       css-stringify@1.0.5 /build/app/node_modules/jade/node_modules/transformers/node_modules/css/node_modules/css-stringify
       uglify-js@2.2.5 /build/app/node_modules/jade/node_modules/transformers/node_modules/uglify-js
       source-map@0.1.33 /build/app/node_modules/jade/node_modules/transformers/node_modules/uglify-js/node_modules/source-map
       amdefine@0.1.0 /build/app/node_modules/jade/node_modules/transformers/node_modules/uglify-js/node_modules/source-map/node_modules/amdefine
       optimist@0.3.7 /build/app/node_modules/jade/node_modules/transformers/node_modules/uglify-js/node_modules/optimist
       wordwrap@0.0.2 /build/app/node_modules/jade/node_modules/transformers/node_modules/uglify-js/node_modules/optimist/node_modules/wordwrap
       character-parser@1.2.0 /build/app/node_modules/jade/node_modules/character-parser
       monocle@1.1.51 /build/app/node_modules/jade/node_modules/monocle
       readdirp@0.2.5 /build/app/node_modules/jade/node_modules/monocle/node_modules/readdirp
       minimatch@0.2.14 /build/app/node_modules/jade/node_modules/monocle/node_modules/readdirp/node_modules/minimatch
       lru-cache@2.5.0 /build/app/node_modules/jade/node_modules/monocle/node_modules/readdirp/node_modules/minimatch/node_modules/lru-cach
       sigmund@1.0.0 /build/app/node_modules/jade/node_modules/monocle/node_modules/readdirp/node_modules/minimatch/node_modules/sigmund
       with@3.0.0 /build/app/node_modules/jade/node_modules/with
       uglify-js@2.4.13 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js
       async@0.2.10 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/async
       source-map@0.1.33 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/source-map
       amdefine@0.1.0 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/source-map/node_modules/amdefine
       optimist@0.3.7 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/optimist
       wordwrap@0.0.2 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/optimist/node_modules/wordwrap
       uglify-to-browserify@1.0.2 /build/app/node_modules/jade/node_modules/with/node_modules/uglify-js/node_modules/uglify-to-browserify
       constantinople@2.0.0 /build/app/node_modules/jade/node_modules/constantinople
       uglify-js@2.4.13 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js
       async@0.2.10 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/async
       source-map@0.1.33 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/source-map
       amdefine@0.1.0 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/source-map/node_modules/amdefine
       optimist@0.3.7 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/optimist
       wordwrap@0.0.2 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/optimist/node_modules/wordwrap
       uglify-to-browserify@1.0.2 /build/app/node_modules/jade/node_modules/constantinople/node_modules/uglify-js/node_modules/uglify-to-browserify
       mongodb@1.4.5 /build/app/node_modules/mongodb
       bson@0.2.8 /build/app/node_modules/mongodb/node_modules/bson
       nan@1.0.0 /build/app/node_modules/mongodb/node_modules/bson/node_modules/nan
       kerberos@0.0.3 /build/app/node_modules/mongodb/node_modules/kerberos
       readable-stream@1.0.27-1 /build/app/node_modules/mongodb/node_modules/readable-stream
       core-util-is@1.0.1 /build/app/node_modules/mongodb/node_modules/readable-stream/node_modules/core-util-is
       isarray@0.0.1 /build/app/node_modules/mongodb/node_modules/readable-stream/node_modules/isarray
       string_decoder@0.10.25-1 /build/app/node_modules/mongodb/node_modules/readable-stream/node_modules/string_decoder
       inherits@2.0.1 /build/app/node_modules/mongodb/node_modules/readable-stream/node_modules/inherits
       monk@0.9.0 /build/app/node_modules/monk
       mongoskin@1.4.1 /build/app/node_modules/monk/node_modules/mongoskin
       mongodb@1.4.1 /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb
       bson@0.2.7 /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/bson
       nan@0.8.0 /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/bson/node_modules/nan
       kerberos@0.0.3 /build/app/node_modules/monk/node_modules/mongoskin/node_modules/mongodb/node_modules/kerberos
       mpromise@0.5.1 /build/app/node_modules/monk/node_modules/mpromise
       compress@0.1.9 /build/app/node_modules/compress
-----> Writing a custom .npmrc to circumvent npm bugs
-----> Installing dependencies
-----> Caching node_modules directory for future builds
-----> Cleaning up node-gyp and npm artifacts
-----> Building runtime environment
       Using release configuration from last framework Node.js:
       addons: []
       default_process_types: {}
-----> Discovering process types
       Procfile declares types -> worker, web
-----> Releasing zon ...

-----> foobar linked to jeffutter/mongodb container
-----> Injecting Shoreman ...
-----> Deploying foobar ...
=====> Application deployed:
       http://foobar.some.where.overtherainbow.com

To dokku@some.where.overtherainbow.com:foobar
   0e4dea4..097b997  master -> master
[/sourcecode]

So that's about all there is to it. It's kind of nice being able to use a system that weaves together simple things like virtualenv, git, Docker, and buildstep to create a really powerful deployment system that allows me to focus on application development and not on operations! Thank you, [Jeff Lindsay](http://progrium.com/blog/)! As always, feel free to leave a comment or contact me directly if you have any questions.
