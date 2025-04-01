# Overview

xGuard is a wrapper for the native **rm** utility designed to protect system directories and critical files from accidental deletion. It monitors specified directories and restricts deletion within a defined depth level.

xGuard operates only in interactive sessions, ensuring that applications and services can still use the native **rm** utility without interference.

# Installation

Follow these steps to install xGuard:

1. Download and extract the script
2. Locate the native **rm** utility 
   
    ```bash
    which rm
    ```

3. Edit the **xGuard/rm** script and configure the following:
   - Add any additional directories to the protected list.
   - Set your password.
   - Specify the path to the native rm utility.
4. Modify the **.bashrc** file of the user that xGuard should apply to by adding the xGuard path:
   
    ```bash
    export PATH="/path/to/xGuard:$PATH"
    ```

    ## Example
     ```bash
    export PATH="/home/essien/myScripts/xGuard:$PATH"
    ```

5. Save the file
6. Reload the **.bashrc** file
   
    ```bash
    source .bashrc
    ```

7. Verify that **rm** points to xGuard:
   
   ```bash
    which rm
    ```

    The output should show the xGuard rm utility path, e.g.:
    **/home/essien/myScripts/xGuard/rm** instead of native **rm** utility path.

# Usage

After installation, xGuard functions similarly to the native rm utility. However, when attempting to delete a protected file or directory within the defined depth level, you will be prompted to enter the configured password.

   - If authentication is successful, the deletion proceeds.
   - When deleting a directory or multiple files, xGuard will prompt for approval of all items after successful authentication. If not approved, each protected file will require individual password confirmation.

## Note
- To bypass xGuard and use the native **rm** utility, specify the absolute path of the native **rm** utility