# CLI_Cloudstor

## Description

[Cloudstor](https://cloudstor.aarnet.edu.au/) is AARNet's file sync, share and storage service. This guide will show you how to upload and download data to Cloudstor via the command line interface.

Note: Researchers have access to 1TB of storage on Cloudstor.

## Create app password

Cloudstor app passwords are a way to control access between your Cloudstor account and other devices (e.g. a CLI platform). This step only needs to be done once per device - you can use the same app password to access your account for future transfers. 

* Log into the [Cloudstor](https://cloudstor.aarnet.edu.au/) web interface using your institutional credentials
* Click the cog icon (settings) at the top right side of the page
* Click "Security" on the left hand pane
* Under "App Passwords", type the name of your app (your CLI platform, e.g. Artemis) in the text box and click "Create new app password"
* Copy the "Password/Token" that was generated. This will be used to access Cloudstor via the CLI

![image](https://user-images.githubusercontent.com/49257820/163305774-26896f75-5e0f-4faf-bc15-25c8d2f71abb.png)

## Data upload

### Check Cloudstor storage availability

Check that you have enough space in Cloudstor to upload your data.

* In the Cloudstor web interface: Click the cog icon (settings) at the top right hand side of the page. The top of the page will tell you amount of disk used vs disk available.
* In your CLI platform, check the size of the data to upload, e.g. using `df -sh </path/to/directory_to_upload>`

### Checksums

We recommend creating and including a checksum file (`.md5`) anytime you intend to share or move data around. Checksums allows the downloader to validate the file and confirm that the transfer completed successfully. We __highly__ recommend doing this for research data (e.g. sequencing data). 

You can skip this step if you already have this file. Otherwise, in your CLI platform with the data (`file.txt`) to upload, create a checksum file by:

```
cd /path/to/file
md5sum file.txt > file.txt.md5
```

Note: We recommend submitting this within a job for very large files. 

### Preparing multiple files for transfer

We recommend tar archiving multiple files into a single tar archive file as this makes downloading them more manageable. For `my_directory` containing multiple files (e.g. `file.txt`, `file.txt.md5`) to upload:

```
# Change to the the path where my_directory is located
cd /path/to
# Tar archive and gzip the directory to upload
tar -zcvf my_directory my_directory.tar.gz
```

### Uploading data to Cloudstor

Here is where you will use the app password generated in [Create app password](#Create-app-password). In your CLI platform, to upload a file (e.g. `my_directory.tar.gz`) to Cloudstor:

```
curl --user 'your.email@sydney.edu.au:THE-BIG-LONG_PASSWORD' -T my_directory.tar.gz 'https://cloudstor.aarnet.edu.au/plus/remote.php/webdav/'
```

Note: We recommend submitting this within a job for very large files. An example PBS script for Artemis HPC is provided in this repository.

You should see your file appear in Files when you log into your Cloudstor account via the web interface.

## Downloading data from Cloudstor

### Get download URL

* On the [Cloudstor](https://cloudstor.aarnet.edu.au/) web interface, navigate to the file or directory that you wish to share
* Click on the share icon (next to the three dots). This will open a pane to the right of the window
* Under "Sharing", click "Public links" and click the "Create public link" button. A small window will appear. Fill details in this window:
  * __Do not set a password if you intend to use CLI interface to download the data__. 
  * Fill in the rest of the form as desired, or leave as default
  * Click share
* You should be able to see that a download URL has been created. Don't skip the next section if you are downloading files to a CLI platform!

### Downloading from Cloudstor to a CLI platform

Add `/download` to the download URL obtained from the Cloudstor web interface (or provided to you by your collaborator). You can then use something like `wget` to download the file:

```
# Use -O to give the output file a name
wget my_directory.tar.gz https://cloudstor.aarnet.edu.au/plus/s/XXXXXXXXXXX/download -O my_directory.tar.gz
# If relevant, unpack the file:
tar -xvf my_directory.tar.gz
```

### Verify files using checksums

It's good practice to verify that the files you have downloaded are not corrupt. To do this, you will need the checksum (`.md5` file) for a file. 

```
md5sum -c my_directory.tar.gz.md5
```

The `.md5` file contains two columns: the first is a unique hash for the file. The second is the path to the file. The command below will only work if you use the correct path relative to what's in the .md5 file. The command will print the result of the verification to stdout. 

# Acknowledgements

Acknowledgements (and co-authorship, where appropriate) are an important way for us to demonstrate the value we bring to your research. Your research outcomes are vital for ongoing funding of the Sydney Informatics Hub and national compute facilities.

## Suggested acknowledgements

The authors acknowledge the technical assistance provided by the Sydney Informatics Hub, a Core Research Facility of the University of Sydney. 
