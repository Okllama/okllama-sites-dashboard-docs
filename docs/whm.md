# WHM / WHMCS Server Setup

[← Back to Docs](README.md)

## New WHM server setup instructions

These instructions assume your dashboard and WHMCS are already set up. This is how you will setup a new WHM server and
connect it to the dashboard.

---

## WHM

### Basic WebHost Manager Setup

1. Scroll to the bottom and set the nameservers:
    - **Nameserver 1:** `[your nameserver]`
    - **Nameserver 2:** `[your other nameserver]`
2. Click **Save Changes**

---

### Tweak Settings

Disable the following options in the **Domains** section:

| Setting                                            | Value |
|----------------------------------------------------|-------|
| Enable SPF on domains for newly created accounts   | Off   |
| Enable DMARC on domains for newly created accounts | Off   |
| Service subdomains                                 | Off   | 

Click **Save**

---

### Edit Zone Templates

Open `standardvirtualftp` and comment out the following two lines by adding a `;` at the start of each:

- `mail IN CNAME %maildomain%.`
- `%domain%. IN MX 0 %domain%.`

The resulting template should look like this:

```
; cPanel %cpversion%
; Zone file for %domain%
$TTL %ttl%
@      %nsttl%	IN      SOA     %nameserver%. %rpemail%. (
		%serial%	; serial, todays date+todays
		3600		; refresh, seconds
		1800		; retry, seconds
		1209600		; expire, seconds
		86400 )		; minimum, seconds

%domain%. %nsttl% IN NS %nameserver%.
%domain%. %nsttl% IN NS %nameserver2%.
%domain%. %nsttl% IN NS %nameserver3%.
%domain%. %nsttl% IN NS %nameserver4%.

%nameserverentry%. IN A %nameservera%
%nameserverentry2%. IN A %nameservera2%
%nameserverentry3%. IN A %nameservera3%
%nameserverentry4%. IN A %nameservera4%

%domain%. IN A %ip%
%domain%. IN AAAA %ipv6%

;%domain%. IN MX 0 %domain%.

;mail IN CNAME %maildomain%.
www IN CNAME %domain%.
;ftp IN A %ftpip%
;ftp IN AAAA %ipv6%
```

---

### Add a Package

Navigate to **Packages > Add a Package** and configure with the following settings:

| Setting                                    | Value            |
|--------------------------------------------|------------------|
| Package Name                               | *[Product name]* |
| Disk Space Quota (MB)                      | `500`            |
| Monthly Bandwidth Limit (MB)               | `3000`           |
| Max FTP Accounts                           | Unlimited        |
| Max Email Accounts                         | Unlimited        |
| Max Mailing Lists                          | Unlimited        |
| Max SQL Databases                          | `2`              |
| Max Sub Domains                            | Unlimited        |
| Max Parked Domains                         | `3`              |
| Max Addon Domains                          | `0`              |
| Max Passenger Applications                 | `4`              |
| Maximum Hourly Email by Domain Relayed     | `20`             |
| Max % of failed/deferred messages per hour | `10`             |
| Max Quota per Email Address (MB)           | Unlimited        |
| Dedicated IP                               | False            |
| Shell Access                               | False            |
| CGI Access                                 | False            |
| Digest Authentication at account creation  | False            |
| cPanel Theme                               | `jupiter`        |
| Feature List                               | `default`        |
| Locale                                     | English          |

---

## WHMCS

### Add Server to WHMCS

1. Go to **System Settings > Servers > Add New Server**
2. Configure the server:

| Field                  | Value                                   |
|------------------------|-----------------------------------------|
| Module                 | `cPanel`                                |
| Hostname or IP Address | *[WHM Server domain/IP]*                |
| Username               | *[WHM Server username (normally root)]* |
| Password               | *[WHM User password]*                   |

3. Click **Test Connection**

4. Add DNS nameservers:

| Position             | Nameserver                       | IP Address     | 
|----------------------|----------------------------------|----------------|
| Primary Nameserver   | *[Nameserver address]*           | *[IP Address]* |
| Secondary Nameserver | *[Secondary Nameserver address]* | *[IP Address]* |

5. Click **Save Changes**
6. Add the new server to the **Website Servers** group

### Get New Server ID

1. On the `configservers.php` page, click **Edit** on the server you just created
2. Look in the URL bar for `&id=` — the number following that is the server ID
3. Note this for later

---

## DNS Manager

### Add Server to DNS Manager

1. Go to **Settings > Servers > Add Server**
2. Configure the server: **General**

| Field                      | Value               |
|----------------------------|---------------------|
| Name                       | *[WHM Server Name]* |
| Module                     | `cPanel`            |
| Allow rDNS Records         | True                |
| Enable DNSSEC              | True                |
| Allow Multiple PTR Records | True                |
| Enable Cache               | True                |
| Overwrite SOA Record       | True                |
| Populate Nameservers       | True                |

**Configuration**

| Field         | Value                                   |
|---------------|-----------------------------------------|
| Username      | *[WHM Server Username (normally root)]* |
| User Password | *[WHM Server Password]*                 |
| Hostname/IP   | *[WHM Server IP Address]*               |
| Enable SSL    | True                                    |
| Default IP    | *[WHM Server IP Address]*               |

**Nameservers**

| Field        | Value            |
|--------------|------------------|
| Nameserver 1 | *[Nameserver 1]* |
| Nameserver 2 | *[Nameserver 2]* |

Click **Confirm**

### Update SPF Record

Edit the **Prime Host Record List** record set to use the IP address of the new server.

**Example:**

```
Before: v=spf1 +a +mx +ip4:192.168.1.1 include:_spf.google.com ~all
After:  v=spf1 +a +mx +ip4:192.168.1.2 include:_spf.google.com ~all
```

### Update Package Server Assignment

1. Open the package for the desired product and go to the **Servers** tab
2. Click the trash icon next to the existing server
3. Click **Add Server** and choose the new server

---

## Change Server in PCS API Call

Ask the PCS team to update the server ID in the `IF Prime Host` statement to the new server ID obtained from WHMCS.

---

## Sites Dashboard

### Add API Key in WHM

Create an API token in WHM with the following configuration. All permissions should be **false** except those listed:

**Edit API Token**

| Field                    | Value                          |
|--------------------------|--------------------------------|
| Name                     | `Okllama_Dashboard`            |
| Should API Token Expire? | The API Token will not expire  |
| Whitelisted IPs          | *[Sites Dashboard IP address]* |

**Permissions (set to True)**

| Permission           | Value |
|----------------------|-------|
| Create User Session  | True  |
| List Accounts        | True  |
| Access to WP Toolkit | True  |

### Add WHM Config in Admin Interface

Navigate to **Add WHM Config** and fill in the following:

| Field    | Value                                                                                                       |
|----------|-------------------------------------------------------------------------------------------------------------|
| Domain   | *[WHM Server domain]*                                                                                       |
| Port     | `2087`                                                                                                      |
| API Key  | *[API key]*                                                                                                 |
| SSH User | `root`                                                                                                      |
| SSH Port | `11208`                                                                                                     |
| Priority | `1` *(push all existing entries back by one so the lowest number always belongs to the most recent server)* |
| Enabled  | True                                                                                                        |

### Setup Templates

Navigate to **Add Template** and fill in the following:

| Field        | Value                  |
|--------------|------------------------|
| Name         | *[name in lowercase]*  |
| Color        | *[color in lowercase]* |
| WHM Server   | *[WHM Server domain]*  |
| WHM ID       | *[site template ID]*   |
| Template Map | *(leave blank)*        |
| Enabled      | True                   |

---

[← Back to Docs](README.md)
