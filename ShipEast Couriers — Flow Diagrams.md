# ShipEast Couriers — Flow Diagrams

---

## 1. System Architecture Overview

```mermaid
graph TB
    subgraph "Client Applications"
        CA["Customer App<br/>(Flutter - Android/iOS)"]
        DA["Driver App<br/>(Flutter - Android/iOS)"]
        MP["Merchant Portal<br/>(Flutter Web / React)"]
        AD["Admin Dashboard<br/>(Flutter Web / React)"]
    end

    subgraph "API Gateway"
        AG["API Gateway<br/>(Node.js + Express)"]
    end

    subgraph "Backend Services"
        AS["Auth Service<br/>(Firebase Auth)"]
        OS["Order Service"]
        PS["Payment Service"]
        NS["Notification Service<br/>(FCM)"]
        TS["Tracking Service<br/>(WebSocket)"]
        RS["Rewards Service"]
        ALS["Analytics Service"]
    end

    subgraph "Database Layer"
        FS["Firebase Firestore<br/>(Real-time Data)"]
        PG["PostgreSQL<br/>(Relational Data)"]
        RD["Redis<br/>(Cache & Sessions)"]
    end

    subgraph "External Services"
        GM["Google Maps API"]
        WP["WiPay Caribbean"]
        PP["PayPal"]
        FCM["FCM"]
        S3["Cloud Storage"]
    end

    CA --> AG
    DA --> AG
    MP --> AG
    AD --> AG

    AG --> AS
    AG --> OS
    AG --> PS
    AG --> NS
    AG --> TS
    AG --> RS
    AG --> ALS

    OS --> FS
    OS --> PG
    PS --> WP
    PS --> PP
    NS --> FCM
    TS --> FS
    AS --> FS
    RS --> PG
    ALS --> PG

    TS --> GM
    AG --> RD
    OS --> S3
```

---

## 2. Customer Registration & Login Flow

```mermaid
flowchart TD
    A["App Launch"] --> B{"First Time User?"}
    B -->|Yes| C["Onboarding Screens<br/>(3-4 carousel slides)"]
    B -->|No| D["Login Screen"]
    
    C --> E["Registration Screen"]
    
    E --> F{"Registration Method"}
    F -->|Email| G["Enter Email + Password"]
    F -->|Phone| H["Enter Phone Number"]
    F -->|Social| I["Google / Apple / Facebook"]
    
    G --> J["Email Verification Screen"]
    H --> K["OTP Verification Screen"]
    I --> L["Social Auth Complete"]
    
    J --> M["Profile Setup"]
    K --> M
    L --> M
    
    M --> N["Add Default Address"]
    N --> O{"Local or Overseas?"}
    
    O -->|Local - Jamaica| P["Enter Jamaican Address<br/>+ GPS Pin"]
    O -->|Overseas| Q["Select Country<br/>(USA/UK/Canada)"]
    
    P --> R["Home Screen<br/>(Local View)"]
    Q --> S["Home Screen<br/>(Overseas View)"]
    
    D --> T{"Login Method"}
    T -->|Email| U["Email + Password"]
    T -->|Phone| V["Phone + OTP"]
    T -->|Social| W["Social Login"]
    
    U --> X{"Valid?"}
    V --> X
    W --> X
    
    X -->|Yes| Y{"User Type?"}
    X -->|No| Z["Error Message"] --> D
    
    Y -->|Local Customer| R
    Y -->|Overseas Customer| S

    D --> FA["Forgot Password"]
    FA --> FB{"Recovery Method"}
    FB -->|Email| FC["Enter Email"]
    FB -->|Phone| FD["Enter Phone"]
    FC --> FE["Check Email for Reset Link"]
    FD --> FF["OTP Verification"]
    FE --> FG["Reset Password Screen"]
    FF --> FG
    FG --> D
```

---

## 3. Food/Restaurant Order Flow

