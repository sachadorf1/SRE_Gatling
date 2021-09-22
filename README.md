- [Set Up](#set-up)
  - [Prerequisites](#prerequisites)
  - [Installing Gatling](#installing-gatling)
  - [Verify Installation](#verify-installation)
  - [Verify Installation - Gatlin Runner](#verify-installation---gatlin-runner)
  - [Verify Installation - Gatlin Recorder](#verify-installation---gatlin-recorder)
- [Launching App from Autoscaling Group with your Database](#launching-app-from-autoscaling-group-with-your-database)
- [Record visit to each page on the app](#record-visit-to-each-pageon-the-app)
- [Run a test](#run-a-test)
- [Run your tests with 10 x more visits each time](#run-your-tests-with-10-x-more-visits-each-time)
- [Using HTTP](#using-http)
## Set Up
### Prerequisites
- IntelliJ 
- Java 8+
- Scala plugin in IntelliJ (In Intellij 'Ctrl + Alt + S' to go into Plugin window and install Scala)

### Installing Gatling
https://www.blazemeter.com/blog/how-to-install-gatling-on-windows

- Download Gatling via https://gatling.io/open-source/ by pressing `Download Now`
- Either unzip the folder within your file explorer or use the command
`unzip gatling-charts-highcharts-bundle-2.3.0-bundle.zip`
- In Windows, `Edit the system environment variables` -> `Advanced` -> `Environment Variables...`
- Under System variables, click `New...`
  - Variable Name: GATLING_HOME
  - Variable value: the directory of the unzipped folder (e.g. C:\Users\YourName\gatling-charts-highcharts-bundle-3.6.1)
- Under User variables for Sacha, click `Path` -> `Edit...`
  - Add the path of the bin directory of your gatling folder (e.g.C:\Users\YourName\gatling-charts-highcharts-bundle-3.6.1\bin)

### Verify Installation
https://www.blazemeter.com/blog/gatling-installation-verification-and-configuration-the-ultimate-guide

To check if Gatlin is working properly, run the Gatling tests runner or the Gatling recorder:
- Go to the bin directory inside your gatling folder (where you unzipped your installation)

`cd gatling-charts-highcharts-bundle-3.6.1/bin`

### Verify Installation - Gatlin Runner

- Run the following command

`./gatling.bat`

- Should say GATLING_HOME is set to... and then ask you to enter a number between 0 and 5
- Enter 0 -> Enter nothing
- Should say:
'Reports generated in 0s.
Please open the following file: (path to an index.html file)'
- Put this link into your browser to see the results of the test

![](img/Gatling_tests_runner_browser.png)  

- Now you have verified that Gatling is working properly

### Verify Installation - Gatlin Recorder

- Run the following command

`./recorder.bat`

- Should say GATLING_HOME is set to... and open a window 'Gatling Recorder - Configuration':

![](img/Gatling_recorder_Config_Window.jpg)
- Now you have verified that Gatling is working properly

## Launching App from Autoscaling Group with your Database

- In the security group for your db, change the app ip to the app instance you launched with your autoscaling group

- SSH into your app instance
```
cd app
export DB_HOST=<DB_IP>:27017/posts
node seeds/seed.js
npm start
```

## Record visit to each page on the app
- Go onto the page for your app
- Right click anywhere on the page and click `Inspect`
- Click on `Network` (1 in diagram)
- Tick `Preserve log` (2 in diagram)
- Click on the no/prohibition sign (clear) to clear the history (3 in diagram)
- Now, go onto the posts page (by adding /posts to the end of your app ip)

![](img/Gatling_Inspect_Network.jpg)

- Click on the down arrow to export the HAR file (4 in diagram)
- Save it in your C folder and give it an appropriate name (e.g. sacha_app_posts_recorder.har)

## Run a test
- In the bin folder in your Gatling folder, run `recorder.bat`
```
cd bin
cd recorder.bat
```
- This will open a window 'Gatling Recorder - Configuration'
- Change Recorder Mode to `HAR Converter`
- Import the HAR file you saved (click `Browse`)
- Enter a Class Name: e.g. AppPostsTest
- CLick `Start`
![](img/Gatling_recorder_Config_Window_HAR_Converter.jpg)

- In the terminal, run `gatling.bat` to run the test
- Enter the number of the test you want to run (e.g. AppPostsTest)
- Select run description: Create a name for this test (e.g. Sacha App Posts Test 1 User)
- It will show the test info and say 'Please open the following file'
- Copy this file into your browser and you can see the results in a lovely user friendly format

## Run your tests with 10 x more visits each time
- In your gatling file directory:
  - `user-files` -> `simulations` -> Open your test file (e.g. AppPostsTest)
- In AppPostsTest, go to the end of the script to:

`setUp(scn.inject(atOnceUsers(1))).protocols(httpProtocol)`

- Increase the atOnceUsers value by a factor of 10, e.g.

`setUp(scn.inject(atOnceUsers(10))).protocols(httpProtocol)`

- Run `gatling.bat` again
- Select the same test as before
- Select run description: Create a different name for this test (e.g. Sacha App Posts Test 10 User)
- It will show the test info and say 'Please open the following file'
- Copy this file into your browser and you can see the results in a lovely user friendly format

- Repeat these steps, increasing the atOnceUsers value by a factor of 10 each time to see how the results change.

## HTTP Proxy

In the terminal:
- `recorder.bat`
- Recorder Mode: HTTP Proxy
- Enter Class Name
- Start
- Gatlin Recorder - Running... window should appear

In Windows:
- Go into `Network and Internet Settings`
- Proxy
- Manual Proxy Setup: On
- Address: http:/localhost
- Port 8000
- Save
- Check it has saved

![](img/Manual_Proxy_Setup_Window.png)

In Gatlin recorder:
- Save and close

## CloudWatch metrics
What are you monitoring with cloud i.e. Network Load, CPU %, memory etc (google what should we be monitoring for INTERFACING WEB APP) then monitor those factors and save logs - create events on cloud watch to trigger ASG with ALB
Create a diagram of the entire infrastructure
A-Z

https://dzone.com/articles/how-to-choose-monitoring-tools-for-devops-and-sre

To achieve Highly Availability and Scalability 

## What are we monitoring? 

### Resource Monitoring (CPU, Network< Memory etc)
- Monitoring Infrastructure e.g. RAM, CPU, remaining disk space
### Network Monitoring
- Captures incoming requests, outgoing responses across everything (switches, firewalls, servers etc)
### Application Performance Monitoring
- Collect data on how the overall service is performing
- Sends requests to the service and tracks speed, completeness of the response
- Ensures services perform at expected levels
### Any Third-party resources/component 
- Monitoring the health and availability of third-party components in your architecture
![](img/autoscalinggroup_loadbalancer_diagram.png)
### Scaling Out
- Creates new instances in a particular scenario (e.g. if CPU Utilisation is over 50%)
### Scaling In
- Removes instances in a particular scenario (e.g. if CPU Utilisation is under 50% for a certain period of time)
### Scaling Up
- Increases the size of the instance (e.g. from t2.micro to t2.medium)
### Scaling Down
- Decreases the size of the instance (e.g. from t2.medium to t2.micro)

## Dynamic Scaling Policies with CloudWatch Alarms
https://docs.aws.amazon.com/autoscaling/ec2/userguide/as-scale-based-on-demand.html

- Set up a dynamic scaling policy for
  - CPU Utilisation
  - Network In
  - Request Count per target

![](img/dynamic_scaling_policies_1.png)
![](img/dynamic_scaling_policies_2.png)

I used Terraform to set these up so see [Terraform documentation](https://github.com/sachadorf1/IAC_Terraform) for how to set these up via Terraform or you can set them up in AWS.

- create or use metrics and Graphs
- create a Dash board of key service health 
- create logs of events

In CloudWatch:
- Dashboards
- Create dashboard (name e.g. SRE_sacha_dashboard)
- Add widget
  - Line -> Next
  - Metrics -> Configure
  - EC2
  - By Auto Scaling Group
  - Search for your auto scaling group
  - Metric Name: CPUUtilization, NetworkIn

![](img/CloudWatchDashboard.png)

# AWS Simple Notification Service (SNS)

![](img/Amazon-SNS_event_diagram.png)
![](img/sns_a2a_a2p_diagram.jpg)

### SNS A2A - Application to Application


### SNS A2P - Application to Person

See [documentation on Monitoring](https://github.com/sachadorf1/SRE_Monitoring#sns-notifications)

- Click on Auto Scaling Groups on the side tab under Auto Scaling
- Select the Auto Scaling Group you just created
- In Activity, in Activity notifications click Create notification
- Create a topic
- Name: SRE_sacha_autoscale_notification
- Recipients: your email address
- Tick the ones you want to be notified for
- Click Create Notification
- You should recieve a confirmation email
- Go into your email and click Confirm Subscription

# Simple Queue Service (SQS)
- More organised, simple
- Whatever goes wrong, it will send the notification





SQS? 
Create a Diagram for each service and the entire process of making your App highly available and scalable 
