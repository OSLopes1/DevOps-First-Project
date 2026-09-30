# LinkedIn Learning DevOps Foundations: Your First Project

This project is part of a ourse available on LinkedIn. All credits to [Carlos Nunez](https://www.linkedin.com/learning/instructors/carlos-nunez) Throughout the course I will make notes for my own engagment and reference.

## What you should know

- Comfortable with Linux shell
- Basic programming experience (optional)
- Experience with editor (we use VS code he used vim)
- AWS account - AWS has 6-month free, which I will see if it is okay for the course

## Configuring AWS

We need to create temporary administrator AWS credentials
- Open [AWS Console](https://console.aws.amazon.com)
- Check the region at the top right of the screen and make a note of the region code
    - Mine was Europe (Stockholm) and the code was __eu-north-1__
- Next we need to create a user that we can log in with &rarr; search & select IAM
    - At this stage I was prompted to set up Multi Factor Authentication (MFA), which I recommend
- Select Create New User and assign whatever name you want, leave the defaults select next &rarr; next &rarr; create user
- Create role that allows us to become administrator:
    - Go to Roles &rarr; create role
    - Select "AWS account" and "require external ID"
    - Type in your own external ID and select next
    - Check administrator access and select next
    - Create a role name and then select create role
    - Once created select view role &rarr; trust relationships
    - Edit Trust relationship &rarr; edit trust policy &rarr; replace root with "user/`your_user_name`" &rarr; click updat policy to apply the changes
- Next we want to generate credentials so we can assume the role we just created.
    - Go to users and select the user we just created
    - Select create access key
    - Select other &rarr; next &rarr; Create access key
    - __Leave this window open for now__
- Open a new tab and go back to [AWS Console](https://console.aws.com) and navigate back to IAM
- Go back to roles and search for the role we created
- Open a terminal, ensure you have the [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) installed
    - In the terminal type `aws configure`, upon which you will be asked to provide an access key
    - Go back to browser we have the create access key on, copy & paste the access key
    - repeat for the secret access key
    - Next enter the region name from earlier
    - Write `json` for the output format
    - I was asked if I want to configure AWS skills and AWS MCP server, as this was not in the course i opted for no.
- Now we want to set up the temporary credentials:
    - Write 
    ```
    aws sts assume-role --role-arn <copy_from_role_tab> --external-id <earlier_created_pw> --role-session-name MySession
    ```
    - Hit enter and you should see a json style output
- To confirm the set up was successful lets run a test:
    - Run `aws iam list-users` &larr; this should give an error saying access denied. This is correct because we did not assign the user this permission.
- To get admin access we:
    - Set the access key environmental variable. For windows command prompt use `set AWS_ACCESS_KEY_ID=<your_access_key>`
    - Next set the secret access key environment variable. `set AWS_SECRET_ACCESS_KEY=<your_secret_key>` &larr; He recommends using single quotes areound the key. However I found it the test later did not work until I removed the single quotes.
    - We create session token environmental variable. `set AWS_SESSION_TOKEN='<really_long_json_string>'`
    - Finally we create the AWS region environmental variable. `set AWS_REGION=<your_region>`
- Now run the test again `aws iam list-users`. You should now get a list of the users instead of an error.
- These credentials usually expire after an hour. therefore we will need to run the assume-role command repeatadly throughout the project

```
aws sts assume-role --role-arn <copy_from_role_tab> --external-id <earlier_created_pw> --role-session-name MySession
```
Then you need to reset the environmental variables with those in the new json file.


