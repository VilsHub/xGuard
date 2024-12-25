# Overview

The xGuard script is used to protect system directories and files from accidently deletion. It monitors the specified directories for files and directories within a specified level from the specified directories.

# Installation

To install the script, take the steps below:

1. Donwload and extract the script
2. Get the path the native **rm** utility
3. Edit the **xGuard/rm** file and:
   - Set your protected root directories
   - Set your password
   - Set the path to the native **rm** utility
4. Edit the **.bashrc** of the user to make use of the script
5. Append the export command below, to the **.bashrc** file
   
    ```bash
    export PATH="/path/to/xGuard:$PATH"
    ```

    ## Example
     ```bash
    export PATH="/home/essien/myScripts/xGuard:$PATH"
    ```

6. Save the file, and confirm that the **which** command matches the xGuard **rm** utility, when its executed for the **rm** utility. That is:
   
   ```bash
    which rm
    ```


# Usage

After installing the script, you make use of the script as you would using the native **rm** utility. Any file or directory within a specified level from the specified directories, would prompt for passowrd stored in the script.

Only on successfull authentication would the target file be deleted

## Note
- To delete without using the xGuard rm utility, make use of the native **rm** utility with it abosolute reference