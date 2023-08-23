1. Setting up ssh for EC2 on windows 11
  a. permission denied error on key file ?
    - open git and use below command to provide permission to file
      - chmod 400 key_file.pem
    - if you get error for above command then right click on file and goto properties -> security tab provide permission to all users group. Retry the above comamnd
  b. command to connect to ec2 via shh
    - ssh -i key_file.pem username@ec2publicip
    - here user name for amazon linux OS is ec2_user and for ubuntu OS user is ubuntu 
3.   
