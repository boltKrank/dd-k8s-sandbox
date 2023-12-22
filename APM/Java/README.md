# Java app on k8s APM example

This app is designed to deliberatly consume resoureces so we can make sure the metrics are being reflected properly.

Build with Spinrg-boot, we can edit the methods used to fine-tune the resource consumption, but in general it's recommended to use the web interface to control this.

## Apps

Webconsumer: this is a web-app that will take input from the web portal and run certain tasks deliberatly designed to consume resources. This can possibly be dangerous and should only be used locally - so JDK can be killed if it goes overboard.