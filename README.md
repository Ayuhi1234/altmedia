graph TD
    %% Users
    User[User / Client]
    Admin[Admin / Staff]

    %% Frontend Layer
    subgraph "Frontend (Client Side)"
        UI_User[User Dashboard<br>(dashbord.html + Firebase SDK)]
        UI_Auth[Login/Signup<br>(login.html + Firebase SDK)]
        UI_Admin[Admin Panel<br>(HTML + Firebase SDK)]
    end

    %% Interaction
    User --> UI_Auth
    User --> UI_User
    Admin --> UI_Admin

    %% Firebase Ecosystem
    subgraph "Firebase Backend (Serverless)"
        %% Auth
        FB_Auth[<b>Firebase Authentication</b><br>Manage Users, Roles]
        
        %% Database
        FB_DB[(<b>Firestore NoSQL DB</b><br>Users, Orders, Wallets)]
        
        %% Storage
        FB_Store[<b>Firebase Storage</b><br>KYC Images, PDFs]

        %% Logic (Cloud Functions)
        subgraph "Cloud Functions (Business Logic)"
            Func_Ref[<b>Referral Trigger</b><br>Calc 15% & 5-Level Team Income]
            Func_Order[<b>Order Trigger</b><br>On 'Completed' -> Unlock Income]
            Func_PDF[<b>Invoice Generator</b><br>Create PDF & Upload]
            Func_Notif[<b>Notification Service</b><br>Send Email/WhatsApp]
        end
    end

    %% Connections - Frontend to Firebase
    UI_Auth -->|Sign In/Up| FB_Auth
    UI_User -->|Read/Write Data| FB_DB
    UI_Admin -->|Approve KYC/Orders| FB_DB
    UI_User -->|Upload KYC| FB_Store

    %% Connections - Internal Firebase Triggers
    FB_DB -.->|Trigger: Order Update| Func_Order
    FB_DB -.->|Trigger: New User| Func_Ref
    
    %% Logic Flow
    Func_Order -->|Update Wallet| FB_DB
    Func_Order -->|Trigger PDF| Func_PDF
    Func_PDF -->|Save PDF| FB_Store
    Func_PDF -->|Get Download URL| FB_DB
    Func_Order -->|Send Alert| Func_Notif

    %% External Services
    subgraph "External APIs"
        Mail_Server[Email Service<br>(SendGrid/Nodemailer)]
        WA_API[WhatsApp API]
    end

    %% External Connections
    Func_Notif -->|Send Invoice| Mail_Server
    Func_Notif -->|Send Update| WA_API
