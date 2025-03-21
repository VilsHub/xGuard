# Overview

The xGuard script is used to protect system directories and files from accidently deletion. It monitors the specified directories for files and directories within a specified level from the specified directories.

# Installation

To install the script, take the steps below:

1. Donwload and extract the script
2. Get the path the native **rm** utility
3. Edit the **xGuard/rm** file and:
   - Set your protected root directories, in addition to the default
   - Set your password
   - Set the path to the native **rm** utility
4. Edit the **.bashrc** of the user that the script would apply to, and the root path of the xGuard rm utility, as shown below:
   
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

7. Confirm that the **which rm** command matches the xGuard **rm** utility path. That is:
   
   ```bash
    which rm
    ```

    Should output:
    **/home/essien/myScripts/xGuard/rm** instead of native **rm** utility

# Usage

After installing the script, you make use of the script as you would using the native **rm** utility. Any file or directory within the specified level in the script, would prompt for passoword stored in the script.

Only on successfull authentication would the target file be deleted

## Note
- To delete without using the xGuard rm utility, make use of the native **rm** utility with it abosolute reference