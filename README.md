# Rails-Delayed-Job-with-AWS-EBS-on-Linux-EC2-Instances

1. 
- please make sure delayed_job bin file is there in your applications directory
- path: APP_ROOT/bin/delayed_job

2. 
- create a new folder called: .ebextensions

3.
- put below script inside
- filename: delayed_job.config

```
files:
  "/opt/elasticbeanstalk/hooks/appdeploy/post/99_restart_delayed_job.sh":
    mode: "000755"
    owner: root
    group: root
    content: |
      #!/usr/bin/env bash
      # Using similar syntax as the appdeploy pre hooks that is managed by AWS

      # Loading environment data
      EB_SCRIPT_DIR=$(/opt/elasticbeanstalk/bin/get-config container -k script_dir)
      EB_SUPPORT_DIR=$(/opt/elasticbeanstalk/bin/get-config container -k support_dir)
      EB_APP_USER=$(/opt/elasticbeanstalk/bin/get-config container -k app_user)
      EB_APP_CURRENT_DIR=$(/opt/elasticbeanstalk/bin/get-config container -k app_deploy_dir)
      EB_APP_PIDS_DIR=$(/opt/elasticbeanstalk/bin/get-config container -k app_pid_dir)

      # Setting up correct environment and ruby version so that bundle can load all gems
      . $EB_SUPPORT_DIR/envvars
      . $EB_SCRIPT_DIR/use-app-ruby.sh

      # Now we can do the actual restart of the worker. Make sure to have double quotes when using env vars in the command.
      # For Rails 4, replace script/delayed_job with bin/delayed_job
      cd $EB_APP_CURRENT_DIR
      su -s /bin/bash -c "bundle exec bin/delayed_job --pid-dir=$EB_APP_PIDS_DIR restart" $EB_APP_USER
```

4. 
- do deployment, either using eb cli or through console

5.
- access your EBS EC2 instances to make sure your delayed_job is running using command

```
ps aux | grep delayed_job
```
