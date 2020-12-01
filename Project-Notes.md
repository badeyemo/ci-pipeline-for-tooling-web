# Continous Integration Pipeline For Tooling Website

I will be building a Jenkins CI pipeline for a tooling website.

I will also mount a NFS to folder located within the Jenkins folder.

                                                                     

> Jenkins             | 192.168.1.109   |

> NFS                   | 192.168.1.116    |

> Web 1                | 192.168.1.117   |

![images/Untitled.png](images/Untitled.png)

# **Prepare the Jenkins server**

I have created a new dedicated virtual machine for **Jenkins**

Here are the steps I have followed.

1. Logged to NFS server and ensured the `/mnt/opt` is exported to allow the Jenkins server make use of the mount. 

    - I double checked `/etc/exports'` file as set in my previous projects, the subnet range of `192.168.10/24` was the range the mounted volumes can be used to for NFS shares
    - Used `exportfs -arv` to export the shared mounted folders 

          - On my jenkins server I used coommand to check for shared NFS folders

    ```bash
    showmount -e 192.168.1.116

    #Output:

    Export list for 192.168.1.116:
    /mnt/logs 192.168.1.0/24
    /mnt/html 192.168.1.0/24
    /mnt/opt  192.168.1.0/24
    /mnt      192.168.1.0/24
    ```

2. Mount `/var/lib/` to store data on NFS

     - I have now mounted `/var/lib` with `/mnt/opt` folder using the following command and ensuring it will persist on system reboot by adding the mount information to `/etc/fstab` file

    ```bash
    mount -t nfs 192.168.1.116:/mnt/opt /var/lib
    ```

    ```bash
    #Added mount info to /etc/fstab file
    echo "192.168.1.116:/mnt/opt                  /var/lib	          nfs     defaults        0 0" >> /etc/fstab

    #Amended /etc/hosts file
    192.168.1.117 web-host-01-devopswithdinul.lan
    192.168.1.115 web-host-02-devopswithdinul.lan
    192.168.1.122 web-host-03-devopswithdinul.lan
    192.168.1.103 apache-lb-devopswithdinul.lan
    192.168.1.109 jenkins-devopswithdinul.lan
    ```

3. Download and install `jenkins` on the Jenkins Server

    ```bash
    # Installed Java on Jenkins server
    sudo wget -O /etc/yum.repos.d/jenkins.repo \
        http://pkg.jenkins-ci.org/redhat/jenkins.repo

    sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key

    sudo dnf upgrade

    sudo dnf install jenkins java-devel
    #restarted server

    ```

    Used the cat command on the directory shown below to get the initial password for admin user.

    ![images/Untitled%201.png](images/Untitled%201.png)

    Once logged in installed suggested plugins to get started.

    ![images/Untitled%202.png](images/Untitled%202.png)

4. Configure a simple jenkins job to automatically build the tooling website code

 * Creating my first job

 * Selecting my Tooling repo

 * choosing to scan for changes every 2 minutes (cronjob)


    ![images/Untitled%203.png](images/Untitled%203.png)



5. Simulated a code change and see how every code push is being deployed to each of the web servers.

I have added a copy right feature on the index.php file and I have then created a new branch and committed the file and then merged it to my master branch.

This is the result of the changes.

Index.php now shows a copy right date.

![images/Untitled%204.png](images/Untitled%204.png)

Jenkins build log

![images/Untitled%205.png](images/Untitled%205.png)

Checking the NFS server, verifying if the folders are being shared from both servers.

![images/Untitled%206.png](images/Untitled%206.png)

Credits:

[Linux](https://www.jenkins.io/doc/book/installing/linux/#red-hat-centos)

[DevOps Career And Mentorship Experts - Darey.io](https://darey.io/dashboard/)