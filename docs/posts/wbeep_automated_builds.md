# Automated Builds

The project includes Docker and Jenkins files as well as a build shell script to support 
automated building of the Vue app via Jenkins (tiles are built in a separate process). 

The build process will remove all existing files from the targeted S3 bucket except for
the tiles directories and their contents.

A variety of build scenarios are supported via build parameters in Jenkins:
- The BUILD_DEST parameter has four options, 'test', 'qa', 'beta', and 'prod'
    Each of these choices correspond to one of our four S3 buckets and indicate the where the Jenkins build job will deliver
    the build version of the project.
- The VUE_BUILD_MODE parameter has two options, 'development' and 'production', this option tells the Vue application which
    environment variables to use. There are several feature differences between the 'development' and 'production' versions of 
    the application that are controlled by these variables. However, the main significance here, is that the variables control which of
    our S3 buckets 'test' or 'prod' from which the map will grab the tiles. 
- Combining the two options above in different ways allows either version of the application to be served from any of the
    various tiers. The main use of this would be to build the production version of the application to the 'test' deployment 
    tier, which would allow us to pull the 'production' version of the tiles to the map on the 'test' tier.
- The last parameter is the BRANCH_TAG, this is used in versioning. The most common choice will be 'origin/master'. 
    This will build the 'development' version of the application to the 'test' S3 bucket