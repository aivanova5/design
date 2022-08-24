The purpose of the monitor application is to provide the TESS system technical support staff the ability to monitor the status and repair the TESS system during operations.

# User Page Flow

See [Page layouts](https://docs.google.com/presentation/d/1VtxWEh3BZM9dYI3MDHqG4p__TPV337L0yp4-b1spSNk) for mockups.

## Authentication

```mermaid
flowchart LR

  Email --LINK--> Welcome
  Welcome --OK--> Warning/Login 
  
  Warning/Login --Help--> Help
  Warning/Login --Password--> Password
  
  Help --Send--> Warning/Login
  
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
    +graph MainGraph
    +ShowDispatches()
    +ShowPrices()
    +ShowBids()
  }
  class MainTab {
    +button Main
    +button Auction
    +button Database
    +button Users
    +button Controllers
    +button Monitors
    +button Billing
    +SelectMain()
    +SelectAuction()
    +SelectDatabase()
    +SelectUsers()
    +SelectControllers()
    +SelectMonitors()
    +SelectBilling()
  }
  class Graph {
    +plot Image
    +button ZoomIn
    +button ZoomOut
    +button PanLeft
    +button PanRight
    +RefreshData(source)
    +ZoomIn()
    +ZoomOut()
    +PanLeft()
    +PanRight()
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