```mermaid
flowchart TD
    A["Home Screen"] --> B["Select 'Food Delivery'"]
    B --> C["Browse Restaurants<br/>(Filter: Cuisine, Rating, Distance)"]
    C --> D["Select Restaurant"]
    D --> E["View Menu<br/>(Categories, Items, Prices)"]
    E --> F["Add Items to Cart<br/>(Qty, Add-ons, Notes)"]
    F --> G{"Continue Shopping?"}
    G -->|Yes| E
    G -->|No| H["View Cart"]
    
    H --> I["Apply Promo Code<br/>(Optional)"]
    I --> J["Select Delivery Address"]
    J --> K["Select Delivery Time<br/>(ASAP / Schedule)"]
    K --> L["Select Payment Method<br/>(WiPay / PayPal / COD)"]
    
    L --> M["Order Summary<br/>(Items + Fees + Tax + Total)"]
    M --> N{"Confirm Order?"}
    N -->|No| H
    N -->|Yes| O["Order Placed"]
    
    O --> P["Push Notification<br/>'Order Confirmed'"]
    P --> Q["Merchant Receives Order"]
    
    Q --> R{"Merchant Decision"}
    R -->|Accept| S["Preparing Order"]
    R -->|Reject| T["Order Cancelled<br/>+ Refund Initiated"]
    
    S --> U["Order Ready"]
    U --> V["System Assigns Driver"]
    V --> W["Driver Accepts"]
    W --> X["Driver En Route to Pickup"]
    
    X --> Y["Driver Arrives at Restaurant"]
    Y --> Z["Driver Picks Up Order"]
    Z --> AA["Driver En Route to Customer<br/>(Live GPS Tracking)"]
    
    AA --> AB["Driver Arrives"]
    AB --> AC["Delivery Photo Taken"]
    AC --> AD["Order Delivered"]
    AD --> AE["Rate Driver & Restaurant<br/>(1-5 Stars + Review)"]
    AE --> AF["Reward Points Added"]
```

---

## 4. Grocery Order Flow

```mermaid
flowchart TD
    A["Home Screen"] --> B["Select 'Grocery Delivery'"]
    B --> C["Browse Grocery Stores"]
    C --> D["Select Store"]
    D --> E["Browse Products by Category"]
    
    E --> F{"Item Type?"}
    F -->|Weight-based| G["Select Weight<br/>(kg / lb)"]
    F -->|Unit-based| H["Select Quantity"]
    
    G --> I["Add Item Notes<br/>(e.g., 'ripe bananas')"]
    H --> I
    
    I --> J["Set Substitution Preference"]
    J --> K{"Substitution Option"}
    K -->|Option 1| L["Replace with similar item"]
    K -->|Option 2| M["Call me before substituting"]
    K -->|Option 3| N["Refund this item"]
    
    L --> O["Add to Cart"]
    M --> O
    N --> O
    
    O --> P{"More Items?"}
    P -->|Yes| E
    P -->|No| Q["Review Cart"]
    
    Q --> R["Select Delivery Address"]
    R --> S["Select Payment"]
    S --> T["Place Order"]
    
    T --> U["Driver Assigned<br/>(Driver performs item picking)"]
    U --> V["Driver Picks Items at Store"]
    
    V --> W{"Item Available?"}
    W -->|Yes| X["Item Added"]
    W -->|No| Y{"Check Substitution Pref"}
    
    Y -->|Replace| Z["Similar Item Added<br/>+ Customer Notified"]
    Y -->|Call| AA["Call Customer<br/>for Decision"]
    Y -->|Refund| AB["Item Refunded<br/>+ Amount Adjusted"]
    
    X --> AC["All Items Picked"]
    Z --> AC
    AA --> AC
    AB --> AC
    
    AC --> AD["Driver Delivers"]
    AD --> AE["Photo Confirmation"]
    AE --> AF["Delivered"]
```

---

## 5. Errands/Custom Delivery Flow

