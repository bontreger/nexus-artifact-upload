Role Name
=========

A local role to upload files to nexus

Requirements
------------

Ansible, Nexus account

Configure the local maven settings (/etc/maven/settings.xml) for the repository

Usage
--------------
Copy the role to a linux box.

Edit vars/main.yml to be accurate for the new artifact to upload

Ensure /etc/maven/settings.xml is set up properly

Run `ansible-playbook -v tasks/main.yml` from the main directory of the role

Maven Settings
--------------
There's a servers block in /etc/maven/settings.xml that contains the information for the nexus server
```
  <servers>
    <server>
      <id>###ALIAS FOR SERVER###</id>
      <username>###INSERT USERNAME###</username>
      <password>###INSERT PASSWORD###</password>
    </server>
  </servers>
```
Multiple servers can be defined in the servers block with different aliases (e.g. separate Nexus servers for dev and prod).  The alias is used as the repo_id in the role variables seen below.

Role Variables
--------------
```
repository_name: The repository to upload to. Often, this is "third party"
group_ID: The group ID for the new artifact. Often of the form "com.<author>.<product>.<version>.<type>"
artifact_ID: The article artifact ID
version: The version to upload. This must be equal to or higher than a current version if this is overwriting.  Best practice is to always be higher
generate_POM: true/false, self explanatory
packaging: What package type to upload. zip/war/jar,etc
repo_ID: nexus, This corresponds to the repo ID defined in /etc/maven/settings.xml, in case multiple nexus repositories are configured
nexus_url: The URL of the nexus at the repository name level.
 # LOCATION ON LOCALHOST
target_file: The path to the file to upload on the local host.
upload_method: maven/curl.  Maven is preferred but in some environments and situations, a curl method is needed
 # Only needed when using curl
username: ### INSERT USERNAME ###
password: ### INSERT PASSWORD ###
filename: The filename to have within nexus. 
```
Example Vars file
------------
```
---
 # The directory structure in Nexus is /repository/{{ repository_name }}/{{ group_ID }}/{{ artifact_ID }}/{{ version }}/<file>.{{ packaging }}
repository_name: thirdparty
group_ID: com.oracle.jdk.solaris.sparc
artifact_ID: jdk1.7.0_91
version: 1
generate_POM: true
packaging: tar
repo_ID: nexus
nexus_url: "http://nexus.local/repository/{{ repository_name }}"
 # LOCATION ON LOCALHOST
target_file: "/root/nexus/upload/jdk1.7.0_91-1.tar"
upload_method: maven
 # Only needed when using curl
username: ### INSERT USERNAME ###
password: ### INSERT PASSWORD ###
filename: "jdk1.7.0_91-1.tar"
```
The result of this playbook is seen in the screenshot linked below 


Example Execution
----------------
ansible-playbook -v tasks/main.yml
