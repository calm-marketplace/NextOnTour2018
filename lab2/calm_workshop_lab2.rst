**********************************************************
**Introduction – Simple Integration with Ansible Galayxy**
**********************************************************

.. contents::

Lab Overview
************

Welcome to the Calm Hands-On-Lab - Simple Integration with Ansible Galayxy.
What we’re going to do here is to import a blueprint to showcase an integration
with Ansible Galaxy (pull mode):

**Part 1: Import blueprint from https://github.com/calm-marketplace/NextOnTour2018/blob/master/lab2/Ansible%20Galaxy%20ThomasF.json **
*****************************************

1. Download the blueprint

2. Login to Prism Central with admin user and using the HPOC credentials

3. Click on the Apps tab across the top of Prism

4. Navigate to the Blueprint ( |image2| ) tab

5. Click on **Upload Blueprint **

6. Enter the Blueprint Name to **ansible-galaxy-<<yourName>> **

7. Choose "calm" as your project and click **Upload **

**Part 2: Edit the blueprint**
**************************************

After the import of a blueprint all credentials are missing! This is needed to avoid that credentials will be exposed or captured.



|image7|

+----------------------+------------------------------------------------------+
| **Variable Name **   | **Value **                                           |
+----------------------+------------------------------------------------------+
| Mysql\_user          | root                                                 |
+----------------------+------------------------------------------------------+
| Mysql\_password      | nutanix/4u                                           |
+----------------------+------------------------------------------------------+
| Database\_name       | training                                             |
+----------------------+------------------------------------------------------+

Setup the variables as specified in the table above.

**Adding A DB Service**

With these basics setup, let’s create our first service.

1. Click the + sign next to **Services** in the **Overview** pane.

2. Notice that the **Configuration** pane has changed and there is now a
   box in the **Workspace.**

3. Name your service DBService at the top

4. The Substrate section is the internal Calm name for this Service.
   Name this **DBSubstrate.** (in the VM tab)

5. Make sure that the Cloud is set to **Nutanix** and the OS set to
   **Linux**

Now update the VM Configuration section to match the following:

+----------------------+------------------------------------------------------+
| VM Name              | training-mysql-<<yourName>>                          |
+----------------------+------------------------------------------------------+
| Image                | CentOS                                               |
+----------------------+------------------------------------------------------+
| vCPUs                | 1                                                    |
+----------------------+------------------------------------------------------+
| Cores per vCpu       | 2                                                    |
+----------------------+------------------------------------------------------+
| Memory               | 2 GiB                                                |
+----------------------+------------------------------------------------------+


1. Scroll to the bottom and add a NIC attached to the **training**
   network

2. Configure the **Credentials** at the bottom to use the credentials
   you made above

3. Scroll back up to the top and click **Package**

**Package Configuration**

Here is where we specify the installation and uninstall scripts for this
service. Give the install package a name (MySQL\_package for example),
set the install script to **shell** and select the **root** credential you created earlier. Copy
the following script into the **install** window:

.. code-block:: bash

   #!/bin/bash
   set -ex

   yum install -y "http://repo.mysql.com/mysql-community-release-el7.rpm"
   yum install -y mysql-community-server.x86_64

   systemctl enable mysqld
   systemctl start mysqld

   #Mysql secure installation
   mysql -u root<<-EOF

   UPDATE mysql.user SET Password=PASSWORD('@@{Mysql_password}@@') WHERE User='@@{Mysql_user}@@';
   DELETE FROM mysql.user WHERE User='@@{Mysql_user}@@' AND Host NOT IN ('localhost', '127.0.0.1', '::1');
   DELETE FROM mysql.user WHERE User='';
   DELETE FROM mysql.db WHERE Db='test' OR Db='test\_%';

   FLUSH PRIVILEGES;
   EOF

   yum install firewalld -y
   systemctl enable firewalld
   systemctl start firewalld
   firewall-cmd --add-service=mysql --permanent
   firewall-cmd --reload

   mysql -u @@{Mysql_user}@@ -p@@{Mysql_password}@@ <<-EOF
   CREATE DATABASE @@{Database_name}@@;
   GRANT ALL PRIVILEGES ON @@{Database_name}@@.* TO '@@{Database_name}@@'@'%' identified by 'secret';

   FLUSH PRIVILEGES;
   EOF


Looking at this script, we see that we’re using the variables we set
before and doing basic mySQL configuration. This can be customized for
whatever unique need you have.

Since we don’t need anything special ran when uninstalling, we will just
add a very basic script to the uninstall. This can be useful for cleanup
(for example, releasing DNS names or cleaning up AD), but we won’t use
it here.

Set the uninstall script to **shell** and select the credential you used
earlier. Fill the uninstall script window with a simple:

.. code-block:: bash

   #!/bin/bash
   echo "Goodbye!"

After doing all the configuration click the **Save** button. If any
errors come up, go back and review the configuration to ensure that all
fields have been filled.

**Part 3: Launching the Blueprint**
***********************************

Now that the blueprint has been created and saved, you can launch it!

Click on the **Launch** button in the top right. This will bring up the
the launch window. Give this instance a unique name
(**training-mysql-\_<<YourName>>\_1**). Note that for every launch you do you will
need to increment this as instance names must be unique.

This will now bring you to the **Instance** page. The bar across the top
allows you to see various information about the instance:

|image11|

**Manage** allows you to see all the actions you can run against this
instance (we’ll get to creating custom actions in a moment).

You can also click on the arrow all the right on an action to see what
it does and ­ if it’s currently running ­ where in the process it is.

|image12|

|image13|

The **Services** tab show you information about the VMs that make up
this instance.

Finally the **Audit** tab shows you what actions have been called
against this instance and by who. You can also click on any action (or
sub­action) and get the logs from that event.

|image14|

|image15|



.. |image1| image:: ./media/image2.png
   :width: 3.84792in
   :height: 4.45278in
.. |image2| image:: ./media/image3.png
   :width: 0.23611in
   :height: 0.23611in
.. |image3| image:: ./media/image4.png
   :width: 5.79314in
   :height: 3.93637in
.. |image4| image:: ./media/image5.png
   :width: 3.03690in
   :height: 3.84580in
.. |image5| image:: ./media/image6.png
   :width: 0.88889in
   :height: 0.22222in
.. |image6| image:: ./media/image7.png
   :width: 2.90364in
   :height: 3.25278in
.. |image7| image:: ./media/image8.png
   :width: 3.19237in
   :height: 3.35452in
.. |/Users/nathancox/Desktop/Screen Shot 2017-11-29 at 11.54.22 AM.png| image:: ./media/media/image9.png
   :width: 2.99372in
   :height: 3.22371in
.. |/Users/nathancox/Desktop/Screen Shot 2017-11-29 at 12.03.25 PM.png| image:: ./media/media/image10.png
   :width: 3.01458in
   :height: 5.12232in
.. |image11| image:: ./media/image12.png
   :width: 5.76458in
   :height: 1.57328in
.. |image12| image:: ./media/image13.png
   :width: 6.50000in
   :height: 1.52603in
.. |image13| image:: ./media/image14.png
   :width: 6.50000in
   :height: 3.04638in
.. |image14| image:: ./media/image15.png
   :width: 3.93125in
   :height: 3.18666in
.. |image15| image:: ./media/image16.png
   :width: 4.34792in
   :height: 3.60663in
.. |image17| image:: ./media/image17.png
   :width: 4.34792in
   :height: 3.60663in