```mermaid
flowchart TD
    A["Home Screen"] --> B["Select 'Errands'"]
    B --> C["Custom Request Form"]
    
    C --> D["Describe What You Need<br/>(Text Description)"]
    D --> E["Upload Images<br/>(Up to 5 photos)"]
    E --> F["Set Pickup Location(s)"]
    
    F --> G{"Multiple Stops?"}
    G -->|Yes| H["Add More Pickup Stops<br/>(Multi-Stop)"]
    G -->|No| I["Set Drop-off Location"]
    H --> I
    
    I --> J["Add Special Instructions"]
    
    J --> K["Dynamic Price Calculation"]
    
    K --> L["Price Based On:"]
    L --> M["Distance between stops"]
    L --> N["Number of stops"]
    L --> O["Item weight/size estimate"]
    L --> P["Urgency: Standard (1x) or Express (1.5x)"]
    
    M --> Q["Price Quote Shown"]
    N --> Q
    O --> Q
    P --> Q
    
    Q --> R{"Accept Price?"}
    R -->|No| S["Cancel / Modify Request"]
    S --> C
    R -->|Yes| T["Payment"]
    
    T --> U["Errand Request Submitted"]
    U --> V["Nearby Drivers Notified"]
    V --> W["Driver Accepts"]
    W --> X["Real-time Tracking Begins"]
    X --> Y["Pickup Completed"]
    Y --> Z{"More Stops?"}
    Z -->|Yes| AA["Navigate to Next Stop"]
    AA --> Y
    Z -->|No| AB["En Route to Drop-off"]
    AB --> AC["Photo Delivery Confirmation"]
    AC --> AD["Errand Complete"]
    AD --> AE["Rate Driver"]
```

---

## 6. Overseas Ordering Flow

```mermaid
flowchart TD
    A["Overseas Customer<br/>(USA/UK/Canada)"] --> B["Login / Register<br/>(Country Selector)"]
    B --> C["Overseas Home Screen"]
    C --> D["Browse Jamaican Merchants<br/>& Product Categories"]
    D --> E["Select Items & Add to Cart"]
    
    E --> F["Enter Jamaican Recipient Details"]
    F --> G["Recipient Name"]
    F --> H["Jamaican Address"]
    F --> I["Jamaican Phone Number"]
    F --> J["Add Personal Message<br/>(Optional)"]
    
    G --> K["Delivery Options"]
    H --> K
    I --> K
    J --> K
    
    K --> L{"Delivery Type?"}
    L -->|ASAP| M["Deliver Now"]
    L -->|Scheduled| N["Select Date & Time"]
    L -->|Recurring| O["Set Frequency<br/>(Weekly/Bi-weekly/Monthly)"]
    
    M --> P["Price in Foreign Currency<br/>(USD / GBP / CAD)<br/>Exchange rate locked at order time"]
    N --> P
    O --> P
    
    P --> Q["International Payment<br/>(PayPal / WiPay)"]
    Q --> R["Order Placed"]
    
    R --> S["Confirmation Email + Push<br/>to Overseas Customer"]
    S --> T["Jamaican Merchant Notified"]
    T --> U["Local Driver Assigned"]
    U --> V["Real-time Status Updates<br/>to Overseas Customer"]
    V --> W["Delivered to Recipient"]
    W --> X["Photo Confirmation<br/>Sent to Overseas Customer"]
    X --> Y["Personal Message Delivered<br/>with Package"]
    Y --> Z["Overseas Customer<br/>Rates & Reviews"]
    
    Z --> AA["Order History<br/>(Displayed in Foreign Currency)"]
```

---

## 7. Service Marketplace Booking Flow

