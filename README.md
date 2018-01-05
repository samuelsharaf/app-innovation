Heroku App Innovation Workshop

Introduction

Welcome to the Heroku App Innovation Workshop. This workshop is intended for developers and architects with hands-on exercises. The goal of this workshop is to provide you a good overall understanding of Heroku as a managed platform for app deployment, scaling and extending the app, and how it integrates with the Salesforce Sales and Service clouds. 

**High Level Agenda**

* 8:30am | Intro + Heroku Overview
* 9:30am | App Exercise Discussion
* 10:30am | Trailhead Trivia
* 11:00am | App Deployment in Teams
* 12:00pm | Working Lunch
* 2:00pm | Use Case Discussion
* 3:15pm | Team Presentations + Prizes

* * *

Heroku App Innovation Workshop Prerequisites

Required Online Accounts (mandatory)

* Sign up for Heroku free account at https://signup.heroku.com (https://signup.heroku.com/)
* Create a free github account at https://github.com/join
* If using windows laptop (not mac or linux) sign up for Cloud9IDE at https://c9.io/signup
* Create a free Salesforce Developer Account at https://developer.salesforce.com/signup

Required Pre-work (not mandatory)

* Complete the Salesforce trails which will get you started with the basics of deploying apps on Heroku at https://trailhead.salesforce.com/en/trails/heroku_enterprise

Training Org

Once we have all of the the prerequisites installed and set up, you’ll need to let the trainer know your account email address. They will add you to the training org which will be used for app deployment and exercises.
* * *

5 Minute Break

* * *

One slide overview of Heroku Architecture (20 minutes)

https://goo.gl/c4T1A8

* * *

App Exerises

