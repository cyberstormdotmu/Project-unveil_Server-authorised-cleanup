<h1>PROJECT UNVEIL DISCOVERS A MALICIOUS PACKAGE THAT ALLOWS A REMOTE SERVER TO DELETE DIRECTORIES</h1>


**INTRODUCTION**
------------------

Project Unveil is an initiative by Cyberstorm.mu focused on investigating real-world supply-chain threats affecting open-source software and providing cybersecurity students with practical experience in malware analysis and threat research.

A malicious package was identified containing functionality that allows a remote server to authorize recursive deletion of files or folders from the victim's working directory without client-side validation.

 

**THE INVESTIGATION**
---------------------

Project Unveil examined the supplied JavaScript source to determine whether it contained malicious or suspicious functionality.

The code uses Node.js filesystem functionality through the built-in fs module and defines a function called performCleanup(). Despite the relatively harmless name, this function does not perform a limited or controlled cleanup. Instead, it enumerates items in a directory and attempts to remove every file and directory it finds, excluding only the JavaScript file itself.

The investigation also identified an external authorization mechanism. Before deletion takes place, the code contacts a remote HTTPS endpoint and supplies a hard-coded API key. The response from that server determines whether the destructive operation is allowed to proceed.

This combination of remote authorization and destructive filesystem operations makes the code highly suspicious and potentially malicious.

 

**WHAT THE MALICIOUS CODE DID**
---------------------------------

Integrated into a cleanup function which intends to simply clear files to free disk space ,

The malicious line of code:  fs.rmSync(fullPath, { recursive: true, force: true }); forcefully deletes the target directory and all files within, without any validation checks or authorisation from the user.

In practical terms, any system running this script would grant the attacker the opportunity to permanently delete the user's personal files, operating system files, windows directory and program data especially if attacker operates as a root user account with administrator privileges.

**INDICATORS OF COMPROMISE**
---------------------------

testback-2aqe.onrender.com
https://testback-2aqe.onrender.com/can-cleanup
The domain and URL appear to be non existent
API_KEY_’f8675764-417f-49a4-807c-5879a667c476'
fetch(API_URL, {
          headers: { 'x-api-key': API_KEY },
Unknown authorisation from this endpoint from a suspicious server
After authorisation, function performCleanup proceeds with cleanup,
fs.rmSync(fullPath, { recursive: true, force: true });
 

**RISK TO USERS**
-----------------------

If this code is included in a package and its triggerCleanup() function is executed, the consequences could be severe.

When the remote server returns an authorization value of true, the program can enumerate the target directory and delete its files and directories. If the function is called without specifying a directory, the target defaults to the process's current working directory.

For a developer working inside a project directory, this could result in source files, configuration files, dependencies, or other project data being deleted.

The exact extent of the damage would depend on the directory supplied to triggerCleanup() and the permissions available to the Node.js process.

 

**AKNOWLEDGEMENTS**
-------------------

Project Unveil (Chitrakshi Bhookhun, Pushali Bundhoo, Rishul Ramdawon and Zahra Sumun, Munish Ancharaj) would like to acknowledge the researchers involved in supporting this investigation.

This advisory is intended for defensive security research and public awareness. The identified remote endpoint should not be accessed directly. Indicators are provided so defenders can identify and block related activity.

**CONCLUSION**
-----------------

This report was established by Project unveil working closely with Cybersecurity students such as Chitrakshi Bhookhun, Pushali Bundhoo, Zahra Sumun, Munish Kumar Ancharaj and Rishul Ramdawon.

We got this npm i server-authorized-cleanup project with the following details:

Package Name: server-authorized-cleanup

Version: 1.1.0

License: ISC

Main file: index.js

After analysing the index.js code, the function performCleanup() was found to delete every file and directory it finds except the script file itself.

The code communicates with a remote authorization endpoint ('https://testback-2aqe.onrender.com/can-cleanup'),  and sends a hard-coded API key in the headers to check if the server authorizes the operation before the deletion takes place.