```mermaid
flowchart TD
    A["Home Screen"] --> B["Services Marketplace"]
    B --> C["Browse Service Categories<br/>(Plumber, Cleaner, Electrician,<br/>Mechanic, Tutor, etc.)"]
    C --> D["Browse Service Providers<br/>(Ratings, Portfolio, Prices)"]
    D --> E["View Provider Profile"]
    
    E --> F{"Booking Type?"}
    F -->|Direct Book| G["Select Date & Time"]
    F -->|Request Quote| H["Describe Job +<br/>Upload Photos"]
    
    H --> I["Quote Request Sent<br/>to Provider"]
    I --> J["Provider Sends Quote"]
    J --> K{"Accept Quote?"}
    K -->|No| L["Decline / Try Another Provider"]
    L --> D
    K -->|Yes| G
    
    G --> M["Job Description<br/>& Special Instructions"]
    M --> N["Payment<br/>(Deposit or Full)"]
    N --> O["Booking Confirmed"]
    
    O --> P["Both Parties Notified"]
    P --> Q["Service Date Arrives"]
    Q --> R["Provider Performs Service"]
    R --> S["Mark as Complete"]
    S --> T["Final Payment<br/>(if deposit was partial)"]
    T --> U["Customer Rates Provider<br/>(1-5 Stars + Review)"]
    U --> V["Added to Order History"]

    O --> PC{"Provider Cancels?"}
    PC -->|Yes| PCA["Customer Notified"]
    PCA --> PCB["Deposit Auto-Refunded"]
    PCB --> PCC["Suggest Alternative Providers"]
    PCC --> D
```

---

## 8. Driver App — Order Lifecycle Flow

```mermaid
flowchart TD
    A["Driver Opens App"] --> B["Toggle: Go Online"]
    B --> C["GPS Location Shared<br/>(Live Tracking Active)"]
    C --> D["Waiting for Orders..."]
    
    D --> E["New Order Alert!<br/>(Sound + Vibration + Push)"]
    
    E --> F["Order Preview:<br/>Pickup Location<br/>Delivery Location<br/>Estimated Earnings<br/>Distance"]
    
    F --> G{"Decision (30s timer)"}
    G -->|Accept| H["Order Accepted"]
    G -->|Reject| I["Order Rejected<br/>Sent to Next Driver"]
    G -->|Timer Expires| I
    I --> D
    
    H --> J["Navigate to Pickup<br/>(Google Maps)"]
    J --> K["Tap: 'Arrived at Pickup'"]
    K --> L["Collect Order from Merchant"]
    L --> M["Tap: 'Order Picked Up'"]
    
    M --> N["Navigate to Customer<br/>(Google Maps)"]
    N --> O["Tap: 'Arrived at Delivery'"]
    O --> P["Take Delivery Photo<br/>(MANDATORY)"]
    P --> Q["Tap: 'Order Delivered'"]
    
    Q --> R["Order Complete!"]
    R --> S["Earnings Updated"]
    S --> T["View Earnings Dashboard<br/>(Daily/Weekly/Monthly)"]
    
    T --> D

    C --> OFF{"Goes Offline Mid-Delivery?"}
    OFF -->|Active Order| OFFA["System waits 2 min<br/>for reconnection"]
    OFFA --> OFFB{"Reconnected?"}
    OFFB -->|Yes| OFFC["Continue delivery"]
    OFFB -->|No| OFFD["Admin alerted<br/>Order can be reassigned"]
    
    style E fill:#ff6b6b
    style H fill:#51cf66
    style R fill:#51cf66
```

---

## 9. Driver Registration & Verification Flow

```mermaid
flowchart TD
    A["Driver Downloads App"] --> B["Registration Form"]
    B --> C["Enter Personal Info<br/>(Name, Phone, Email, Photo)"]
    C --> D["Enter Vehicle Details<br/>(Type, Make, Model, Color, Plate)"]
    D --> E["Upload Documents"]
    
    E --> F["Driver's Licence<br/>(Front & Back)"]
    E --> G["Vehicle Registration"]
    E --> H["Insurance (Optional)"]
    
    F --> I["Submit Registration"]
    G --> I
    H --> I
    
    I --> J["Pending Approval Screen<br/>(Cannot go online yet)"]
    J --> K["Admin Receives<br/>Verification Request"]
    
    K --> L["Admin Reviews Documents"]
    L --> M{"Documents Valid?"}
    
    M -->|Approved| N["Driver Status: Approved"]
    M -->|Rejected| O["Driver Status: Rejected<br/>(Reason Provided)"]
    
    N --> P["Push Notification:<br/>'You are approved!'"]
    O --> Q["Push Notification:<br/>'Documents rejected - reason'"]
    
    P --> R["Driver Can Go Online"]
    Q --> S["Driver Re-uploads<br/>Corrected Documents"]
    S --> I
```

