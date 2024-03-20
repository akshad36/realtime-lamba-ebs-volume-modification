# realtime-lamba-ebs-volume-modification
We use AWS cloud watch in combination with AWS Lambda to govern the resources according to the policies

Create a new lambda function from scratch as EBS_VOLUME_CHECKS-using runtime as pythongo with default IAM role and create the function, where you can see the test page
![ebs1](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/299a3b4a-207f-48d3-a474-c4c3efc553ab)

now go to the cloud watchunder events go to rulescreate a new ruleservice as eczselect the name of the event as per our project (ebs volume notification) specify events as creation of volumeany volume ARNselect target as lambda function that we wrote above(ebs_volume_checks)
![ebs2](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/52cb72d3-d04d-48f9-a1b3-96c184beb2c7)
Note: cloudwatch rules now moved to AWS eventbridge

now go to IAM roles to edit the existing lamda role that we created by default, edit the role by going with inline permissionstinline policies are useful when there is a direct one-to-one relationship between the policy and the user of group)->
![ebs3](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/634e2703-87f0-47b2-b53a-92d2212383de)

Note: In AWS Identity and Access Management (IAM), when you set the permissions for an identity. you have three options AWS managed policy, customer managed policy, or inline policy

we can see the basic python lambda function: 
 Now, edit the lambda function🡪add print(event)..i.e it is importing event in json from cloudwatch logs. So to verify the event we are using print the event.-->test is from the cloudwatch logs by creating a new volume
 ![ebs4](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/607e83ce-0696-442f-93ac-d1ddbdb73eba)
 
![ebs5](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/16f64485-5f80-4d04-aa98-97c1c8282a92)

Now, we can notify the complete details of the EBS volume which is in the json data:-
![ebs6](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/7c6a6d27-4411-4363-a621-fb53a36af701)
![ebs7](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/05bacc17-44f2-447f-bfbd-8b3d98a7bf76)
(i.e., here we are doing parsing:- Parsing JSON means converting JSON (JavaScript Object Notation) data into a format that can be easily used and manipulated in a programming language)
![ebs8](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/72b65002-d9ab-4a1b-a568-1465b72c0eec)

i.e., volume_arn= event [resources][0] (It means from the json event resources first entry-as 0)🡪we are giving to our lambda def under get_volume_id

-similarly we are getting volume-id from parsing as mentioned below:
volume_id= get_volume_id_from_arn(volume_arn)
![ebs9](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/6e5290ed-b52d-4527-8860-4d0e41dc611b)

now go to boto3 modify_volume documentation for syntax to convert our gp2 volume id into gp3
-as we know ebs falls under ec2, so I am calling the ec2_client=boto3.client(‘ec2’)
So if we put all together, the final code to deploy in lambda as:-

mport boto3

def get_volume_id_from_arn(volume_arn):
    # Split the ARN using the colon(':') separator
    arn_parts = volume_arn.split(':')
    # The volume ID is the last part of the ARN after the 'volume/'  prefix
    volume_id = arn_parts[-1].split('/')[-1]
    return volume_id
    
    
def lambda_handler(event, context):
    
    volume_arn = event['resources'][0]
    volume_id = get_volume_id_from_arn(volume_arn)
    
    ec2_client = boto3.client('ec2')
    
    response = ec2_client.modify_volume(
        VolumeId=volume_id,
        VolumeType='gp3',
    )
Note: create any gp2 volume and check whether it is automatically converted into gp3.

![ebs10](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/16305915-1893-4498-b6f0-178983fb76b6)
![ebs11](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/39089024-d815-4849-8d6a-8dec49d551be)
![ebs12](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/ded2f726-a8d4-4294-b1bc-b23124053599)
![ebs13](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/0d3ed6fa-2d9e-4e21-bc40-6ff4a0cd26ac)
![ebs14](https://github.com/akshad36/realtime-lamba-ebs-volume-modification/assets/88645921/1d47f1fa-6365-4508-b2a7-0484e7eb4c3b)






