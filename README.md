Magedock
====================
Magedock helps you run your magento on Docker real quick. 
It's like laradock but for Magento instead of Laravel.

Help to work with 2.3.x -> https://digitalstartup.co.uk/t/how-to-install-magento-2-3-and-build-a-web-server/263#chapter3

## Installation

Let's see how easy it is to install `NGINX`, `PHP`, `Composer`, `MySQL` and `Redis`. Then run `Magento`.

1. Get Magedock inside your Magento project: 

```bash
git submodule add https://github.com/ojhaujjwal/magedock.git
```
2. Copy local.env.sample to local.env and modify it to your needs.
3. Enter the magedock folder and run only these Containers: 

`docker-compose up -d nginx mysql redis`

4. `docker-compose exec workspace magento-installer`
5. Open your browser and visit the localhost: `http://localhost`
