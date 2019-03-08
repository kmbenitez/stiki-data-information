<!----- Conversion time: 2 seconds.


Using this Markdown file:

1. Cut and paste this output into your source file.
2. See the notes and action items below regarding this conversion run.
3. Check the rendered output (headings, lists, code blocks, tables) for proper
   formatting and use a linkchecker before you publish this page.

Conversion notes:

* Docs to Markdown version 1.0β16
* Fri Mar 08 2019 08:33:09 GMT-0800 (PST)
* Source doc: https://docs.google.com/a/getstiki.com/open?id=180v2T8aChVqjXKnXusD1sHpxLi5wayehX6pr8cM72XI

WARNING:
Inline drawings not supported: look for ">>>>>  gd2md-html alert:  inline drawings..." in output.

* This document has images: check for >>>>>  gd2md-html alert:  inline image link in generated source and store images to your server.
----->


<p style="color: red; font-weight: bold">>>>>>  gd2md-html alert:  ERRORs: 0; WARNINGs: 1; ALERTS: 3.</p>
<ul style="color: red; font-weight: bold"><li>See top comment block for details on ERRORs and WARNINGs. <li>In the converted Markdown or HTML, search for inline alerts that start with >>>>>  gd2md-html alert:  for specific instances that need correction.</ul>

<p style="color: red; font-weight: bold">Links to alert messages:</p><a href="#gdcalert1">alert1</a>
<a href="#gdcalert2">alert2</a>
<a href="#gdcalert3">alert3</a>

<p style="color: red; font-weight: bold">>>>>> PLEASE check and correct alert issues and delete this message and the inline alerts.<hr></p>




<p id="gdcalert1" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline image link here (to images/Stiki-Helpful0.png). Store image on your image server and adjust path/filename if necessary. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert2">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![alt_text](images/Stiki-Helpful0.png "image_tooltip")


**GetStiki.com**


# Stiki Helpful Commands


## Chroot Jail Setup on EC2 instance


### Resources



*   _https://aws.amazon.com/premiumsupport/knowledge-center/new-user-accounts-linux-instance/_
*   _http://allanfeid.com/content/creating-chroot-jail-ssh-access_


### Background

We would like to give our clients a secure location to upload phone lists and other media needed to create their campaigns. Previously this was done on our main server by creating a chroot jail, a user for each client requesting access, and not allowing SSH access. We want to provide a similar interface for them even after migrating to the AWS ecosystem. We considered several options:

(1) creating an S3 bucket with folders for each client and restricting their access to that particular folder


    This solution is a good one in terms of using parts of AWS for what they are designed and optimized for (S3 is for storage, and Amazon has created a simple, secure interface for uploading and downloading files). However, we would need to create AWS IAM users for each client, and in order to access their own files, they would be able to see all other Stiki storage buckets, and all other folders in the upload area. We could conceal client names at that level, but I don't think we're willing to consider clients when creating storage buckets for other Stiki uses.