---

## 10. Merchant Registration & Approval Flow

```mermaid
flowchart TD
    A["Merchant Visits Portal"] --> B["Registration Form"]
    B --> C["Business Details<br/>(Name, Type, Description)"]
    C --> D["Upload Logo & Cover Image"]
    D --> E["Business Address + GPS Pin"]
    E --> F["Set Operating Hours<br/>(Per Day of Week)"]
    F --> G["Contact Information"]
    G --> H["Bank / Payment Details<br/>(For Payouts)"]
    
    H --> I["Submit Registration"]
    I --> J["Pending Approval Screen"]
    
    J --> K["Admin Receives Request"]
    K --> L["Admin Reviews Business Info"]
    
    L --> M{"Business Legitimate?"}
    M -->|Approved| N["Merchant Status: Active"]
    M -->|Rejected| O["Merchant Status: Rejected<br/>(Reason Provided)"]
    
    N --> P["Set Commission Rate<br/>(Admin configures)"]
    P --> Q["Notification: 'Approved!'<br/>Can receive orders"]
    Q --> R["Merchant Adds<br/>Menu / Products"]
    R --> S["Portal Fully Active"]
    
    O --> T["Notification: 'Rejected - reason'"]
    T --> U["Merchant Can Reapply<br/>with Updated Info"]
    U --> I
```

---

## 11. Merchant Order Management Flow

```mermaid
flowchart TD
    A["Merchant Portal"] --> B["Dashboard<br/>(Today's Orders, Revenue)"]
    
    B --> C["New Order Notification!<br/>(Sound + Push)"]
    C --> D["View Order Details<br/>(Items, Customer, Address)"]
    
    D --> E{"Decision"}
    E -->|Accept| F["Order Accepted"]
    E -->|Reject| G["Order Rejected<br/>Rejection Reason Required<br/>Customer Notified + Refund"]
    
    F --> H["Set Preparation Time<br/>(e.g., 25 minutes)"]
    H --> I["Prepare Order"]
    I --> J["Mark as 'Ready for Pickup'"]
    J --> K["Driver Notified:<br/>'Order Ready'"]
    K --> L["Driver Picks Up"]
    L --> M["Order Complete"]
    
    M --> N["Revenue Updated"]
    N --> O["View Reports<br/>(Sales, Top Items, Revenue)"]
    
    subgraph "Menu Management"
        P["Add/Edit Menu Items"]
        Q["Upload Item Photos"]
        R["Set Prices"]
        S["Toggle Availability"]
        T["Manage Categories"]
    end
    
    subgraph "Payout Tracking"
        U["View Payouts"]
        V["Commission Breakdown"]
        W["Export Reports"]
    end
```

---

## 12. Admin Dashboard — Order Dispute Resolution Flow

