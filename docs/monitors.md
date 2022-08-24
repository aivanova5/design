The purpose of the monitor application is to provide the TESS system technical support staff the ability to monitor the status and repair the TESS system during operations.

# User Page Flow

## Authentication

```mermaid
flowchart LR

  Welcome --OK--> Warning/Login 
  
  Warning/Login --Email--> Email
  Warning/Login --Password--> Password
  
  Email --Send--> Warning/Login
  
  Password --Send--> TwoFactor --Code--> Main
```

## Main

```mermaid
flowchart TD

  Main --Select--> Auction --Select--> Main  
  Main --Select--> Database --Select--> Main
  Main --Select--> Users --Select--> Main
  Main --Select--> Controllers --Select--> Main
  Main --Select--> Monitors --Select--> Main
  Main --Select--> Billing --Select--> Main
```

```mermaid
classDiagram
   class Main {
      +tab MainTab
      +tab GraphTab
      +ShowDispatches()
      +ShowPrices()
      +ShowBids()
    }
```

## Auction

```mermaid
flowchart LR
  Auction --> Agents
  Auction --> Bids
  Auction --> Dispatch
  
  Agents --> Devices
```
