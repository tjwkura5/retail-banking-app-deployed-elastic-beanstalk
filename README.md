# Retail Banking App Deployed to Elastic Beanstalk

## Purpose

**CI/CD** stands for Continuous Integration and Continuous Delivery/Deployment. It's a software development methodology that helps teams deliver code changes more frequently and reliably. This project provides a hands-on learning experience and introduction to setting up a CI/CD pipeline for deploying an application to AWS Elastic Beanstalk.

**Understanding CI/CD:**

* **Continuous Integration (CI):**
    * Developers frequently merge code changes into a shared repository.
    * Automated build and testing processes triggered by code changes.
    * Early issue detection to reduce integration conflicts.
* **Continuous Delivery (CD):**
    * Automated packaging and preparation for deployment.
    * Rigorous testing in various environments (staging, production).
    * Maintainable deployable code state.
* **Continuous Deployment:** Automated deployment to production after successful testing.

## Clone Repository

**GitHub** is a platform for developers to store, manage, share, and collaborate on code using Git, a version control system tracking code changes. In this phase we will be coping the files from the kura labs workload 1 repository to our own. 

**Steps:**

1. Create an empty repository on your GitHub account.
2. Clone that repository locally by running the following command:
   ```
    git clone https://github.com/tjwkura5/retail-banking-app-deployed-elastic-beanstalk.git
    ```
3. Clone the kura labs repository locally by running the following command:
    ```
    git clone https://github.com/kura-labs-org/C5-Deployment-Workload-1.git
    ```
4. Copy the files from the Kura Labs repository to your repository by running the following command: 

    ```
    cp -r /*/*/*/Kura_code/C5-Deployment-Workload-1/* /*/*/*/Kura_code/retail-banking-app-deployed-elastic-beanstalk
    ```
5. Push the code to your repository and delete the kura labs repo by running the following commands: 

    ```
    git push -u https://tjwkura5:{ACCESS_CODE}@github.com/tjwkura5/retail-banking-app-deployed-elastic-beanstalk.git main

    sudo rm -rf /*/*/*/Kura_code/C5-Deployment-Workload-1
    ```

## Jenkins Server

**Jenkins** is an open-source automation server that helps teams automate parts of the software development process, especially building, testing, and deploying software. In this phase, we'll set up a Jenkins server to build and test our code stored in the GitHub repository.

**Key Features:**

* **Automation:** Frees developers to focus on coding by automating repetitive tasks.
* **Plugins:** Extends functionality for various technologies and platforms through a vast plugin ecosystem.
* **Flexibility:** Adapts to different project needs and workflows with its customizable nature.
* **Continuous Integration:** Automates build and testing processes upon code check-ins.
* **Continuous Delivery/Deployment:** Automates deployment to different environments (staging, production).

**Setting Up the CI Server (Jenkins):**

