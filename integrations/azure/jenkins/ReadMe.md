### Building a browser-based test automation server with Jenkins on Azure by using SeleniumBase

(**2022 NOTE:** Steps from [this 2019 tutorial from Boston Code Camp](https://www.bostoncodecamp.com/CC31/sessions/details/16741) are now **out-of-date**. For installing Jenkins from the Azure Marketplace, you can try using [Bitnami Jenkins](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.jenkins). **Or**, for the newer official Microsoft tutorial, see [Get Started: Install Jenkins on an Azure Linux VM](https://docs.microsoft.com/en-us/azure/developer/jenkins/configure-on-linux-vm), and then continue with [Step 4](#step4) below to resume SeleniumBase setup after you've created your Jenkins instance.)

----------

### Step 0. Fork the [SeleniumBase](https://github.com/seleniumbase/SeleniumBase) repo on GitHub to get started quickly.

* **(You'll be using your own repository eventually.)**

### Step 1. Find Jenkins in the Azure Marketplace

#### Search for ["Jenkins" in the Azure Marketplace](https://portal.azure.com/#blade/Microsoft_Azure_Marketplace/GalleryFeaturedMenuItemBlade/selectedMenuItemId/home/searchQuery/jenkins/resetMenuId/) and select the ``Jenkins (Publisher: Microsoft)`` result to get to the Jenkins Start page.

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_01.png "Jenkins on Azure")

----------

### Step 2. Launch a Jenkins instance

#### Click "Create" and follow the steps...

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_02.png "Jenkins on Azure")

----------

#### Continue to "Additional Settings" when you're done with "Basics".

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_03.png "Jenkins on Azure")

----------

#### On the "Additional Settings" section, set the Size to "B2s":

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_04.png "Jenkins on Azure")

----------

#### Once you've reached Step 5, click "Create" to complete the setup.

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_05.png "Jenkins on Azure")

----------

### Step 3. Inspect your new Jenkins instance to SSH into the new machine

#### Once your new Jenkins instance has finished launching, you should be able to see the main page:

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_06.png "Jenkins on Azure")

----------

#### On the main page, you should be able to find the Public IP Address.
* **Use that IP Address to SSH into the machine:**

```zsh
ssh USERNAME@IP_ADDRESS
```

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_07.png "Jenkins on Azure")

----------

<a id="step4"></a>

### Step 4. Clone the SeleniumBase repository from the root ("/") directory.

```zsh
cd /
sudo git clone https://github.com/seleniumbase/SeleniumBase.git
```

### Step 5. Enter the "linux" folder

```zsh
cd SeleniumBase/integrations/linux/
```

### Step 6. Give the "jenkins" user sudo access (See [jenkins_permissions.sh](https://github.com/seleniumbase/SeleniumBase/blob/master/integrations/linux/jenkins_permissions.sh) for details)

```zsh
./jenkins_permissions.sh
```

### Step 7. Become the "jenkins" user and enter a "bash" shell

```zsh
sudo su jenkins
bash
```

### Step 8. Install dependencies (See [Linuxfile.sh](https://github.com/seleniumbase/SeleniumBase/blob/master/integrations/linux/Linuxfile.sh) for details)

```zsh
./Linuxfile.sh
```

### Step 9. Start up the headless browser display mechanism: Xvfb (See [Xvfb_launcher.sh](https://github.com/seleniumbase/SeleniumBase/blob/master/integrations/linux/Xvfb_launcher.sh) for details)

```zsh
./Xvfb_launcher.sh
```

### Step 10. Go to the SeleniumBase directory

```zsh
cd /SeleniumBase
```

### Step 11. Install the [requirements](https://github.com/seleniumbase/SeleniumBase/blob/master/requirements.txt) for SeleniumBase

```zsh
sudo pip install -r requirements.txt --upgrade
```

### Step 12. Install SeleniumBase (Make sure you already installed the requirements above)

```zsh
sudo python setup.py develop
```

### Step 13. Install chromedriver

```zsh
sudo seleniumbase install chromedriver
```

### Step 14. Run an [example test](https://github.com/seleniumbase/SeleniumBase/blob/master/examples/my_first_test.py) in Chrome to verify installation (May take up to 10 seconds)

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_08.png "Jenkins on Azure")

----------

```zsh
pytest examples/my_first_test.py --headless --browser=chrome
```

### Step 15. Secure your Jenkins machine

#### Navigate to http://JENKINS_IP_ADDRESS/jenkins-on-azure/

(Depending on your version of Jenkins, you may see the following screen, or nothing at all.)

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_09.png "Jenkins on Azure")

----------

#### Initially, Jenkins uses only ``http``, which makes it less secure.

#### You'll need to set up SSH Port Forwarding in order to secure it.

* **To do this, copy/paste the string and run it in a NEW command prompt on your local machine (NOT from an SSH terminal session), swapping out the username and DNS name with the ones you set up when creating the Jenkins instance in Azure.**

``ssh -L 127.0.0.1:8080:localhost:8080 USERNAME@DNS_NAME``

### Step 16. Login to Jenkins

#### If you've correctly set up SSH Port Forwarding, the url will be ``http://127.0.0.1:8080/``

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_10.png "Jenkins on Azure")

----------

#### You'll need to get the password from the SSH terminal on the Linux machine to log in:

```zsh
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Step 17. Customize Jenkins

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_11.png "Jenkins on Azure")

----------

### Step 18. Create an Admin user

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_12.png "Jenkins on Azure")

----------

#### Once Jenkins has finished loading, the top left of the page should look like this:

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_13.png "Jenkins on Azure")

----------

### Step 19. Create a new Jenkins job

* **Click on "New Item"**
* **Give your new Jenkins job a name (ex: "Test1")**
* **Select "Freestyle project"**
* **Click "OK"**

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_14.png "Jenkins on Azure")

----------

### Step 20. Setup your new Jenkins job

* **Under "Source Code Management", select "Git".**
* **For the "Repository URL", put: ``https://github.com/seleniumbase/SeleniumBase.git``. (You'll eventually be using your own clone of the repository here.)**

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_15.png "Jenkins on Azure")

----------

* **Under "Build", click the "Add build step" dropdown.**
* **Select "Execute shell".**
* **For the "Command", paste:**

```zsh
cd examples
pytest my_first_test.py --headless
```

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_16.png "Jenkins on Azure")

----------

#### Click "Save" when you're done.

* **You'll see the following page after that:**

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_18.png "Jenkins on Azure")

----------

### Step 21. Run your new Jenkins job

* **Click on "Build Now"**
* **(If everything was done correctly, you'll see a blue dot appear after a few seconds, indicating that the test job passed.)**

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_19.png "Jenkins on Azure")

----------

### Step 22. See the top Jenkins page for an overview of all jobs

![](https://seleniumbase.github.io/cdn/img/azure/jenkins_on_azure_17.png "Jenkins on Azure")

----------

### Step 23. Future Work

If you have a web application that you want to test, you'll be able to create SeleniumBase tests and add them to Jenkins as you saw here. You may want to create a Deploy job, which downloads the latest version of your repository, and then kicks off all tests to run after that. You could then tell that Deploy job to auto-run whenever a change is pushed to your repository by using: "Poll SCM". All your tests would then be able to run by using: "Build after other projects are built". 

#### Congratulations! You're now well on your way to becoming a build & release / automation engineer!