```mermaid
flowchart TD
    A["Admin Dashboard"] --> B["Dispute Reported<br/>(by Customer/Merchant/Driver)"]
    
    B --> C["View Dispute Details"]
    C --> D["Review:<br/>Order Details<br/>Timeline<br/>Photos<br/>Messages"]
    
    D --> E{"Dispute Type"}
    
    E -->|Missing Items| F["Verify with Merchant<br/>& Delivery Photo"]
    E -->|Wrong Order| G["Arrange Re-delivery<br/>or Refund"]
    E -->|Late Delivery| H["Check Timeline<br/>& Driver GPS History"]
    E -->|Quality Issue| I["Review Customer Photos<br/>& Merchant Response"]
    E -->|Payment Issue| J["Check Payment<br/>Gateway Records"]
    
    F --> K{"Resolution"}
    G --> K
    H --> K
    I --> K
    J --> K
    
    K -->|Full Refund| L["Process Full Refund"]
    K -->|Partial Refund| M["Process Partial Refund"]
    K -->|Store Credit| N["Issue Store Credit<br/>/ Promo Code"]
    K -->|Re-delivery| O["Arrange Re-delivery"]
    K -->|No Action| P["Dispute Rejected<br/>(with Reason)"]
    
    L --> Q["Notify All Parties"]
    M --> Q
    N --> Q
    O --> Q
    P --> Q
    
    Q --> R["Update Analytics<br/>& Dispute Records"]
    R --> RA["Log Action in Audit Trail"]
```

---

## 13. Payment Processing Flow

```mermaid
flowchart TD
    A["Customer at Checkout"] --> B{"Payment Method?"}
    
    B -->|WiPay| C["WiPay Caribbean<br/>(JMD - Local)"]
    B -->|PayPal| D["PayPal<br/>(USD/GBP/CAD - International)"]
    B -->|Cash on Delivery| E["COD Selected"]
    
    C --> F["Secure Payment Gateway"]
    D --> F
    
    F --> G{"Payment Status"}
    G -->|Success| H["Payment Confirmed"]
    G -->|Failed| I["Payment Failed<br/>Retry / Change Method"]
    G -->|Timeout| IA["Payment Pending<br/>Retry up to 3x<br/>(Exponential Backoff)"]
    I --> B
    IA --> IB{"Retry Succeeded?"}
    IB -->|Yes| H
    IB -->|No| I
    
    H --> J["Order Created in System"]
    E --> J
    
    J --> K["Revenue Recorded"]
    
    K --> L["Commission Calculation"]
    L --> M["Platform Fee: X%"]
    L --> N["Merchant Share: Y%"]
    L --> O["Driver Pay: Z"]
    
    M --> P["Admin Revenue Dashboard"]
    N --> Q["Merchant Payout Queue"]
    O --> R["Driver Earnings"]
    
    Q --> S["Scheduled Payout<br/>(Daily/Weekly)"]
    R --> T["Driver Payout<br/>(Daily/Weekly)"]
    
    subgraph "Refund Flow"
        U["Refund Requested"]
        V["Admin Reviews"]
        W["Refund Processed<br/>(Original Payment Method)"]
        X["Audit Log Entry Created"]
        U --> V --> W --> X
    end
```

---

## 14. Push Notification Flow

```mermaid
flowchart TD
    A["Order Event Occurs"] --> B{"Event Type"}
    
    B -->|Order Placed| C["To Customer: 'Order Confirmed!'<br/>To Merchant: 'New Order!'"]
    B -->|Merchant Accepts| D["To Customer: 'Order Being Prepared'"]
    B -->|Driver Assigned| E["To Customer: 'Driver Assigned - [Name]'<br/>To Driver: 'New Pickup Assignment'"]
    B -->|Picked Up| F["To Customer: 'Order Picked Up!'"]
    B -->|En Route| G["To Customer: 'Driver En Route'<br/>(with ETA)"]
    B -->|Delivered| H["To Customer: 'Order Delivered!'<br/>With Delivery Photo"]
    B -->|Cancelled| I["To All Parties: 'Order Cancelled'<br/>With Reason"]
    B -->|Overseas Delivered| HA["To Overseas Customer: 'Delivered!'<br/>With Photo + Message Confirmation"]
    
    C --> J["Firebase Cloud Messaging"]
    D --> J
    E --> J
    F --> J
    G --> J
    H --> J
    HA --> J
    I --> J
    
    J --> K["Push to Device"]
    J --> L["In-App Notification Centre"]
    J --> M["Email (Optional)"]
    
    subgraph "Admin Broadcasting"
        N["Admin Creates Broadcast"]
        O{"Target Audience"}
        O -->|All Users| P["Send to All"]
        O -->|Customers| Q["Send to Customers"]
        O -->|Drivers| R["Send to Drivers"]
        O -->|Merchants| S["Send to Merchants"]
        O -->|Specific Zone| SA["Send to Zone Users"]
        N --> O
    end
```

