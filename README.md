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

