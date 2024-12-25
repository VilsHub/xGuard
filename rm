#!/bin/bash
params=$*

# Config
delPassword="123456"
level=5
mainRM="/usr/bin/rm"


# Functions definition
function formOptions(){
    local options=""
    while getopts "fiIrdv" variable; do

        case "$variable" in
            f)
                options=$options"f"
            ;;
            i)
                options=$options"i"
            ;;
            I)
                options=$options"I"
            ;;
            r)
                options=$options"r"
            ;;
            d)
                options=$options"d"
            ;;
            v)
                options=$options"v"
            ;;
        esac
    done

    echo $options
    
}

function parseParam(){
    local param=$1
    param="${param%/}"
    param="${param#/}"
    echo $param
}

function totalSegments(){
    local segment=$1
    segment="${segment%/}"
    segment="${segment#.}"
    segment="${segment#/}"
    delimiter="/"
    IFS="$delimiter" read -ra parts <<< "$segment"
    local totalSegments=${#parts[@]}
    echo $totalSegments
}

function objectExist(){
    local file="$1"
    local objectType=""
    
    if [ -d $file ]; then
        objectType="directory"
    elif [ -f $file ]; then
        objectType="file"
    fi

    echo $objectType
}

function promptForDeletion(){
    local directory=$1
    local protectedDirectory=$2
    objectType=$(objectExist $directory)

    echo -e "\nThe $objectType '$directory' to be detected exist within $level levels deep from the Protected directory '"$protectedDirectory"'"
    echo "You would need to supply your password or cancel ( Ctrl + C ) to use the inbuilt rm '$mainRM' utility"
    read -p "Password: " password
    while [ $password != $delPassword ]; do
        echo "Incorrect password, try again or cancel ( Ctrl + C ) to use the inbuilt rm '$mainRM' utility"
        read -sp "Password: " password
    done
    return 22
}

function spliBy(){
    local string=$1
    local delimiter=$2
    local parts=()

    while [[ "$string" == *"$delimiter"* ]]; do
        parts+=("${string%%"$delimiter"*}")  # Add the portion before ".." to the array
        string="${string#*"$delimiter"}"    # Remove the portion up to ".." from the string
    done

    parts+=("$string")  # Add the remaining part

    echo ${parts[@]}

}

function newObject(){

    local path=$1
    local firstChar=${path:0:1}
    local first2Chars=${path:0:2}
    local currentDir=$(pwd)

    if [[ $firstChar == "." && $first2Chars != ".." ]];then
        path=${path#.}
    elif [[ $first2Chars == ".." ]]; then

        pathsSegment=$(spliBy $path "..")

        read -a parsedPathsSegment <<< "$pathsSegment"

        totalPathSegments=${#parsedPathsSegment[@]}
        cd $(printf '../%.0s' $(seq 1 $totalPathSegments))
        path="${path//..\//}"
        path="/"$path

    else
        path="/"$path
    fi

    local newPath=$currentDir$path
    echo $newPath
}

function isRelative(){

    local path=$1
    local status=0
    local firstChar=${path:0:1}
    local first2Chars=${path:0:2}

    if [[ $firstChar == "." || $first2Chars == ".." ]]; then
        status=1
    elif [ $firstChar != "/" ]; then
        status=0
    fi
    echo $status
}

function deleteFile(){
    file=$1
    shift
    echo -e "\n'$file' is considered safe for deletion"
    echo "Executing Deletion of file '$file'...."
    objectType=""
    
    if [ -f $file ]; then
        objectType="file"
    elif [ -d $file ]; then
        objectType="directory"
    fi
    
    if [ ${#objectType} -gt 0 ]; then
        # Can delete
        options=$(formOptions $@)
        parsedOptions=""

        if [ ${#options} -gt 0 ]; then
            parsedOptions="-"$options
        fi

        # $mainRM $parsedOptions $file

        if [ $? -eq 0 ]; then
            echo -e "'$file' deleted successfully!\n"
        fi

    else
        # no object found to delete
        echo -e "\nThe object '$file' does not exist for deletion"
    fi
   
}

function isOption(){
    local object=$1
    local firstChar=${object:0:1}
    local status=0

    if [ $firstChar == "-" ]; then
        status=1
    fi

    echo $status
}


# List of gaurded root directories protected to maximum $level levels sub directories
protectedDirectories=("/root" "/usr" "/bin" "/boot" "/dev" "/etc" "/home" "/lib" "/lib32" "/lib64" "/libx32" "/media" "/opt" "/proc" "/run" "/sbin" "/snap" "/srv" "/sys" "/var")
currentDir=$(pwd)

# Check multiple mode
delimiter=" "
IFS="$delimiter" read -ra objects <<< "$params"
totalFilesOrDirs=${#objects[@]}

# Current diretory
totalCurrentDirSegment=$(totalSegments $currentDir)

if [ $totalFilesOrDirs -gt 1 ]; then
    # Multiple objects deletion detected (if option is passed or more than one object specified for deletion)
    for object in ${objects[@]}; do
        
        if [ $object == "/" ]; then
            echo -e "Trying to delete '/' skipping deletion task....\n"
            continue
        else

            is_option=$(isOption $object)

            if [ $is_option -eq 0 ]; then

                eShow=0
                delFile=0

                for protectedDirectory in ${protectedDirectories[@]}; do

                    isPathRelative=$(isRelative $object)

                    if [ $isPathRelative -eq 1 ];then
                        object=$(newObject $object)
                    fi
                
                    objectType=$(objectExist $object)

                    if [ ${#objectType} -gt 0 ]; then
                        
                        # object exist
                        totalOjectSegment=$(totalSegments $object)

                        if [[ $object =~ ^$protectedDirectory ]]; then
                            
                            # Check the current 
                            if [ $totalOjectSegment -lt $level ]; then

                                promptForDeletion $object $protectedDirectory 

                                if [ $? -eq 22 ]; then
                                    if [ $delFile -eq 0 ]; then
                                        deleteFile $object $@
                                        delFile=1
                                        eShow=1
                                    fi
                                fi

                            fi
                            
                        fi

                    else

                        # no object found to delete
                        if [ $eShow -eq 0 ]; then
                            echo -e "\nThe object '$object' does not exist for deletion\n"
                            eShow=1
                        fi

                    fi

                done 

                if [ $delFile -eq 0 ]; then
                    deleteFile $object $@
                fi

            else
                continue
            fi
        fi

    done
else
    # Single directories deletion detected 
    eShow=0
    delFile=0
    if [ $object == "/" ]; then
        echo -e "Trying to delete '/' exiting....\n"
        exit 0
    else

        for protectedDirectory in ${protectedDirectories[@]}; do
            isPathRelative=$(isRelative $params)
            object=$params

            if [ $isPathRelative -eq 1 ];then
                object=$(newObject $params)
            fi

            objectType=$(objectExist $object)
    
            if [ ${#objectType} -gt 0 ]; then

                totalOjectSegment=$(totalSegments $object)

                if [[ $object =~ ^$protectedDirectory ]]; then
                    # Check the current 

                    if [ $totalOjectSegment -lt $level ]; then
                        promptForDeletion $object $protectedDirectory 
                        
                        if [ $? -eq 22 ]; then
                            if [ $delFile -eq 0 ]; then
                                deleteFile $object
                                delFile=1
                                eShow=1
                            fi
                        fi
                    fi
                fi

            else
                # no object found to delete
                
                if [ $eShow -eq 0 ]; then
                    echo -e "\nThe object '$object' does not exist for deletion\n"
                    eShow=1
                fi
                
            fi

        done 

        if [ $delFile -eq 0 ]; then
            deleteFile $object $@
        fi
    fi

fi