---

## 15. Rewards System Flow

```mermaid
flowchart TD
    A["Order Completed"] --> B["Calculate Reward Points<br/>(X points per $100 JMD)"]
    
    B --> C["Points Added to<br/>Rewards Wallet"]
    C --> D["Check Tier Status"]
    
    D --> E{"Current Points Total"}
    E -->|0-999| F["Bronze Tier"]
    E -->|1000-4999| G["Silver Tier<br/>(2x Points Multiplier)"]
    E -->|5000+| H["Gold Tier<br/>(3x Points + Free Delivery)"]
    
    F --> I{"Milestone Check"}
    G --> I
    H --> I
    
    I -->|10th Order| J["10th Order Bonus!<br/>+500 Points"]
    I -->|50th Order| K["50th Order Bonus!<br/>+2000 Points"]
    I -->|100th Order| L["100th Order Bonus!<br/>+5000 Points"]
    I -->|None| M["Continue Earning"]
    
    J --> N["Rewards Wallet"]
    K --> N
    L --> N
    M --> N
    
    N --> O{"Redeem Points?"}
    O -->|Discount| P["Apply Points<br/>for Order Discount"]
    O -->|Free Delivery| Q["Free Delivery<br/>(X Points)"]
    O -->|Save| R["Keep Saving"]
    
    subgraph "Referral System"
        S["Customer Gets<br/>Unique Referral Code"]
        T["Share Code with<br/>Friends & Family"]
        U["Friend Signs Up<br/>& Places First Order"]
        V["Both Earn<br/>Referral Bonus Points!"]
        S --> T --> U --> V
    end
```

---

## 16. Complete Order State Machine

```mermaid
stateDiagram-v2
    [*] --> PLACED: Customer places order
    
    PLACED --> CONFIRMED: Merchant accepts
    PLACED --> CANCELLED: Merchant rejects / Customer cancels
    
    CONFIRMED --> PREPARING: Merchant starts preparation
    PREPARING --> READY: Order ready for pickup
    
    READY --> DRIVER_ASSIGNED: System assigns driver
    DRIVER_ASSIGNED --> DRIVER_AT_PICKUP: Driver arrives at merchant
    DRIVER_AT_PICKUP --> PICKED_UP: Driver collects order
    
    PICKED_UP --> EN_ROUTE: Driver heads to customer
    EN_ROUTE --> DRIVER_AT_DELIVERY: Driver arrives at customer
    DRIVER_AT_DELIVERY --> DELIVERED: Photo confirmation + handoff
    
    DELIVERED --> COMPLETED: Customer confirms / auto-complete
    COMPLETED --> [*]
    
    CANCELLED --> REFUND_PENDING: If payment was made
    REFUND_PENDING --> REFUNDED: Refund processed
    REFUNDED --> [*]
    CANCELLED --> [*]: If COD / no payment
    
    CONFIRMED --> CANCELLED: Unexpected cancellation
    PREPARING --> CANCELLED: Unexpected cancellation
    EN_ROUTE --> CANCELLED: Emergency cancellation (admin only)
    
    DRIVER_ASSIGNED --> WAITING_FOR_DRIVER: Driver non-responsive (5 min timeout)
    WAITING_FOR_DRIVER --> DRIVER_ASSIGNED: New driver accepts
```

---

## 17. Scheduled & Recurring Order Flow

