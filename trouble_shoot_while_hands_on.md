1. Setting up ssh for EC2 on windows 11
  a. permission denied error on key file?
    - Open git and use the below command to provide permission to file
      - chmod 400 key_file.pem
    - If you get an error for the above command then right-click on the file and go to properties -> security tab provides permission to all user groups. Retry the above command.
  b. command to connect to ec2 via shh
    - ssh -i key_file.pem username@ec2publicip
    - User name for Amazon Linux OS is ec2_user and for ubuntu OS user name is ubuntu 
3.   
