# System Architecture
```mermaid
flowchart TD
  
  classDef active stroke-width:4px;

  devices([Devices]) --OpenADR/CTA2045--> agents  
  agents[Agents]:::active --REST--> gateway[API Gateway]
      click agents "https://github.com/postroad-energy/design/blob/main/docs/agents.md" _blank

  participant([Participants]) --HTTPS--> participant_app[Participant App]:::active --REST--> gateway
      click participant_app "https://github.com/postroad-energy/design/blob/main/docs/participants.md" _blank

  controllers([Controllers]) --HTTPS--> controller_app[Controller App]:::active --REST--> gateway
      click controller_app "https://github.com/postroad-energy/design/blob/main/docs/controllers.md" _blank

  settlement([Settlement]) --HTTPS--> settlement_app[Settlement App]:::active --REST--> gateway
      click billing_app "https://github.com/postroad-energy/design/blob/main/docs/settlement.md" _blank

  operators([Operators]) --HTTPS--> operator_app[Operator App]:::active --REST--> gateway
      click operator_app "https://github.com/postroad-energy/design/blob/main/docs/operators.md" _blank

  experiment([Experimenters]) --HTTPS--> experiment_app[Experiment App]:::active --REST--> gateway
      click experiment_app "https://github.com/postroad-energy/design/blob/main/docs/experimenters.md" _blank

  analysis([Analysts]) --HTTPS--> analysis_app[Analysis App]:::active --REST--> gateway
      click analysis_app "https://github.com/postroad-energy/design/blob/main/docs/analysis.md" _blank
  
  developers([Developers]) --HTTPS--> simulation[Simulation]:::active --REST--> gateway
      click simulation "https://github.com/postroad-energy/design/blob/main/docs/simulation.md" _blank
  
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
    lambda --> auction[Auction]:::active --REST--> db_api  
      click auction "https://github.com/postroad-energy/design/blob/main/docs/auction.md" _blank
    lambda --> orderbook[Orderbook]:::active --REST--> db_api  
      click orderbook "https://github.com/postroad-energy/design/blob/main/docs/orderbook.md" _blank
    lambda --REST--> db_api[Database API]:::active --SQL--> database[(Database)]:::active
      click db_api "https://github.com/postroad-energy/design/blob/main/docs/database.md" _blank
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
  
  participant --Device Interface--> device
  
  auction --"REST"--> database_api
    click database "https://github.com/postroad-energy/design/blob/main/docs/database.md" _blank
  
  participant([Participant]) --HTTPS--> participant_app[Participant App]:::active --REST--> database_api[Database API]:::active --SQL--> database
    click participant_app "https://github.com/tess-cc/design/blob/main/docs/participants.md" _blank
    click db_api "https://github.co/postroad-energy/design/blob/main/docs/database.md" _blank

  controller([Controller]) --HTTPS--> controller_app[Controller App]:::active --REST--> database_api
      click controller_app "https://github.com/postroad-energy/design/blob/main/docs/controller.md" _blank

  settlement([Settlement]) --HTTPS--> settlement_app[Settlement App]:::active --REST--> database_api
      click settlement_app "https://github.com/postroad-energy/design/blob/main/docs/settlement.md" _blank

  operators([Operators]) --HTTPS--> operator_app[Operator App]:::active --REST--> database_api
      click operator_app "https://github.com/postroad-energy/design/blob/main/docs/operators.md" _blank

  experiment([Experimenters]) --HTTPS--> experiment_app[Experiment App]:::active --REST--> database_api
      click experiment_app "https://github.com/postroad-energy/design/blob/main/docs/experimenters.md" _blank

  developer([Developers]) --GridLAB-D--> simulation[Simulation]:::active --GLM--> database_api
      click simulation "https://github.com/postroad-energy/design/blob/main/docs/simulation.md" _blank
```

# Component Design 

## Applications
* [Operators](operators.md)
* [Controllers](controllers.md)
* [Settlement](settlement.md)
* [Participants](participants.md)
* [Experimenters](experimenters.md)
* [Simulation](simulation.md)

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
    * [OpenADR 2.0b Specification](https://cimug.ucaiug.org/Projects/CIM-OpenADR/Shared%20Documents/Source%20Documents/OpenADR%20Alliance/OpenADR_2_0b_Profile_Specification_v1.0.pdf)