```mermaid
flowchart TD
    A["Customer Creates<br/>Scheduled Order"] --> B{"Order Type?"}
    
    B -->|One-Time Scheduled| C["Select Date & Time"]
    B -->|Recurring| D["Select Frequency<br/>(Weekly / Bi-Weekly / Monthly)"]
    
    C --> E["Payment Processed<br/>(Charged Immediately)"]
    D --> F["Payment Method Saved<br/>(Charged Before Each Delivery)"]
    
    E --> G["Order in 'Scheduled' State"]
    F --> H["Recurring Schedule Created"]
    
    G --> I["Trigger Time Arrives"]
    I --> J["Order Converted to Active<br/>(Status: PLACED)"]
    J --> K["Normal Order Flow Begins"]
    
    H --> L["Next Delivery Date Approaches"]
    L --> M["24h Before: Attempt Payment"]
    
    M --> N{"Payment Successful?"}
    N -->|Yes| O["Order Auto-Created<br/>(Status: PLACED)"]
    O --> K
    N -->|No| P["Retry After 24h"]
    
    P --> Q{"2nd Attempt Successful?"}
    Q -->|Yes| O
    Q -->|No| R["3rd and Final Retry"]
    
    R --> S{"3rd Attempt Successful?"}
    S -->|Yes| O
    S -->|No| T["Schedule PAUSED"]
    T --> U["Customer Notified:<br/>'Update payment method'"]
    
    H --> V["Customer Manages Schedule"]
    V --> VA["Pause Schedule"]
    V --> VB["Resume Schedule"]
    V --> VC["Modify Items / Recipient"]
    V --> VD["Cancel Schedule Permanently"]
```

---

## 18. App Module Architecture

```mermaid
graph TB
    subgraph "Customer App Modules"
        AUTH["Auth Module<br/>(Login/Register/Social)"]
        HOME["Home Module<br/>(Service Sections)"]
        FOOD["Food Module<br/>(Restaurants/Menu)"]
        GROC["Grocery Module<br/>(Stores/Products)"]
        ERR["Errands Module<br/>(Custom Requests)"]
        OVER["Overseas Module<br/>(International Orders)"]
        SERV["Services Module<br/>(Marketplace)"]
        CART["Cart Module<br/>(Checkout/Payment)"]
        TRACK["Tracking Module<br/>(Live GPS Map)"]
        PROF["Profile Module<br/>(Settings/Addresses)"]
        NOTIF["Notification Module"]
        REWARD["Rewards Module<br/>(Points/Tiers)"]
        ORDER["Order History Module"]
        RATE["Ratings Module"]
        CHAT["Chat Module<br/>(Built, Disabled in V1)"]
    end

    AUTH --> HOME
    HOME --> FOOD
    HOME --> GROC
    HOME --> ERR
    HOME --> OVER
    HOME --> SERV
    
    FOOD --> CART
    GROC --> CART
    ERR --> CART
    OVER --> CART
    SERV --> CART
    
    CART --> TRACK
    TRACK --> RATE
    RATE --> REWARD
    
    PROF --> ORDER
    PROF --> REWARD
    HOME --> NOTIF
```

---

## 19. Driver Assignment Logic

```mermaid
flowchart TD
    A["New Order Needs Driver"] --> B["Query Online Drivers<br/>in Delivery Zone"]
    
    B --> C{"Drivers Available?"}
    C -->|No| D["Order Status: WAITING_FOR_DRIVER<br/>Merchant + Customer Notified"]
    D --> E["Admin Can Manually Assign"]
    
    C -->|Yes| F["Rank Drivers By:<br/>1. Proximity to Pickup<br/>2. Rating<br/>3. Acceptance Rate"]
    
    F --> G["Send to Top-Ranked Driver<br/>(30-second timer)"]
    
    G --> H{"Driver Response"}
    H -->|Accept| I["Order Assigned!<br/>Customer + Merchant Notified"]
    H -->|Reject| J["Move to Next Driver"]
    H -->|Timer Expires| J
    
    J --> K{"More Drivers in List?"}
    K -->|Yes| G
    K -->|No| L{"Retry Round < 3?"}
    
    L -->|Yes| M["Wait 60 seconds<br/>Re-query Drivers"]
    M --> B
    L -->|No| D
```
