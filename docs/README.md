# System Architecture
```mermaid
flowchart TD
  
  classDef active stroke-width:4px;

  devices([Devices]) --OpenADR--> agents  
  agents[Agents]:::active --REST--> gateway[API Gateway]
      click agents "https://github.com/postroad-energy/design/blob/main/docs/agents.md" _blank

  users([Users]) --HTTPS--> user_app[User App]:::active --REST--> gateway
      click user_app "https://github.com/postroad-energy/design/blob/main/docs/users.md" _blank

  controllers([Controllers]) --HTTPS--> controller_app[Controller App]:::active --REST--> gateway
      click controller_app "https://github.com/postroad-energy/design/blob/main/docs/controllers.md" _blank

  billing([Billing User]) --HTTPS--> billing_app[Billing App]:::active --REST--> gateway
      click billing_app "https://github.com/postroad-energy/design/blob/main/docs/billing.md" _blank

  monitors([Monitors]) --HTTPS--> monitor_app[Monitor App]:::active --REST--> gateway
      click monitor_app "https://github.com/postroad-energy/design/blob/main/docs/monitors.md" _blank

  experiment([Experimenters]) --HTTPS--> experiment_app[Experiment App]:::active --REST--> gateway
      click experiment_app "https://github.com/postroad-energy/design/blob/main/docs/experimenters.md" _blank

  gateway --> auth[Cognito]
  
  gateway --> s3[S3]
    s3 --> websites([Websites]):::active
      click websites "https://github.com/postroad-energy/design/blob/main/docs/websites.md" _blank
  
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
    lambda --> auction[Auction]:::active
      click auction "https://github.com/postroad-energy/design/blob/main/docs/auction.md" _blank
      auction --API--> database  
    lambda --> device[Device]:::active
      click device "https://github.com/postroad-energy/design/blob/main/docs/device.md" _blank
      device --API--> database[(Database)]:::active
      click database "https://github.com/postroad-energy/design/blob/main/docs/database.md" _blank
      
  gateway --> amplify[Amplify]
  
  gateway --> other[Other services]
  
```

# Information Flow

```mermaid
flowchart LR

  classDef active stroke-width:4px;

  agent[Agent]:::active --"REST"--> auction[Auction]:::active
    click agent "https://github.co/postroad-energy/design/blob/main/docs/agent.md" _blank
    click auction "https://github.com/postroad-energy/design/blob/main/docs/auction.md" _blank
    
  agent --"OpenADR"--> device[Device]:::active
  agent --"CTA2045"--> device
    click device "https://github.com/postroad-energy/design/blob/main/docs/device.md" _blank
  
  user --Device Interface--> device
  
  auction --"SQL"--> database[(Database)]:::active
    click database "https://github.com/postroad-energy/design/blob/main/docs/database.md" _blank
  
  user([User]) --HTTPS--> user_app[User App]:::active --REST--> database_api[db_api]:::active --SQL--> database
    click user_app "https://github.com/tess-cc/design/blob/main/docs/users.md" _blank
    click db_api "https://github.co/postroad-energy/design/blob/main/docs/database.md" _blank

  controller([Controller]) --HTTPS--> controller_app[Controller App]:::active --REST--> database_api
      click controller_app "https://github.com/postroad-energy/design/blob/main/docs/controller.md" _blank

  billing([Billing User]) --HTTPS--> billing_app[Billing App]:::active --REST--> database_api
      click billing_app "https://github.com/postroad-energy/design/blob/main/docs/billing.md" _blank

  monitors([Monitors]) --HTTPS--> monitor_app[Monitor App]:::active --REST--> database_api
      click monitor_app "https://github.com/postroad-energy/design/blob/main/docs/monitors.md" _blank

  experiment([Experimenters]) --HTTPS--> experiment_app[Experiment App]:::active --REST--> database_api
      click experiment_app "https://github.com/postroad-energy/design/blob/main/docs/experimenters.md" _blank

```

# Component Design 

## Applications
* [Billing](billing.md)
* [Users](users.md)
* [Controllers](controllers.md)
* [Monitors](monitors.md)
* [Experimenters](experimenters.md)

## APIs
* [Agents](agents.md)
* [Auction](auction.md)
* [Database](database.md)
* [Device](device.md)

## Data systems
* [Database](database.md)

# Reference Documents

* Device Communications
  * [CTA-2045](https://shop.cta.tech/products/modular-communications-interface-for-energy-management)
    * [SkyCentrics](https://skycentrics.com)
  * OpenADR
    * [OpenADR Alliance](https://openadr.org/)
    * [Introduction to OpenADR 2.0](https://www.openadr.org/assets/docs/understanding%20openadr%202%200%20webinar_11_10_11_sm.pdf)
    * [OpenADR 2.0b Specification](https://cimug.ucaiug.org/Projects/CIM-OpenADR/Shared%20Documents/Source%20Documents/OpenADR%20Alliance/OpenADR_2_0b_Profile_Specification_v1.0.pdf)]

