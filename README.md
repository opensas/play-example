Play framework on OpenShift Express
============================

This git repository helps you get up and running quickly with a Play framework installation
on OpenShift Express.


Running on OpenShift
----------------------------

Create an account at http://openshift.redhat.com/

Create a raw (do-it-yourself) application

    rhc app create -a play -t raw-0.1

Add this upstream play-example repo

    cd play
    git remote add upstream -m master https://github.com/opensas/play-example.git
    git pull -s recursive -X theirs upstream master
    
Then push the repo upstream

    git push

That's it, you can now checkout your application at:

    http://play-$yournamespace.rhcloud.com

Now it's a good time to change your application.name setting in conf/application.conf to match your application

Working with a mysql database
----------------------------

Just issue

    rhc app cartridge add -a play -c mysql-5.1

Don't forget to write down the credentials.

Then uncomment the following lines from your conf/application.conf

    # openshift mysql database
    %openshift.db.url=jdbc:mysql://${OPENSHIFT_DB_HOST}:${OPENSHIFT_DB_PORT}/${OPENSHIFT_APP_NAME}
    %openshift.db.driver=com.mysql.jdbc.Driver
    %openshift.db.user=admin
    %openshift.db.pass=<write your password here>

You can manage your new MySQL database by embedding phpmyadmin-3.4.

    rhc app cartridge add -a play -c phpmyadmin-3.4

It's also a good idea to create a diferent user with limited privileges con the database

Updating your application
----------------------------

To deploy your changes to openshift just add your changes to the index, commit and push

    git add . -A

    git commit -m "a nice message"

    git push origin

Working with modules
----------------------------

Dont't forget to issue 

    play deps --forceCopy

Before add your changes to the index

Trouble shooting
----------------------------

To find out what's going on in openshift, issue

    rhc app tail -a play

Having a look under the hood
----------------------------

.openshift/action_hooks/pre_build does the following everytime you push changes

* reads play version from openshift.play.version at application.conf (1.2.4 by default)

* check it the desired version is installed, if not it downloads and installs play framework at $OPENSHIFT_DATA_DIR

* removes any other play framework version

then .openshift/action_hooks/start goes like this

* it executes .openshift/action_hooks/stop to stop the application

* cleans environment and update dependencies using openshift.deps.params for play deps parameters (uses "--forProd --clearcache" by default)

    play clean
    play deps $DEPS_PARAMS -Divy.hom=/tmp/ivy2

* finally it starts the application, using openshift.id (by default the configuration id to use is openshift). You can specify additional parameters with openshift.play.params.

    play start --%ID $PLAY_PARAMS

By default play will run in production mode, you can change it setting %openshift.application.mode=dev in application.conf. The server will listen to ${OPENSHIFT_INTERNAL_PORT} at ${OPENSHIFT_INTERNAL_IP}.

* .openshift/action_hooks/stop just tries to kill the server.pid process, and then checks that no "java" process is running. If it's there, it tries five times to kill it nicely, and then if tries another five times to kill it with -SIGKILL.

Acknowledgments
----------------------------

I couldn't have developed this quickstar without the help of marekjelen (https://github.com/marekjelen) who answered my questions on stackoverflow and who also shared his JRuby quickstart repo (https://github.com/marekjelen/openshift-jruby#readme).

It was also of great help Grant Shipley's article on building a quickstart for openshift (https://www.redhat.com/openshift/community/blogs/how-to-create-an-openshift-github-quick-start-project)

Play framework native support for openshift was a long awaited and pretty popular feature (you are still on time to vote for it at https://www.redhat.com/openshift/community/content/native-support-for-play-framework-application) So it's a great thing that Red Hat engineers came out with this simple and powerful solution, that basically let's you implement any server able to run on a linux box. Kudos to them!!!