(2) using a third party solution designed to create an SFTP server on an EC2 instance and move it to S3. (_https://aws.amazon.com/marketplace/pp/B072M8VY8M_)


    This solution also uses S3 for storage (desired), and has the SFTP interface that clients have grown accustomed to, in addition to being well reviewed. It does, however, come with an hourly cost. ($.05/hour for the software, in addition to the cost for the EC2 instance).  In the future, we may want to upgrade to this solution.

(3) a DIY implementation of the SFTP server on EC2, using only the storage associated with that instance.


    Even the T2 micro comes with enough storage for the kinds of files that we regularly receive. The challenge is to create the chroot jail, and to have alerts in the event that the instance is terminated.


### Implementation



1. Create EC2 instance using the console.
2. SSH to the new instance.
3. Create the jailing group


###### > sudo groupadd sftponly


###### > sudo mkdir /sftp


###### > sudo vim /etc/ssh/sshd_config


###### > sudo service sshd restart


    We want to create a new group of users, each of whom will only have access to their own folder. Each user will have a folder containing a .ssh folder and an uploads folder within the /sftp parent. When editing sshd_config, we change the sftp server and add specific directions for the sftponly group.


#### Edit /etc/ssh/sshd_config


    Comment out:  Subsystem sftp /usr/libexec/openssh/sftp-server


    Add: Subsystem sftp internal-sftp


    End Result:


```
        # override default of no subsystems
        #Subsystem sftp /usr/libexec/openssh/sftp-server
        Subsystem sftp internal-sftp
```



    Add to end:


```
            Match Group sftponly
                    ChrootDirectory /sftp/%u
                    AuthorizedKeysFile /sftp/%u/.ssh/authorized_keys
                    ForceCommand internal-sftp
                    AllowTcpForwarding no
                    X11Forwarding no

```



4. Create the keypair. Use the AWS EC2 Console to create a new keypair.  Name it something like clientAccess_newclient. The keyfile will automatically download. Change the permissions to 600, and run ssh-keygen -y against it to obtain the public key. (https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html#having-ec2-create-your-key-pair)
5. Create the user

######
    sudo adduser -g sftponly -d /uploads -s /sbin/nologin newclient


######
    sudo mkdir /sftp/newclient && sudo mkdir /sftp/newclient/uploads && sudo mkdir /sftp/newclient/.ssh


######
    sudo vim /sftp/newclient/.ssh/authorized_keys ###Insert public key found above


######
    sudo chown newclient /sftp/newclient/.ssh


######
    sudo chown newclient /sftp/newclient/.ssh/authorized_keys



###### sudo chown newclient /sftp/newclient/uploads


######
    sudo chgrp sftponly /sftp/newclient/.ssh


######
    sudo chgrp sftponly /sftp/newclient/.ssh/authorized_keys


###### sudo chgrp sftponly /sftp/newclient/uploads


######
    sudo chmod 700 /sftp/newclient/.ssh


######
    sudo chmod 600 /sftp/newclient/.ssh/authorized_keys


    Each user needs a directory, and uploads directory in which to place their files, and a .ssh directory to store their keyfile for authentication.



6. Test - Attempt to login using an SFTP client. Be sure to choose "Keyfile" as the authentication method, and supply the keyfile that you got the public key from in the step above.


## Combine Multiple CSVs

Create directory with all the csvs you want to combine, change to that directory.


######
    cat *.csv > merged.csv


## Create Elastic Beanstalk Application Source Bundle

To include hidden files, and avoid some of the Apple cruft, and the top-level folder.


###### zip ../myapp.zip -r * .[^.]*


## Messing About with Virtual Hosts

/Applications/XAMPP/xamppfiles/etc/extra/httpd-vhosts.conf


## Test EB Deployments



1. Open the Terminal application
2. Type `sudo vim /etc/hosts` and hit enter (Translate as: As the superuser, open the /etc/hosts file with the program vim)
3. Enter your password
4. Type <shift>+G o This will insert the cursor on the last line of the file.
5. Type the ip address of the development instance, space, then the url you want to use
6. Type <esc>:w
7. To exit vim, hit `<esc>:q`


## Test API Deployment


###### curl -v -d '@post.json' -H "Content-Type: application/json" -X POST https://api.getstiki.com/events


## Loop over results of a grep


###### grep '72346' customMsgSend.php | while read -r line;


###### do


###### wc -c <<< $line;


###### done > wordcounts


## Uploading Lambda function with outside dependencies using Docker

Resources: https://medium.freecodecamp.org/escaping-lambda-function-hell-using-docker-40b187ec1e48

I've been using python-lambda ([https://github.com/nficano/python-lambda](https://github.com/nficano/python-lambda)) to structure and test my functions. Install python-lambda in your environment, follow the directions in the documentation. Also, to avoid installing a bunch of extra packages, create a requirements.txt.

But if there are dependencies that have to be built on the ubuntu architecture (bcrypt was the one that started this), then you need to run the deploy function from Ubuntu. So we use Docker. From the directory where your source code is:


###### docker run -v `pwd`:/working -it --rm ubuntu

This will create a docker container that will be removed when you're done, allow you to interact with it first, and give you access to your current directory in a directory in the container called 'working'.


###### apt-get update


###### apt-get install python3-pip


###### cd working


###### pip3 install python-lambda


###### [pip3 install whatever packages you need]


###### lambda deploy --requirements='requirements.txt'


## Promote a Lambda function to production

For most of our Lambda functions, we maintain an alias named 'production' that is called instead of the latest version. This enables us to do testing without creating additional functions, and then to promote the newest version to production. Executing this command requires that appropriate credentials for the AWS cli be defined in ~/.aws/credentials.


###### aws lambda update-alias --region us-east-1 --function-name stikiSendHandler --function-version 5 --name production


## Create/update API Documentation (manual)

Our documentation is currently generated from a .yaml file adhering to OpenAPI v.3. The Swagger Editor does a good job making sure your doc fits the spec. [https://swagger.io/tools/swagger-editor/](https://swagger.io/tools/swagger-editor/)

Then we create the actual documentation to host privately using ReBilly/ReDoc. [https://github.com/Rebilly/ReDoc](https://github.com/Rebilly/ReDoc). I've been using the command line interface which can bundle the spec and the software into a zero-dependency HTML file, which we then store on S3.


###### redoc-cli bundle -o index.html NotifyOAS.yaml --options.pathInMiddlePanel --options.hideDownloadButton --options.theme.colors.primary.main='#82be37'

-o is the path for the outputted .html file

→options.pathInMiddlePanel puts the path in the middle panel of the documentation. (see yellow arrow)

→options.hideDownloadButton hides the download button. Ideally we wouldn't have to use this, but the Download Button was broken the last time I checked (should give the reader the option to download the .yaml spec, but just downloaded the page instead) (see red arrow)

→options.theme.colors.primary.main sets the main color. The above color makes it Stiki green.

Edit the resulting .html file to change the automatically generated title (ReDoc Documentation) to the correct one (something about Stiki and your API name). You can also do this by discarding the hunk that contains this mod in the Git diff display on GitX.



<p id="gdcalert2" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline drawings not supported directly from Docs. You may want to copy the inline drawing to a standalone drawing and export by reference. See <a href="https://github.com/evbacher/gd2md-html/wiki/Google-Drawings-by-reference">Google Drawings by reference</a> for details. The img URL below is a placeholder. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert3">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![drawing](https://docs.google.com/a/google.com/drawings/d/12345/export/png)

<p id="gdcalert3" ><span style="color: red; font-weight: bold">>>>>>  gd2md-html alert: inline drawings not supported directly from Docs. You may want to copy the inline drawing to a standalone drawing and export by reference. See <a href="https://github.com/evbacher/gd2md-html/wiki/Google-Drawings-by-reference">Google Drawings by reference</a> for details. The img URL below is a placeholder. </span><br>(<a href="#">Back to top</a>)(<a href="#gdcalert4">Next alert</a>)<br><span style="color: red; font-weight: bold">>>>>> </span></p>


![drawing](https://docs.google.com/a/google.com/drawings/d/12345/export/png)


<!-- Docs to Markdown version 1.0β16 -->
