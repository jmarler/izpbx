# Name
[<img src="https://www.initzero.it/images/initzero-logo-izpbx-48x48.png">](https://www.initzero.it) izPBX Cloud Native VoIP Telephony System

# Description
izPBX is a Turnkey Cloud Native VoIP Telephony System powered by Asterisk Engine and FreePBX Management GUI

> **About this fork:** this is a fork of [ugoviti/izpbx](https://github.com/ugoviti/izpbx) that keeps the izPBX image patched.
> Container images are built with GitHub Actions and published to the GitHub Container Registry as **`ghcr.io/jmarler/izpbx-asterisk`**.
> Release `20.16.22` fixes the critical PHP remote code execution vulnerabilities CVE-2026-109618 and CVE-2026-109619 (see [Security: PHP CVE-2026-109618 and CVE-2026-109619](#security-php-cve-2026-109618-and-cve-2026-109619)).
> The upstream Docker Hub images (`docker.io/izdock/izpbx-asterisk`, up to `20.16.21`) are vulnerable: don't use them.

# Supported tags

## Production Branch (`ghcr.io/jmarler/izpbx-asterisk`):
* `20.16`, `20.16.X`, `20.16.X-BUILD`, `20`, `latest` (Asterisk 20 LTS + FreePBX 16)
* `main`: latest build of the `main` branch (published only when the workflow is run manually)

## Upstream only (`docker.io/izdock/izpbx-asterisk`, not maintained by this fork, vulnerable PHP):
* `18.16`, `18.16.X`, `18.16.X-BUILD`,`18` (Asterisk 18 LTS + FreePBX 16)
* `18.15`, `18.15.X`, `18.15.X-BUILD` (Asterisk 18 LTS + FreePBX 15)
* `16.15`, `16.15.X`, `16.15.X-BUILD` (Asterisk 16 LTS + FreePBX 15)
* Development branches: `dev-20`, `dev-20.X`, `dev-20.X.X-BUILD`, `dev-18`, `dev-18.X`, `dev-18.X.X-BUILD`

## Version notes:
Tags format: **Z.Y.X-[BUILD]**

where:  
  * **Z** = Asterisk release (PBX engine)
  * **Y** = FreePBX release (PBX GUI)
  * **X** = izPBX release (this project)
  * **BUILD** = Build number (GitHub Actions run number)

Look into the [izpbx-asterisk package](https://github.com/jmarler/izpbx/pkgs/container/izpbx-asterisk) page to discover the latest versions

# Dockerfile
- https://github.com/jmarler/izpbx/blob/main/izpbx-asterisk/Dockerfile

# Targets of this project
Cloud and On-Premise, Fast, Automatic and Repeatable deploy of VoIP PBX systems

# Features
- Fast initial bootstrap to deploy a full features PBX system (60 secs install time from zero to a running turnkey PBX system)
- Built-in PBX Engine based on Asterisk® project (compiled from scratch)
- Built-in WEB Management GUI based on FreePBX® project (with default predownloaded modules for quicker initial deploy)
- No vendor lock-in, you can migrare to izPBX and away izPBX simply importing/exporting FreePBX Backups
- Based on Rocky Linux 8 64bit OS (RHEL derivate with long term support)
- Small container image footprint (~450 MB vs 2300 MB of official FreePBX ISO distro file)
- Multi-Tenant PBX System Support (look into **Advanced Production Configuration Examples** section)
- Automatic Remote XML PhoneBook support for compatible VoIP Phones
- Persistent storage mode for configuration and not volatile data
- Fail2ban as security monitor to block SIP and HTTP brute force attacks
- FOP2 Operator Panel
- Integrated Asterisk Zabbix agent for services health monitoring
- Misc `izpbx-*` tools scripts (like `izpbx-callstats`)
- `izsynth` utility - TTS/Text To Speech synthesizer, background music overlay assembler and audio file converter for PBX and Home Automation Systems
- `tcpdump` and `sngrep` utility to debug VoIP calls
- supervisord as services management with monitoring and automatic restart when services fail
- postfix MTA daemon for sending mails (notifications, voicemails and FAXes)
- Integrated cron daemon for running scheduled tasks
- Integrated TFTP and DHCP server powered by DNSMasq for autoprovisioning VoIP Phones
- Integrated NTP Server
- Apache 2.4 and PHP 7.4 (mpm_prefork+mod_php configuration mode)
- Automatic Let's Encrypt HTTPS certificate management for exposed PBXs to Internet
- Custom commercial SSL Certificates support
- Logrotating of service logs
- All configurations made via single central `.env` file
- Many customizable variables to use (look inside `default.env` file)
- Only two containers setup: (**Antipattern container design** but needed by the FreePBX ecosystem to works)
  - **izpbx** (izpbx-asterisk container: Asterisk Engine + FreePBX Frontend + others services)
  - **izpbx-db** (mariadb container: Database Backend)

# Screenshots
#### izPBX Dashboard (FreePBX):
![izpbx-dashboard](https://raw.githubusercontent.com/ugoviti/izpbx/main/izpbx-asterisk/screenshots/izpbx-dashboard.png)

#### izPBX Operator Panel (FOP2):
![izpbx-izpbx-operator-panel](https://raw.githubusercontent.com/ugoviti/izpbx/main/izpbx-asterisk/screenshots/izpbx-operator-panel.png)

#### izPBX Monitoring Dashboard (Zabbix):
![izpbx-zabbix-dashboard](https://raw.githubusercontent.com/ugoviti/izpbx/main/izpbx-asterisk/screenshots/izpbx-zabbix-dashboard.png)

#### izPBX CLI (Asterisk):
![izpbx-console](https://raw.githubusercontent.com/ugoviti/izpbx/main/izpbx-asterisk/screenshots/izpbx-cli.png)
  
# Security: PHP CVE-2026-109618 and CVE-2026-109619
On 21 October 2026 PHP disclosed two critical remote code execution vulnerabilities ([PHP security advisory](https://www.php.net/archive/2026.php#2026-10-21-5), CVSS 4.0 score 9.2):
- **CVE-2026-109618** (GHSA-w8hw-w9fx-j79w): RCE via out of memory corruption
- **CVE-2026-109619** (GHSA-6wmf-4rv5-p3rp): RCE via stale global state

Every PHP release before 8.2.35, 8.3.36, 8.4.27 and 8.5.12 is affected. Apache `mod_php`, the mode izPBX uses, is rated the highest risk: an unauthenticated HTTP request can trigger the attack before any FreePBX code runs.

## Why izPBX stays on PHP 7.4
FreePBX 16 supports only PHP 7.4. The fixed PHP 8.x releases need FreePBX 17, which can't upgrade in place from FreePBX 16. When the vulnerabilities were disclosed, Rocky Linux 8 had no fixed PHP package (its `php:7.4` stream was `7.4.33-6`, its `php:8.2` stream `8.2.33`).

## What changed (izPBX 20.16.22)
- **Patched PHP 7.4 build:** the Dockerfile has a new first stage (`php-build`) that downloads the Rocky Linux 8 `php:7.4` source package and rebuilds it with the PHP project's minimal fix for 7.4 (php-src branch `PHP-7.4-CVE-2026-109618-109619`, commit `712e60f9`). The patch is rebased onto the distro sources, which already carry part of it: [`izpbx-asterisk/php-patches/7.4/php-7.4.33-cve-2026-109618-109619.patch`](izpbx-asterisk/php-patches/7.4/php-7.4.33-cve-2026-109618-109619.patch).
- **Patched packages replace the distro ones:** the rebuilt packages (`7.4.33-6.1.izpbx.el8`) are installed over the distro packages, and the image build fails if they aren't installed.
- **Skipped automatically when Rocky Linux ships a fix:** once the distro package changelog lists both CVEs, the rebuild stage does nothing and the distro packages are used. A newer distro release also replaces the izPBX build.
- **Upload progress disabled:** `session.upload_progress.enabled = Off` is set in `/etc/php.d/99-local.ini`, as the advisory recommends (the patched build disables the feature anyway).
- **Images published by this fork:** `compose.yml` pulls `ghcr.io/jmarler/izpbx-asterisk` instead of the vulnerable upstream Docker Hub image.

## Behavior changes from the fix
- When a PHP request exceeds `memory_limit`, the process now prints the fatal error to the log and exits, instead of returning a PHP error page. Apache starts a new worker, and the client gets an empty response.
- PHP session upload progress (`session.upload_progress`) is no longer available. FreePBX installed and ran normally without it in testing.

## Verify your container
```
docker exec izpbx rpm -q --changelog php | head -2
```
The first entry must read `izPBX rebuild with security fixes for CVE-2026-109618 CVE-2026-109619`, or come from a newer Rocky Linux package that lists both CVEs.

# Deploy izPBX
Using **docker compose** is the suggested method:

- Install your prefered Linux OS into VM or Baremetal Server

- Install Docker Runtime with `docker compose` plugin for your Operating System from https://www.docker.com/get-started
  - RHEL8 based distro Quick&Dirty commands (skip if you use other distribution):
```
sudo dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo
sudo dnf install docker-ce docker compose-plugin -y
sudo systemctl enable --now docker
```

- Create a `compose.yml`, or clone git repository, or download latest tarbal release from: https://github.com/jmarler/izpbx/releases and unpack it into a directory (ex. `/opt/izpbx`), faster method with git:
  - `git clone https://github.com/jmarler/izpbx.git /opt/izpbx`
  - `cd /opt/izpbx`

- Checkout into latest official release:
  - `git checkout tags/$(git tag | sort --version-sort | tail -1)`

- Copy default configuration file `default.env` into `.env`:
  - `cp default.env .env`

- Customize `.env` variables, specially the security section of default passwords:
  - `vim .env`

- Deploy and start izpbx using `docker compose` command:
  - `docker compose up -d`

- Wait the pull of the `ghcr.io/jmarler/izpbx-asterisk` image to finish (~60 seconds with fast internet connections) and point your web browser to the IP address of your docker host and follow initial setup guide

**Note:** `compose.yml` also contains a `build:` section: when the image tag can't be pulled from ghcr.io, `docker compose up -d` builds it locally from `./izpbx-asterisk` (it takes a while: PHP and Asterisk are compiled from source). To always build locally run `docker compose build izpbx` before `docker compose up -d`.

**Note:** by default, to correctly handle SIP NAT and SIP-RTP UDP traffic, the izpbx container will use the `network_mode: host`, so the izpbx container will be exposed directly to the outside network without using docker internal network range (**network_mode: host** will prevent multiple izpbx containers from running inside the same host).  
Modify compose.yml and comment `#network_mode: host` if you want run multiple izpbx containers in the same host (not production tested. There will be problems with RTP traffic).
Another available option is to disable `network_mode: host` and use **macvlan** network mode used for running izPBX into multi-tenant mode.

## Customizing compose.yml

If you need to customize the default `compose.yml`, don't edit it, but create an **override** file:

- `docker compose.override.yml`

and specify the options you want to override, for example:

```
version: '3'

services:
  db:
    image: docker.io/mariadb:10.11.4
  izpbx:
    image: ghcr.io/jmarler/izpbx-asterisk:20.16.22
```

and start the deploy with the same command `docker compose up -d`

## Alternative deploy method via 'docker run' command (not suggested)
If you want test izPBX without using docker compose command, you can use the following docker commands:

1. Start MySQL:  
`docker run --rm -ti -v ./data/db:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=CHANGEM3 -e MYSQL_PASSWORD=CHANGEM3 --name izpbx-db mariadb:10.4`

2. Start izPBX:  
`docker run --rm -ti --network=host --privileged --cap-add=NET_ADMIN -v ./data/izpbx:/data -e MYSQL_ROOT_PASSWORD=CHANGEM3 -e MYSQL_PASSWORD=CHANGEM3 -e MYSQL_SERVER=127.0.0.1 -e MYSQL_DATABASE=asterisk -e MYSQL_USER=asterisk -e APP_DATA=/data --name izpbx ghcr.io/jmarler/izpbx-asterisk:latest`

# Upgrade izPBX

1. Upgrade the version of izpbx by downloading a new tgz release, or changing image tag into **compose.yml** file (from git releases page, verify if upstream docker compose was updated), or if you cloned directly from GIT, use the following commands as quick method:
```
cd /opt/izpbx
git checkout main
git pull
git fetch --tags --all -f
git checkout tags/$(git tag | sort --version-sort | tail -1)
```

2. Upgrade the **izpbx** deploy with:  
(NB. **First** verify if `compose.yml` and `default.env` was updated a make the same changes in your `.env` file)
```
docker compose pull
docker compose up -d
```

## Migrate an existing deploy from the upstream Docker Hub image
If your `compose.yml` still uses `docker.io/izdock/izpbx-asterisk`, change only the image of the `izpbx` service (keep your `.env` and `data` directory):
```
    image: ghcr.io/jmarler/izpbx-asterisk:20.16.22
```
then backup the `data` directory, and recreate the container with:
```
docker compose pull izpbx
docker compose up -d
```

# Build and publish images (GitHub Actions)
The [Docker image workflow](.github/workflows/docker-publish.yml) builds `izpbx-asterisk` for `linux/amd64` and publishes it to `ghcr.io/jmarler/izpbx-asterisk`:
- **Release:** push a version tag (ex. `git tag 20.16.22 && git push origin 20.16.22`) to publish `20.16.22`, `20.16.22-BUILD`, `20.16`, `20` and `latest`
- **Manual:** run the workflow from the GitHub **Actions** tab to publish the selected branch (ex. `main`)
- **Pull requests:** changes to `izpbx-asterisk/` are built but not published

The first published image is private: make it public from the package settings (**Packages > izpbx-asterisk > Package settings > Change visibility**), or run `docker login ghcr.io` (with a GitHub token with `read:packages` scope) on the docker hosts before pulling.

3. If the mariadb database version was changed, rememeber to update tables schema with command  
  `source .env ; docker exec -it izpbx-db mysql_upgrade -u root -p$MYSQL_ROOT_PASSWORD`

4. Open FreePBX Web URL and verify if exist any modules updates from FreePBX Menu: **Admin --> Modules Admin --> Check Online --> Upgade all --> Process**

That's all

### Upgrade path to a major FreePBX release
FreePBX will be installed into persistent data dir on initial deploy only (when no installations already exist).

Successive container updates on the same release (example from 18.15.1 to 18.15.2) will not upgrade the FreePBX Framework (only Asterisk engine will be updated).  

If you want upgrade FreePBX Framework/Core to a major release (example from 15 to 16), you have 2 options:  
  
1. Automatic upgrade using izPBX container release (example, switching from 18.15.x to 18.16.x release)
2. Manual upgrade using **FreePBX Upgrader** tool

### method 1: Automatic upgrade using izPBX container release (suggested)
* Make sure before switching izPBX to a new major release, all FreePBX modules are updated to the latest release
* Make sure you made a full backup of `data` dir (IMPORTANT!)
* Make sure you enabled `FREEPBX_AUTOUPGRADE_CORE=true` in the `.env` file
* Deploy the latest version of izPBX (ex. 18.15.x for FreePBX 15, 18.16.x for FreePBX 16, etc...)
* izPBX should be detect an old FreePBX version installed and start the upgrade process
* From the **FreePBX / Modules Admin** page, check if all modules are been updated and enable again disabled modules

### method 2: Manual upgrade using FreePBX Upgrader tool
* Make sure before switching izPBX to a new major release, all FreePBX modules are updated to the latest release
* Make sure you made a full backup of `data` dir (IMPORTANT!)
* Make sure you disabled `FREEPBX_AUTOUPGRADE_CORE=false` in the `.env` file
* Deploy the latest version of izPBX (ex. 18.15.x for FreePBX 15, 18.16.x for FreePBX 16, etc...)
* izPBX should start with the old FreePBX release but with all dependences installed and ready to complete the upgrade
* Open FreePBX Menu: **Admin --> Modules Admin --> Check Online** select **FreePBX Upgrader --> Process**
* Follow these istructions: https://wiki.freepbx.org/display/FOP/Non+Distro+-+Upgrade+to+FreePBX+16
* From the **FreePBX / Modules Admin** page, check if all modules are been updated and enable again disabled modules

# Advanced Production Configuration Examples

### Multi-Tenant VoIP PBX with dedicated Databases

#### Objective
- Run many izPBX instances into single docker host (you must allocate an external static IP for every izPBX backend/frontend)
- Dedicated Database for every izPBX instances

#### Configuration
Create a directory where you want deploy izpbx data and create `compose.yml` and `.env` files:

Example:
```
mkdir yourgreatpbx
cd yourgreatpbx
vim compose.yml
vim .env
```

NOTE:
- Modify `compose.yml` according to your environment needs:
  - `parent:` (must be specified your ethernet card)
  - `subnet:` (must match you intranet network range)
  - `ipv4_address:` (every izPBX frontend will must to have a different external IP)
- Modify `.env` according to your environment needs:
  - `MYSQL_SERVER=db` (you can't use localhost here)

```yaml
version: '3'

networks:
  izpbx-0-ext:
    driver: macvlan
    driver_opts:
      parent: eth0
    ipam:
      config:
      - subnet: 10.1.1.0/24
        #ip_range: "10.1.1.221/30"
        #gateway: 10.1.1.1
  izpbx-1:
    driver: bridge

services:
  db:
    image: mariadb:10.5.9
    ## WARNING: if you upgrade image tag enter the container and run mysql_upgrade:
    ## source .env ; docker exec -it izpbx-db mysql_upgrade -u root -p$MYSQL_ROOT_PASSWORD
    command: --sql-mode=ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
    restart: unless-stopped
    env_file:
    - .env
    environment:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_DATABASE
    - MYSQL_USER
    - MYSQL_PASSWORD
    ## database configurations 
    volumes:
    - ./data/db:/var/lib/mysql
    networks:
      izpbx-1:

  izpbx:
    #hostname: ${APP_FQDN}
    image: ghcr.io/jmarler/izpbx-asterisk:20.16
    restart: unless-stopped
    depends_on:
    - db
    env_file:
    - .env
    volumes:
    - ./data/izpbx:/data
    cap_add:
    - SYS_ADMIN
    - NET_ADMIN
    privileged: true
    networks:
      izpbx-0-ext:
        ipv4_address: 10.1.1.221
      izpbx-1:
```

Repeat the procedure for every izPBX you want deploy. Remember to create a dedicated directory for every izpbx deploy.

#### Deploy
Enter in every directory containig configuration files and run:
- `docker compose up -d`

### Multi-Tenant VoIP PBX with shared global Database and single compose.yml file

#### Objective
- Run many izPBX instances into single docker host (you must allocate an external static IP for every izPBX backend/frontend)
- Single Global Shared Database used by all izPBX instances

#### Configuration
Create a directory where you want deploy all izpbx data and create `compose.yml` and a `PBXNAME.env` file for every izpbx deploy:

Example:
```
mkdir izpbx
cd izpbx
vim compose.yml
vim izpbx1.env
vim izpbx2.env
vim izpbx3.env
```
etc...

NOTE:
- Modify `compose.yml` according to your environment needs, changing:
  - `parent:` (must be specified your ethernet card)
  - `subnet:` (must match you intranet network range)
  - `ipv4_address:` (every izPBX frontend will must to have a different external IP)
- Remember to modify every `PBXNAME.env` file and set different variables for `MYSQL` (for best security use a different password for every deploy), example:
  - `MYSQL_SERVER=db` (all deployes will use the same db name)
  - `MYSQL_DATABASE=izpbx1_asterisk`
  - `MYSQL_DATABASE_CDR=izpbx1_asteriskcdrdb`
  - `MYSQL_USER=izpbx1_asterisk`
  - `MYSQL_PASSWORD=izpbx1_AsteriskPasswordV3ryS3cur3`
  - so on...

```yaml
version: '3'

networks:
  izpbx-0-ext:
    driver: macvlan
    driver_opts:
      parent: enp0s13f0u3u1u3
    ipam:
      config:
      - subnet: 10.1.1.0/24
  izpbx-1:
    driver: bridge

services:
  db:
    image: mariadb:10.5.9
    ## WARNING: if you upgrade image tag enter the container and run mysql_upgrade:
    ## source .env ; docker exec -it izpbx-db mysql_upgrade -u root -p$MYSQL_ROOT_PASSWORD
    command: --sql-mode=ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
    restart: unless-stopped
    env_file:
    - db.env
    environment:
    - MYSQL_ROOT_PASSWORD
    - MYSQL_DATABASE
    - MYSQL_USER
    - MYSQL_PASSWORD
    ## database configurations 
    volumes:
    - ./data/db:/var/lib/mysql
    networks:
      izpbx-1:

  izpbx1:
    #hostname: ${APP_FQDN}
    image: ghcr.io/jmarler/izpbx-asterisk:20.16
    restart: unless-stopped
    depends_on:
    - db
    env_file:
    - izpbx1.env
    volumes:
    - ./data/izpbx1:/data
    cap_add:
    - NET_ADMIN
    privileged: true
    networks:
      izpbx-0-ext:
        ipv4_address: 10.1.1.221
      izpbx-1:

  izpbx2:
    #hostname: ${APP_FQDN}
    image: ghcr.io/jmarler/izpbx-asterisk:20.16
    restart: unless-stopped
    depends_on:
    - db
    env_file:
    - izpbx2.env
    volumes:
    - ./data/izpbx2:/data
    cap_add:
    - NET_ADMIN
    privileged: true
    networks:
      izpbx-0-ext:
        ipv4_address: 10.1.1.222
      izpbx-1:

  izpbx3:
    #hostname: ${APP_FQDN}
    image: ghcr.io/jmarler/izpbx-asterisk:20.16
    restart: unless-stopped
    depends_on:
    - db
    env_file:
    - izpbx3.env
    volumes:
    - ./data/izpbx3:/data
    cap_add:
    - NET_ADMIN
    privileged: true
    networks:
      izpbx-0-ext:
        ipv4_address: 10.1.1.223
      izpbx-1:
```

#### Deploy
Enter the directory containig configuration files and run:
- `docker compose up -d`

# Services Management

### Command to restart whole izPBX deploy
`docker compose restart izpbx`

### Command to restart izPBX container only
`docker restart izpbx`

### Command to restart DB container only
`docker restart izpbx-db`

### If you want restart single services inside `izpbx` container
Enter the container:  
`docker exec -it izpbx bash`

Restart izpbx service (Asterisk Engine):  
`supervisorctl restart izpbx`

To restart others available services use `supervisorctl restart SERVICE`

Available services:  
  - `asterisk`
  - `cron`
  - `fail2ban`
  - `fop2`
  - `httpd`
  - `izpbx`
  - `tftpd`
  - `postfix`
  - `zabbix-agent`

# Tested systems and host compatibility
Tested Docker Runtime:
  - docker-ce >= 20.0
  - docker compose-plugin >= 2.0

Tested Host Operating Systems:
  - RHEL 6/7/8/9 based distro
  - Fedora Core >30
  - Debian 10
  - Ubuntu 20.04

# Environment default variables
Consult the `default.env` file: 
  - https://github.com/ugoviti/izpbx/blob/main/default.env

# Zabbix Agent Configuration
Consult official repository page for installation and configuration of Asterisk Zabbix Template in you Zabbix Server:
- https://github.com/ugoviti/zabbix-templates/tree/main/asterisk

# FreePBX Configuration Best Practices
* **Settings-->Advanced Settings**
  * CW Enabled by Default: **NO**
  * Country Indication Tones: **Italy**
  * Ringtime Default: **60 seconds**
  * Speaking Clock Time Format: **24H**
  * PHP Timezone: **Europe/Rome**
  
* **Settings-->Asterisk Logfile Settings**
  * Security Settings-->Allow Anonymous Inbound SIP Calls: **No**
  * Security Settings-->Allow SIP Guests: **No**

* **Settings-->Asterisk SIP Settings**
  * File Name: **security**
  * Security: **ON** (all others OFF)
  
* **Settings-->Filestore-->Local**
  * Path Name: **Local Storage**
  * Path: **__ASTSPOOLDIR__/backup**

* **Admin-->Backup & Restore**
  * Basic Information-->Backup Name: **Daily Backup**
  * Notifications-->Email Type: **Failure**
  * Storage-->Storage Location: **Local Storage**
  * Schedule and Maintinence-->Enabled: **Yes**
  * Schedule and Maintinence-->Scheduling: Every: **Day** Minute: **00** Hour: **00**
  * Maintinence-->Delete After Runs: **0**
  * Maintinence-->Delete After Days: **14**

* **Admin-->Contact Manager**
  * External
    * Add New Group
      * Name: **PhoneBook**
      * Type: **External**
  
* **Admin-->Caller ID Lookup Sources**
  * Source Description: **ContactManager**
  * Source type: **Contact Manager**
  * Cache Results: **No**
  * Contact Manager Group(s): **All selected**
  
* **Admin-->Sound Languages-->Setttings**
  * Global Language: **Italian**

# Configuring VoIP XML PhoneBook Lookup

- Visit **http://IZPBX_ADDRESS/pb/** to learn about the supported Phones and XML Address Books.

- Configure **Contact Manager** as reported above (create a new contact manager group name like **PhoneBook**)

## Define every PhoneBooks you want to use
- Open your VoIP Phone GUI (example for Yealink Phones):
  - **Directory-->Remote Phone Book**
    - Index 1 (URL for Extensions PhoneBook)
      - RemoteURL: **http://IZPBX_ADDRESS/pb/yealink/ext**
      - Display Name: **Extensions**
    - Index 2 (URL for Corporate PhoneBook)
      - RemoteURL: **http://IZPBX_ADDRESS/pb/yealink/PhoneBook**
      - Display Name: **Corporate**

For Yealink / Fanvil phones you can use a single Phonebook Menu: **http://IZPBX_ADDRESS/pb/yealink/menu**

# FAQ / Troubleshooting
- FOP2 specific version upgrade/downgrade (get latest release number from https://www.fop2.com/download.php):
  automatic upgrade:
  ```
  FOP2_VER=2.31.37
  curl -fSL --connect-timeout 30 http://download2.fop2.com/fop2-$FOP2_VER-centos-x86_64.tgz | tar xz -C /usr/src
  cd /usr/src/fop2
  make install
  supervisorctl restart fop2
  ```

  for manual downgrade just copy the fop2_server binary:
  ```
  cp /usr/src/fop2/server/fop2_server /usr/local/fop2/fop2_server
  supervisorctl restart fop2
  ```

- FOP2 useful commands:
    NB. define interface name to associate the license, for example: `eth0`
    - enter into izpbx container: `docker exec -it izpbx bash`
    - register the license: `/usr/local/fop2/fop2_server --rp=http --register --iface eth0 --name "Company Name" --code "LICENSECODE"`
    - get lincese detail: `/usr/local/fop2/fop2_server --rp=http --getinfo --iface eth0`
    - reactivate the license: `/usr/local/fop2/fop2_server --rp=http --reactivate --iface eth0`
    - revoke the license: `/usr/local/fop2/fop2_server --rp=http --revoke --iface eth0`

- FOP2 is running in Demo mode because the license is invalid
  - FOP2 soffer from a bug about the lincensing model (already comunicated many times to the FOP2 support without an official solution)
    to workaround that problem, normally is required to **reactivate** the license, but some times doesn't works, so the only solution is to contact FOP2 support team

- FreePBX is slow to reload (https://issues.freepbx.org/browse/FREEPBX-20559)
  - As temporary WORKAROUND enter into izpbx container shell and run:  
    `docker exec -it izpbx bash`  
    `fwconsole setting SIGNATURECHECK 0`

- Factory Reset izPBX (WARNING! your persistent storage will be wiped!):
  - `docker compose down`
  - `rm -rf data`
  - `docker compose up -d`
    
# TODO / Future Development
- Add ARM version compatible with Raspberry PI 
- Kubernetes deploy via Helm Chart (major problems for RTP UDP ports range... needs further investigation, no valid solutions right now)
- Hylafax+ Server + IAXModem (used for sending FAXes. Receiving FAXes via mail is already possibile using FreePBX FAX Module)
- macOS host support? (edit `compose.yml` and comment localtime volume?)
- Windows host support (need to use docker volume instead local directory path?)

# BUGS and Limits of this project
- izPBX was developed with container antipattern design in mind.
  - FreePBX was not designed to run as containerized app, and its ecosystem requires numerous modules to works
  - FreePBX modules updates will managed by FreePBX Admin Modules Pages itself not by izPBX container updates
- izPBX must be deployed by 1 instance for every VM or Baremetal host
  - No multi deploy works out of the box by default when using `network_mode: host`
  - Look **Advanced Production Configuration Examples** section for Multi-Tenant Solutions
- By default `network_mode: host` is used, so the PBX network is esposed directly in the host interface (no internal container network is used), so the default UDP RTP port range can be set from `10000` to `20000`.
  - If you plan to disable `network_mode: host`, tune the port range (forwarding 10000 ports with the docker stack make high cpu usage and longer startup times), for example for 50 concurrent calls:  
`APP_PORT_RTP_START=10000`  
`APP_PORT_RTP_END=10200`  
for best security, fine-tune the ports range based on your needs by not using standard port ranges!  
- Unpredictable network interface order when running in Multi-Tenant mode. As workaround, used network interface name must be named in lexical order. refs:
  - https://gist.github.com/jfellus/cfee9efc1e8e1baf9d15314f16a46eca
  - https://github.com/moby/moby/issues/20179
- By default FreePBX use Signature Checking for modules packages, but this make very high delays when reloading FreePBX, so by default is been disabled. refs:
  - https://issues.freepbx.org/browse/FREEPBX-20559
- No commercial FreePBX modules can be installed
  - sysadmin rpm module is missing. Looking for a solution
- Missing good EndPoint Manager for automatic phone provisioning
  - Looking fo a valid solution
  
# Quick reference
- **Developed and maintained by**:
  [Ugo Viti](https://github.com/ugoviti/izpbx) @ InitZero S.r.l.

- **Where to file issues**:
  [https://github.com/ugoviti/izpbx/issues](https://github.com/ugoviti/izpbx/issues)

- **Where to get commercial help**:
  email: [support@initzero.it](mailto:support@initzero.it) - web: [InitZero Support](https://www.initzero.it/)
  
- **Supported architectures**:
  [`amd64`]

- **Supported Docker versions**:
  [the latest release](https://github.com/docker/docker-ce/releases/latest) (down to 1.6 on a best-effort basis)

- **License**:
  [GPL v3](https://github.com/ugoviti/izpbx/blob/main/LICENSE)
