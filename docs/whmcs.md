# WHMCS Setup

[← Back to Docs](README.md)

This guide walks through creating an API credential in WHMCS and connecting it to Sites Dashboard.

---

## 1. Create an API Role

In WHMCS, go to **System Settings > API Roles > Add Role**.

Give the role a name (e.g., `Sites Dashboard`) and enable the following permissions:

- AcceptOrder
- AddOrder
- AddTicketReply
- CreateInvoice
- DomainRenew
- DomainUpdateNameservers
- GetClients
- GetClientsDetails
- GetClientsDomains
- GetClientsProducts
- GetTicket
- GetTickets
- OpenTicket
- SendEmail
- UpdateClientDomain
- UpdateClientProduct
- UpdateTicket

---

## 2. Create Ticket Statuses

In WHMCS, go to **System Settings > Ticket Statuses**.

| Title                  | Include in Active Tickets | Include in Awaiting Reply | 
|------------------------|---------------------------|---------------------------| 
| Order Placed           | Yes                       | No                        | 
| Form Submitted         | Yes                       | No                        |
| Site Install Failed    | Yes                       | Yes                       |
| Site Not Provisioned   | Yes                       | No                        |
| Ready QA               | Yes                       | Yes                       |
| DNS Check Failed       | Yes                       | Yes                       |

If using the auto-release feature, you should also create the following:

| Title          | Include in Active Tickets | Include in Awaiting Reply | Auto Close? | 
|----------------|---------------------------|---------------------------|-------------| 
| Auto QA Failed | Yes                       | Yes                       | No          | 
| Auto QAed      | No                        | No                        | Yes         |

And configure auto close at **System Settings > Automation Settings > Support Ticket Settings > Close Inactive Tickets** and choose how many hours you would prefer (we like 48).

## 3. Create an API User (Optional)

This simply requires a WHMCS admin user to run the API calls. It is recommended to create a dedicated user for this purpose. But your own admin account can work too. 

---

## 4. Create API Credentials

Go to **System Settings > API Credentials > Generate API Credential**.

| Field       | Value                             |
|-------------|-----------------------------------|
| Admin User  | *[The user to run the API calls]* |
| Description | *[Something descriptive]*         |
| API Role(s) | *[Select the role from step 1]*   |

Click **Generate**. Copy both the **API Identifier** and **API Secret** — the secret is only shown once.

---

## 5. Add WHMCS Config in the Dashboard Admin

Navigate to **Admin > Interface > WHMCS Configs > Add WHMCS Config** and fill in:

| Field               | Value                                                  |
|---------------------|--------------------------------------------------------|
| Domain              | `example.com/whmcs/` *(WHMCS domain, no `https://`)*   |
| Admin Location      | `admin` *(path to your WHMCS admin panel, no slashes)* |
| API ID              | *[API Identifier from step 2]*                         |
| API Secret          | *[API Secret from step 2]*                             |
| Admin Email Domain  | *[Domain emails are sent from]*                        |
| Client Email Domain | *[Domain client's emails are sent from]*               |
| Admin Email Address | *[Email address of the server admin]*                  |
| Enabled             | True                                                   |

**Product IDs** (fill in based on your WHMCS product catalog):

| Field                             | Description                                                                           |
|-----------------------------------|---------------------------------------------------------------------------------------|
| Site Renewal Product IDs          | JSON list of product IDs for hosting plans that include a website (e.g., `[1, 2]`)    |
| No Site Renewal Product IDs       | JSON list of product IDs for plans that do **not** include a website (e.g., `[1, 2]`) |
| Legacy No Site Renewal Product ID | If there are old orders that require special legacy treatment                         |

> Only one WHMCS config can be enabled at a time. Enabling a config automatically disables every other config.

---

[← Back to Docs](README.md)
