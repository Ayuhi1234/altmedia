%% Frontend Layer subgraph "Frontend Layer (Client Side)" UI_User[User Dashboard (dashbord.html)] UI_Auth[Login/Signup (login.html)] UI_Admin[Admin Panel] end

%% Interaction User -->|Browser| UI_Auth User -->|Browser| UI_User Admin -->|Browser| UI_Admin

%% API Gateway / Load Balancer API_GW[API Gateway / Routes] UI_User -->|HTTP Requests (JSON)| API_GW UI_Auth -->|HTTP Requests (JSON)| API_GW UI_Admin -->|HTTP Requests (JSON)| API_GW

%% Backend Layer subgraph "Backend Layer (Server)" Auth_Svc[<b>Authentication Module</b>
Login, OTP, Role Checks] User_Svc[<b>User & KYC Module</b>
Profile, Docs, Tree Structure] Order_Svc[<b>Order System</b>
Services, Status, Invoices] Wallet_Svc[<b>Wallet & Income</b>
Referral (15%), Team (5 Levels), Payouts] Supp_Svc[<b>Support Module</b>
Tickets, Notifications]

%% Logic Connections
API_GW --&gt; Auth_Svc
API_GW --&gt; User_Svc
API_GW --&gt; Order_Svc
API_GW --&gt; Wallet_Svc
API_GW --&gt; Supp_Svc

%% Internal Logic
Order_Svc --&gt;|Trigger: Order Completed| Wallet_Svc
Order_Svc --&gt;|Trigger: Generate Invoice| PDF_Eng[PDF Generator Engine]
end

%% Database Layer subgraph "Database Layer" DB_Users[(Users & KYC Data)] DB_Orders[(Orders & Transactions)] DB_Wallet[(Wallets & Withdrawals)] DB_Logs[(Audit Logs)] end

%% DB Connections User_Svc <--> DB_Users Order_Svc <--> DB_Orders Wallet_Svc <--> DB_Wallet Auth_Svc <--> DB_Users Supp_Svc <--> DB_Logs

%% External Services subgraph "External Services" Storage[File Storage (AWS S3/Local)
KYC Docs, Excel, Invoices] Mail_Server[Email Service (SMTP)
Invoices, Alerts] WA_API[WhatsApp API
Notifications] end

%% External Connections User_Svc -->|Upload Docs| Storage PDF_Eng -->|Save PDF| Storage Order_Svc -->|Send Update| WA_API Order_Svc -->|Send Invoice| Mail_Server
