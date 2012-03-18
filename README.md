Play framework on OpenShift Express
============================

This git repository helps you get up and running quickly with a Play framework installation
on OpenShift Express.


Running on OpenShift
----------------------------

Create an account at http://openshift.redhat.com/

Create a ruby-1.8 application

    rhc app create -a play -t raw-0.1

Add this upstream sinatra repo

    cd play
    git remote add upstream -m master https://github.com/opensas/play-example.git
    git pull -s recursive -X theirs upstream master
    
Then push the repo upstream

    git push

That's it, you can now checkout your application at:

    http://play-$yournamespace.rhcloud.com

Working with a mysql database
----------------------------

Just issue

    rhc app cartridge add -a play -c mysql-5.1

Don't forget to write down the credentials.

Then uncomment the following lines from your conf/application.conf

    # openshift mysql database
    %openshift.db.url=jdbc:mysql://${OPENSHIFT_DB_HOST}:${OPENSHIFT_DB_PORT}/playdemo
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

    git commit -m "a nice commit"

    git push origin

Working with modules
----------------------------

Dont't forget to issue 

    play deps --forceCopy

Before add your changes to the index

Trouble shooting
----------------------------

