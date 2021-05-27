# Zammad/Docker-Compose/Let's Encrypt guide

Install your Zammad instance in any Linux server with 'https' protocol certificate provided by Let's Encrypt.

⚠️ This is an alternative and not the official way. Find more information [here](https://docs.zammad.org/en/latest/install/docker-compose.html).

# Installation

1. [Follow these instructions to install the SSL Let's Encrypt Certificate](https://haydenjames.io/how-to-set-up-an-nginx-certbot/)

2. Git clone this repository to your server

```
git clone https://github.com/danperrout/zammad-docker-letsencrypt.git
```

```
cd zammad-docker-letsencrypt
```

3. Replace the 'example.com' domain on the `default` file to your own domain

4. Copy the `default` file to the nginx/sites-available folder

```
sudo cp default /etc/nginx/sites-available/default
```

5. Create a symbolic link on the the docker volume so that the nginx can access it

```
sudo ln -s /var/lib/docker/volumes/zammad-docker-letsencrypt_zammad-data/_data/ /opt/zammad
```

6. Rename `sample.env` to `.env` and change the 'POSTGRES_PASS' value

7. Run the Docker-compose command:

```
sudo docker-compose -f docker-compose.yml -f docker-compose.override.yml up -d
```

8. ...and wait a few minutes (~5) until the initial setup is finished and you're done! Access https://yourdomain.com and enjoy!

# Configuration (Optional)

Use an admin account to make the following configurations on your instance.

## E-mail account without IMAP nor POP3 access

Run this command to add an e-mail account with SMTP access only (notification only):

- SMTP_HOST
- SMTP_USER
- SMTP_PASS
- SMTP_PORT

```
sudo docker exec -it zammad-docker-letsencrypt_zammad-railsserver_1 /bin/bash
```

Edit the variables on the 'rails r' command:

```
rails r "Channel.create(area: 'Email::Account', options: { inbound: { adapter: 'null', options: {} }, outbound: { adapter: 'smtp', options: { host: 'SMTP_HOST', port: SMTP_PORT, user: 'SMTP_USER', password: 'SMTP_PASS' } } }, active: true, preferences: { editable: false }, updated_by_id: 1, created_by_id: 1)"
```

```
exit
```
