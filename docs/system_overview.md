```mermaid
flowchart TD
  
  classDef active stroke-width:4px;

  devices([Devices]) -- OpenADR --> agents  
  agents[Agents] -- REST --> gateway[API Gateway]

  billing([Billing User]) --> billing_app[Billing App]:::active -- REST --> gateway
      click billing_app "https://github.com/tess-v2/design/blob/main/docs/billing.md" _blank

  users([Users]) --> user_app[User App]:::active -- REST --> gateway
      click user_app "https://github.com/tess-v2/design/blob/main/docs/users.md" _blank

  controllers([Controllers]) --> controller_app[Controller App]:::active -- REST --> gateway
      click controller_app "https://github.com/tess-v2/design/blob/main/docs/controllers.md" _blank

  monitors([Monitors]) --> monitor_app[Monitor App]:::active -- REST --> gateway
      click monitor_app "https://github.com/tess-v2/design/blob/main/docs/monitors.md" _blank

  experiment([Experimenters]) --> experiment_app[Experiment App]:::active -- REST --> gateway
      click experiment_app "https://github.com/tess-v2/design/blob/main/docs/experimenters.md" _blank

  gateway --> auth[Cognito]
  
  gateway --> s3[S3]
    s3 --> www([Websites])
  
  gateway --> ec2[EC2]
    ec2 --> elb((ELB))
    ec2 --> ebs([EBS])
    ec2 --> dns((DNS))
    ec2 --> route53((Route53))
    ec2 --> vpc((VPC))
    ec2 --> sg((SG))
  
  gateway --> lambda[Lambda]
    lambda --> sqs((SQS))
    lambda --> sns((SNS))
    lambda ---> auction[Auction]:::active
      click auction "https://github.com/tess-v2/design/blob/main/docs/auction.md" _blank
      auction ---> database[(Database)]    
    lambda ---> device[Device]:::active
      click device "https://github.com/tess-v2/design/blob/main/docs/device.md" _blank
      device ---> database[(Database)]:::active
      click database "https://github.com/tess-v2/design/blob/main/docs/database.md" _blank
      
  gateway --> amplify[Amplify]
  
  gateway --> other[Other services]
  
```

# Component Design 

## Applications
* [Billing](billing.md)
* [Users](users.md)
* [Controllers](controllers.md)
* [Monitors](monitors.md)
* [Experimenters](experimenters.md)

## APIs
* [Auction](auction.md)
* [Device](device.md)

## Data storage
* [Database](database.md)