For the app exercises, we will be using a simple Node.JS app (https://github.com/heroku/app-innovation). Note that this app is forked from heroku NodeJS getting started app with a few changes for this workshop.


1 - Fork the Github Repository and Setup Dev Environment

* Fork the Github repo (https://github.com/heroku/app-innovation) to your github account (via github dashboard).
* On Cloud9 console clone the repo e.g. if you are cloning from heroku/app-innovation you will use the following command:

git clone https://github.com/heroku/app-innovation

* Once the cloud9 repo is created, Run this command:

sh setup.sh && . ~/.profile

* Note that if using Mac or Linux laptops, Cloud9 is not mandatory but highly recommended.

2 - Deploy to Heroku

From the command-line in Cloud9 run the following commands:

heroku login
heroku create --app <appName> --team <teamName> (please replace appName with the app name you want and the team name with the assigned team)
git push heroku master

Open your Heroku app and you should see the app running

3 - Extending the App with Logging add-on

Extend the app with logging add-on

heroku addons:create papertrail
heroku addons:open papertrail

4 - Extending the App with Postgres DB

In this step you will add a free Heroku Postgres Starter Tier dev database to your app.

heroku addons:create heroku-postgresql:hobby-dev

Edit your package.json file to add the pg npm module to your dependencies:

"dependencies": {
    "pg": "6.x",
    "ejs": "2.5.6",
    "express": "4.15.2",
    "cool-ascii-faces": "1.3.4"
}

* Now edit your index.js file to use this module to connect to the database specified in your DATABASE_URL environment variable:

var pg = require('pg');

Copy paste the code below in the body of index.js

app.get('/db', function (request, response) {
  pg.connect(process.env.DATABASE_URL, function(err, client, done) {
    client.query('SELECT * FROM test_table', function(err, result) {
      done();
      if (err)
       { console.error(err); response.send("Error " + err); }
      else
       { response.render('pages/db', {results: result.rows} ); }
    });
  });
});

* This ensures that when you access your app using the /db route, it will return all rows in the test_table table.
* Create a table and insert a record

1. heroku pg:psql

2. create table test_table (id integer, name text);

3. insert into test_table values (1, 'hello database');

4. \q

4b - Add Heroku Connect

Add Heroku Connect (Bi-directional sync engine between Heroku Postgres and Salesforce (sales and service clouds).

heroku addons:create herokuconnect

Click on Heroku Connect in the dashboard and provision the connection to the Salesforce DEV Org.

* Map a few objects and corresponding fields
* Enable the streaming API for Sync from Salesforce to Heroku Postgres

Bonus -- Once the data is synced to database, modify the database code in previous step to output the results from Salesforce

Create a new route in index.js name it salesforce

5 - Monitoring: Exercise

Offense

You are a hacker. You live in eastern hackistan. You have a contract with EvilCorp to take down a site that competes with them. Your mission is to take down WellCo! The discovery phase of your attack has already been completed. A colleague of yours has identified several HTTP endpoints that seem to have weaknesses:

* /messages
* /clocks
* /rockets
* /beverages
* /maps
* /pipes
* /bins
* /handles
* /children

Your task is to pick one of these endpoints, and attack it with everything you have. Try to take down WellCo:

artillery quick --duration 60 --rate 10 -n 20 http://my.app.dev/api/resource

If and when the WellCo engineer can identify the endpoint you are attacking, they will block it, and your attack will be thwarted.

Defense

You are a dev ops engineer at WellCo - a company that makes products that work well. You are on call, supporting an app that has been deployed to production. Unfortunately, this app has gained the reputation for being "the problem app". And now it's 23:30 on a Friday, and you get an SMS message.
The "problem app" is acting up again. You suspect the site may even be under denial-of-service attack.
But how do you identify the vulnerability? Use your knowledge of Logplex and NewRelic to decide which endpoint is being attacked! Once you've determined that, try to form a hypothesis as to which resource is being used up by the endpoint by inspecting New Relic and Logplex.

Turn About is Fair Play

After you have successfully attacked and defended an end-point, switch roles! WellCo should attack EvilCorp. Pick a different endpoint, and work your way through until you can identify the signature of each attack, using Logplex and NewRelic. Happy hacking!

Exercise

* Run an artillery command
* Observe the rps (requests per second)
* Scale the app out (more dynos)
* Re-run the artillery command
* Observe the rps (requests per second) is higher

Don't forget to scale down at the end of the day!

6 - Heroku CI/CD and Review Apps

Setup the Pipeline

* Open the app
* Go to "Deploy"
* Click "New Pipeline..."
* Click "Create Pipeline"
* Click on the menu for the app and select "Move app to staging"

Setup CI

* Click on "Tests"
* Click on "Connect to Github"
* Search for heroku-innovation
* Click "Connect"
* Click "Enable Heroku CI"
* Click the "Tests" tab
* Click "+ New Test"
* Click "Start Test Run"

Continuous Deployment

* Click on the app menu for the staging app
* Click "Configure Automatic Deploys"
* Check "Wait for CI to pass before deploy"
* Click "Enable Automatic Deploys"

Review Apps

* Click "Enable Review Apps..."
* Check "Create New..." and check "Destroy stale..."
* Click "Enable"

View a Review App in Action

* In Github go to your "Code" tab of your repo
* Select a file e.g. views/pages/index.ejs and make a simple change like "Welcome to the Heroku Workshop"
* Click "New Pull Request"
* Click "Create Pull Request"
* In Heroku you should see that a new Review App will be created

NOTE: Tests will *not* run because for this demo you haven't created a new commit. The tests would have been run when this branch was pushed in real life.

* Open the URL and make sure things look good
* Go to Github and merge the pull request
* The tests should pass and it should auto-deploy

And that's a real-life review-app -> CI -> CD scenario!

Viewing an error

* Create a new pull request from master -> pr-2-task-create-test-broken

This branch contains a test that will pass

* You'll see that it creates a review app - you can ignore this
* Manually trigger a test build

NOTE: in production developers would be pushing code, and these would all be automatic. But for this workshop we don't want you to write a lot of code...

* Merge this broken code to master
* You'll see that the staging app is *not* rolled in
* Merge pr-3-task-create-works into master (this fixes it)
* You'll see that staging gets auto-deployed

