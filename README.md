# Uptime Kuma with Caddy proxy, Ntfy and email notifications

This `docker-compose.yml` project makes it really simple to deploy a self hosted uptime monitoring system with [Uptime Kuma](https://github.com/louislam/uptime-kuma) and [Caddy](https://caddyserver.com/) as a reverse proxy with TLS support.

It also includes [Ntfy](https://github.com/binwiederhier/ntfy) for push notifications to your phone, and email notifications.

## Prerequisites

- A linux server with a public IP address. You can get one for free from [Oracle Cloud](https://www.oracle.com/cloud/free/) or [Google Cloud](https://cloud.google.com/free).

- [Docker and Compose](https://docs.docker.com/engine/install/ubuntu/) installed and running on your system.

- A domain name. If you host your DNS with Cloudflare, you can use their API to automatically deploy and renew TLS certificates with Caddy.

- An account in any mail service provider that supports SMTP.

## Installation

1. Clone this repository:

    ```bash
    git clone git@github.com:jschwindt/uptime-kuma-caddy-ntfy-smtp.git
    cd uptime-kuma-caddy-ntfy-smtp
    ```
2. Copy the `.env.example` file to `.env` and fill in the required values:

    ```bash
    cp .env.example .env
    ```
    The `.env` file needs updates to the following variables:

    | Variable | Description |
    | --- | --- |
    | `UPTIME_KUMA_DOMAIN` | Your domain name for the `uptime` service. |
    | `NTFY_DOMAIN` | Your domain name for the `ntfy` service. |
    | `NTFY_BASE_URL` | Same as `NTFY_DOMAIN` but as a https url. |
    | `CLOUDFLARE_EMAIL` | Your Cloudflare account email. |
    | `CLOUDFLARE_API_TOKEN` | The token for Caddy to handle DNS changes in Cloudflare. Get it [here](https://dash.cloudflare.com/profile/api-tokens) with `DNS:Edit` permissions. |
    | `RELAY_xxxx` | The config for your SMTP service provider account. The example given is for the Sendgrid service. |

3. Start the containers:

    ```bash
    docker-compose up -d
    ```
    The first time you run this command, it will take a while to download the images and build the containers.

    Check the logs to see if everything is working:

    ```bash
    docker-compose logs -f
    ```
4. Create a new admin user for `ntfy`. Follow the instruction in the help message or see [here](https://docs.ntfy.sh/config/#users-and-roles):

    ```bash
    docker compose exec -it ntfy ntfy user add --role=admin --help
    ```
    With those credentials, you can log in to the `ntfy` web interface at `https://ntfy.yourdomain.com/` and create a new notification channel and an API key to use later in the `uptime` configuration.

5. Finally go to the `uptime` web interface at `https://uptime.yourdomain.com/`, create a new user and start adding monitors. Use the following settings for the notification channels:

    | Notification Type | Settings |
    | --- | --- |
    | Email (SMTP) | Hostname: `smtp`, Port: `25`, Security: `None / STARTTLS (25, 587)` |
    | Ntfy | Server URL: `https://ntfy.example.com` (the same in .env), Authentication Method: `Username and Password` or `Access Token`|
