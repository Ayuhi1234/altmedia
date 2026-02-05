%% Frontend Layer
subgraph &quot;Frontend Layer (Client Side)&quot;
    UI_User[User Dashboard (dashbord.html)]
    UI_Auth[Login/Signup (login.html)]
    UI_Admin[Admin Panel]
end

%% Interaction
User --&gt;|Browser| UI_Auth
User --&gt;|Browser| UI_User
Admin --&gt;|Browser| UI_Admin

%% API Gateway / Load Balancer
API_GW[API Gateway / Routes]
UI_User --&gt;|HTTP Requests (JSON)| API_GW
UI_Auth --&gt;|HTTP Requests (JSON)| API_GW
UI_Admin --&gt;|HTTP Requests (JSON)| API_GW

%% Backend Layer
subgraph &quot;Backend Layer (Server)&quot;
    Auth_Svc[&lt;b&gt;Authentication Module&lt;/b&gt;<br>Login, OTP, Role Checks]
    User_Svc[&lt;b&gt;User &amp; KYC Module&lt;/b&gt;<br>Profile, Docs, Tree Structure]
    Order_Svc[&lt;b&gt;Order System&lt;/b&gt;<br>Services, Status, Invoices]
    Wallet_Svc[&lt;b&gt;Wallet &amp; Income&lt;/b&gt;<br>Referral (15%), Team (5 Levels), Payouts]
    Supp_Svc[&lt;b&gt;Support Module&lt;/b&gt;<br>Tickets, Notifications]
    
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

%% Database Layer
subgraph &quot;Database Layer&quot;
    DB_Users[(Users &amp; KYC Data)]
    DB_Orders[(Orders &amp; Transactions)]
    DB_Wallet[(Wallets &amp; Withdrawals)]
    DB_Logs[(Audit Logs)]
end

%% DB Connections
User_Svc &lt;--&gt; DB_Users
Order_Svc &lt;--&gt; DB_Orders
Wallet_Svc &lt;--&gt; DB_Wallet
Auth_Svc &lt;--&gt; DB_Users
Supp_Svc &lt;--&gt; DB_Logs

%% External Services
subgraph &quot;External Services&quot;
    Storage[File Storage (AWS S3/Local)<br>KYC Docs, Excel, Invoices]
    Mail_Server[Email Service (SMTP)<br>Invoices, Alerts]
    WA_API[WhatsApp API<br>Notifications]
end

%% External Connections
User_Svc --&gt;|Upload Docs| Storage
PDF_Eng --&gt;|Save PDF| Storage
Order_Svc --&gt;|Send Update| WA_API
Order_Svc --&gt;|Send Invoice| Mail_Server
