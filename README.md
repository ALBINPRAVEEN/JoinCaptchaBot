# JoinCaptchaBot

<p align="center">
    <img width="100%" height="100%" src="https://gist.githubusercontent.com/J-Rios/05d7a8fc04166fa19f31a9608033d10b/raw/32dee32a530c0a0994736fe2d02a1747478bd0e3/captchas.png">
</p>

Bot to verify if a new user, who joins a group, is a human.
The Bot sends an image captcha for each new user, and kicks any of them that can't solve the captcha in a specified amount of time. Also, any message that contains an URL sent by a new "user" before captcha completion will be considered Spam and will be deleted.


## Installation

Note: Use Python 3.6 or above to install and run the Bot, previous version are unsupported.


1. Install Pillow prerequisites:

    ```bash
    sudo apt install -y libtiff5-dev libjpeg62-turbo-dev zlib1g-dev libfreetype6-dev liblcms2-dev libwebp-dev tcl8.6-dev tk8.6-dev python-tk
    ```

2. Get the project and install JoinCaptchaBot requirements:

    ```bash
    git clone https://github.com/ALBINPRAVEEN/JoinCaptchaBot
    pip install -r JoinCaptchaBot/requirements.txt
    ```

3. Go to project sources and give execution permission to usage scripts:

    ```bash
    cd JoinCaptchaBot/sources
    chmod +x run status kill
    ```

4. Specify Telegram Bot account Token (get it from @BotFather) in "settings.py" file:

    ```python
    'TOKEN' : 'XXXXXXXXX:XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX'
    ```

## Usage

To ease usage a `run`, `status`, and `kill` scripts have been provided.

- Launch the Bot:  
`./run`

- Check if the script is running:  
`./status`

- Stop the Bot:  
`./kill`

## Systemd service

For systemd based systems, you can setup the Bot as daemon service.

To do that, you need to create a new service description file for the Bot as follow:

```bash
[vim or nano] /etc/systemd/system/bot.service
```

File content:

```bash
[Unit]
Description=Bot Telegram Daemon
Wants=network-online.target
After=network-online.target

[Service]
Type=forking
WorkingDirectory=/path/to/dir/sources/
ExecStart=/path/to/dir/sources/run
ExecReload=/path/to/dir/sources/kill

[Install]
WantedBy=multi-user.target
```

Then, to add the new service into systemd, you should enable it:

```bash
systemctl enable --now bot.service
```

Remember that, if you wan't to disable it, you should execute:

```bash
systemctl disable bot.service
```

## Docker

You can also run the bot on [Docker](https://docs.docker.com/get-started/). This allows easy
server migration and automates the download of all dependencies. Look at the
[docker specific documentation](docker/README.md) for more details about how to create a Docker Container for Captcha Bot.

## Bot Owner

The **Bot Owner** can run special commands that no one else can use, like /allowgroup (if the Bot is private, this allow groups where the Bot can be used) or /allowuserlist (to make Bot don't ask for captcha to some users, useful for blind users).

You can setup a Bot Owner by specifying the Telegram User ID or Alias in "settings.py" file:

```python
"BOT_OWNER": "@i_am_albin_praveen",
```

## Make Bot Private

By default, the Bot is **Public**, so any Telegram user can add and use the Bot in any group, but you can set it to be **Private** so the Bot just can be used in allowed groups (Bot owner allows them with **/allow_group** command).

You can set Bot to be Private in "settings.py" file:

```python
"BOT_PRIVATE" : True,
```

**Note:** If you have a Public Bot and set it to Private, it will leave any group where is not allowed to be used when a new user joins.

**Note:** Telegram Private Groups could changes their chat ID when it become a public supergroup, so the Bot will leave the group and the owner has to set the new group chat ID with /allow_group.

## Scalability (Polling or Webhook)

By default, Bot checks and receives updates from Telegram Servers by **Polling** (requests and get if there is any new updates in the Bot account corresponding to that Bot Token), this is really simple and can be used for low to median scale Bots. However, you can configure the Bot to use a **Webhook** instead if you expect to handle a large number of users/groups.

To use Webhook instead Polling, you need a signed certificate file in the system, you can create the key file and self-sign the cert through openssl tool:

```bash
openssl req -newkey rsa:2048 -sha256 -nodes -keyout private.key -x509 -days 3650 -out cert.pem
```

Once you have the key and cert files, setup the next lines in "settings.py" file to point to expected Webhook Host address, port and certificate file:

```python
"WEBHOOK_HOST": "Current system IP/DNS here",
"WEBHOOK_PORT": 8443,
"WEBHOOK_CERT" : SCRIPT_PATH + "/cert.pem",
"WEBHOOK_CERT_PRIV_KEY" : SCRIPT_PATH + "/private.key",
```

To use Polling instead Webhook, just set host value back to none:

```python
"WEBHOOK_HOST": "None",
```