1. **Create an EC2 Instance:** Follow the [AWS EC2 Quickstart Guide](https://github.com/kura-labs-org/AWS-EC2-Quick-Start-Guide/blob/main/AWS%20EC2%20Quick%20Start%20Guide.pdf)

2. Connect to the EC2 terminal and install Jenkins:

    ```
    $sudo apt update && sudo apt install fontconfig openjdk-17-jre software-properties-common && sudo add-apt-repository ppa:deadsnakes/ppa && sudo apt install python3.7 python3.7-venv
    $sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    $echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    $sudo apt-get update
    $sudo apt-get install jenkins
    $sudo systemctl start jenkins
    $sudo systemctl status jenkins
    ```

    If successful, the console output should look something like the following:

    ```
    ubuntu@ip-172-31-42-3:~$ sudo systemctl status jenkins

    ● jenkins.service - Jenkins Continuous Integration Server
    Loaded: loaded (/usr/lib/systemd/system/jenkins.service; enabled; preset: enabled)
    Active: active (running) since Sun 2024-07-28 00:25:53 UTC; 2min 25s ago
    Main PID: 4588 (java)
      Tasks: 39 (limit: 1130)
     Memory: 311.4M (peak: 330.8M)
        CPU: 46.541s
     CGroup: /system.slice/jenkins.service
             └─4588 /usr/bin/java -Djava.awt.headless=true -jar /usr/share/java/jenkins.war --webroot=/var/cache/jenkins/war --httpPort=8080


**Accessing the Jenkins Web Interface :**

1. Determine Jenkins's Public IP Address by checking your EC2 instance details in the AWS Management Console.
2. Open a web browser and navigate to `http://<your_ec2_instance_public_ip>:8080`.
3. Unlock Jenkins by finding the generated alphanumeric password in the Jenkins log file (`/var/lib/jenkins/secrets/initialAdminPassword`) and entering it on the setup screen.
4. Install recommended plugins for basic functionality.
5. Create an admin user by following on-screen instructions.

**Create a Multi Branch Pipeline and Connect Github to Jenkins :**

1. Click on “New Item” in the menu on the left of the page.

2. Enter a name for your pipeline.

3. Select “Multibranch Pipeline”.

4. Under “Branch Sources”, click “Add source” and select “GitHub”.

5. Click “+ Add” and select “Jenkins”.

6. Make sure “Kind” reads “Username and password”.

7. Under “Username”, enter your GitHub username.

8. Under “Password”, enter your GitHub personal access token. The instructions for creating a token can be found [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic)

9. Enter the repository HTTPS URL and click "Validate"

10. Make sure that the "Build Configuration" section says "Mode: by Jenkinsfile" and "Script Path: Jenkinsfile"

11. Click "Save"

Once you save the pipeline a build should start automatically and if it's successful it should look like the following:

![Successful Jenkins Build](documentation/build_1.png)

## Deply to Elastic Beanstalk
We've now reached the continuous deployment phase of our project. **AWS Elastic Beanstalk** will be used to host our application.

**AWS Elastic Beanstalk** is a Platform as a Service (PaaS) offering from Amazon Web Services (AWS) that simplifies deploying and scaling web applications. It handles infrastructure setup, allowing developers to focus on application code rather than managing servers, load balancers, or auto-scaling.

We'll create a zip file from our repository's contents and upload it to Elastic Beanstalk.

**Key features of Elastic Beanstalk**:

* **Simplified deployment**: Upload your application code, and Elastic Beanstalk automatically provisions and configures the necessary resources.   
* **Auto-scaling**: Automatically adjusts the number of instances based on your application's traffic.   
* **Load balancing**: Distributes incoming traffic across multiple instances for optimal performance.   
* **Environment management**: Provides tools for managing and monitoring your application environments.   
Supported languages and platforms: Works with various languages and platforms like Java, .NET, Python, Node.js, Ruby, Go, and Docker. For this project we are using Python. 

**Steps:**

1. Follow the instructions in this [LINK](https://scribehow.com/shared/How_to_Create_an_AWS_IAM_Role_for_Elastic_Beanstalk_and_EC2__kTg4B7zRRxCp-aYTJc-WLg) for "How to Create an AWS IAM Role for Elastic Beanstalk and EC2" and create the two IAM roles as specified.

2. Navigate to the AWS Elastic Beanstalk console page.

3. Navigate to the "Environments" page on the left side menu and click on "Create Environment".

4. Create a "Web server environment" and enter the an Application name (Environment name should auto populate after that).

5. Choose "Python 3.7" as the "Managed platform".

6. "Upload your code" by choosing a "local file" and select the zipped application files you created earlier.

7. Under "Presets", make sure that "Single instance (free tier eligible) is selected and then click "Next".

8. Select the "Service role" and "EC2 profile" in the appropriate drop down menus and then click "Next".

9. Select the default VPC and Subnet "us-east-1a" and then click "Next".

10. Select "General Purpose (SSD) for "Root volume type" and assign it 10 GB.

11. Ensure that "Single instance" is selected for the "Environment type" and that ONLY "t3.micro" is selected for instance types (remove all others if present) and then click "Next".

12. Select 'BASIC' health reporting under the monitoring section. NOT "ENHANCED"!

13. Under Managed Platform Updates and Manged Updates uncheck Activated.

14. Continue to the "Review" page and then click "Submit".

15. When the "environment is successfully launched", click on the link provided in the "Domain" and confirm that the application has deployed.

## Issues/Troubleshooting

I finally reached the project's final step, only to encounter a 502 Gateway Error when accessing the application. To troubleshoot, I attempted to connect to the Elastic Beanstalk instance's terminal. However, I was unable to do so initially because I hadn't configured a security group to allow inbound SSH traffic on port 22. Once resolved, I could SSH into the instance and examine the error logs: 

    
    2024/07/28 02:04:16 [error] 2717#2717: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 108.250.49.128, server: , request: "GET / HTTP/1.1", upstream: "http://127.0.0.1:8000/", host: "retailbankapp-env.eba-psgup2q4.us-east-1.elasticbeanstalk.com"
    2024/07/28 02:04:17 [error] 2717#2717: *1 connect() failed (111: Connection refused) while connecting to upstream, client: 108.250.49.128, server: , request: "GET /favicon.ico HTTP/1.1", upstream: "http://127.0.0.1:8000/favicon.ico", host: "retailbankapp-env.eba-psgup2q4.us-east-1.elasticbeanstalk.com", referrer: "http://retailbankapp-env.eba-psgup2q4.us-east-1.elasticbeanstalk.com/"

The preceding error logs indicated that the web server attempted to connect to my application on port 8000 but was refused. Initially, I modified the /etc/nginx/nginx.conf file to change the nginx server's listening port, suspecting port 5000 was the correct one based on the application code. After restarting nginx with `sudo systemctl restart nginx`, it became clear this wasn't the solution. Further investigation led me to examine the logs more closely:

```
----------------------------------------
/var/log/web.stdout.log
----------------------------------------
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2789] [INFO] Worker exiting (pid: 2789)
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2782] [ERROR] Worker (pid:2789) exited with code 3
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2782] [ERROR] Shutting down: Master
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2782] [ERROR] Reason: Worker failed to boot.
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2794] [INFO] Starting gunicorn 22.0.0
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2794] [INFO] Listening at: http://127.0.0.1:8000 (2794)
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2794] [INFO] Using worker: gthread
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2801] [INFO] Booting worker with pid: 2801
Jul 28 20:32:12 ip-172-31-24-55 web: [2024-07-28 20:32:12 +0000] [2801] [ERROR] Exception in worker process
Jul 28 20:32:12 ip-172-31-24-55 web: Traceback (most recent call last):
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/arbiter.py", line 609, in spawn_worker
Jul 28 20:32:12 ip-172-31-24-55 web: worker.init_process()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/workers/gthread.py", line 95, in init_process
Jul 28 20:32:12 ip-172-31-24-55 web: super().init_process()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/workers/base.py", line 134, in init_process
Jul 28 20:32:12 ip-172-31-24-55 web: self.load_wsgi()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/workers/base.py", line 146, in load_wsgi
Jul 28 20:32:12 ip-172-31-24-55 web: self.wsgi = self.app.wsgi()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/app/base.py", line 67, in wsgi
Jul 28 20:32:12 ip-172-31-24-55 web: self.callable = self.load()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/app/wsgiapp.py", line 58, in load
Jul 28 20:32:12 ip-172-31-24-55 web: return self.load_wsgiapp()
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/app/wsgiapp.py", line 48, in load_wsgiapp
Jul 28 20:32:12 ip-172-31-24-55 web: return util.import_app(self.app_uri)
Jul 28 20:32:12 ip-172-31-24-55 web: File "/var/app/venv/staging-LQM1lest/lib64/python3.7/site-packages/gunicorn/util.py", line 371, in import_app
Jul 28 20:32:12 ip-172-31-24-55 web: mod = importlib.import_module(module)
Jul 28 20:32:12 ip-172-31-24-55 web: File "/usr/lib64/python3.7/importlib/__init__.py", line 127, in import_module
Jul 28 20:32:12 ip-172-31-24-55 web: return _bootstrap._gcd_import(name[level:], package, level)
Jul 28 20:32:12 ip-172-31-24-55 web: File "<frozen importlib._bootstrap>", line 1006, in _gcd_import
Jul 28 20:32:12 ip-172-31-24-55 web: File "<frozen importlib._bootstrap>", line 983, in _find_and_load
Jul 28 20:32:12 ip-172-31-24-55 web: File "<frozen importlib._bootstrap>", line 965, in _find_and_load_unlocked
Jul 28 20:32:12 ip-172-31-24-55 web: ModuleNotFoundError: No module named 'application'
```
The preceding errors indicate that the web server can't start because it can't find the module application it expects at your Flask app's entry point. Even though the application.py file is clearly located in the root directory!

Back at work, I first tried modifying the Procfile in the current directory, but this proved to be a waste of time.

Next, I attempted to add a python.config file to define the WSGI path, suspecting an incorrect configuration. Unfortunately, this approach also failed.

Frustrated, I was about to give up but luckily a fellow student had already started this workload before I did. He had encountered the same issue and managed to resolve it.  Fortunately, he was able to point me in the right direction when I sought his advice. 

I started watching some Youtube tutorials and I noticed an interesting detail: when they zip the application before uploading to beanstalk they're only zipping certain files in the root directory of the application and not the entire folder. When I tried doing it this way It worked but initially I had no idea why. 

Elastic Beanstalk requires the application's entry point to be located at the top level of the deployment package. This means we needed to simplify the directory structure by not including a parent folder or top-level directory. The following is the final product:

![Running Application](documentation/running_app.png)

## Optimization

**What are the benefits of using managed services for cloud infrastructure?**

**Reduced operational overhead:** Managed services handle the infrastructure management, provisioning, and maintenance allowing developers to focus on application code.

**Scalability:** Managed services can easily scale resources up or down to meet fluctuating demands, ensuring optimal performance and cost-efficiency.

**Cost optimization:** Managed services can provide cost-effective solutions by optimizing resource utilization and eliminating unnecessary expenses. Keep in mind that this will depend on your application. Ask your solution architect if managed services are right for you.

**Faster time-to-market:** Managed services accelerate deployment and time-to-market by handling infrastructure setup and configuration.

**What are some issues that a retail bank would face choosing this method of deployment and how would you address/resolve them?**

This manual deployment process is inefficient. To streamline the process, we should implement a Jenkins pipeline to automate deploying our application to Beanstalk. The AWS Elastic Beanstalk publisher plugin can facilitate this. Automation will save time and reduce human error. Additionally, establishing a staging environment for testing before production deployment is crucial. This approach allows for smoke and regression testing to identify issues early in the process. 

**What are other disadvantages of using elastic beanstalk or similar managed services for deploying applications?**

While managed services offer numerous benefits, they also have some potential drawbacks:

**Less control:** Managed services often abstract away underlying infrastructure details, limiting customization and control over the environment.

**Cost implications:** While managed services can optimize costs in some cases, they might incur additional fees compared to managing infrastructure entirely on your own.

**Performance:** In certain scenarios, the managed service layer can introduce slight performance overhead compared to managing infrastructure directly.

## Conclusion 

This project was a challenging but rewarding experience. We successfully leveraged Jenkins for continuous integration and configured Elastic Beanstalk to host our application.

**Key Takeaways:**

* **CI/CD Pipeline:** We established a CI/CD pipeline using Jenkins to automate code building and testing. This streamlines development and reduces manual intervention.

* **Elastic Beanstalk:** We utilized Elastic Beanstalk, a PaaS offering from AWS, to simplify deployment and management of the application. It allows focusing on application code rather than server infrastructure.

* **Deployment Challenges:** We encountered issues related to module import paths during deployment. Resolving this involved simplifying the directory structure by removing the top level directory.