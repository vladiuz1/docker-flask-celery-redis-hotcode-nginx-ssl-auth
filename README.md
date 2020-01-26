# Docker Nginx SSL Cert Auth +  Flask + Celery + Redis

A basic [Docker Compose](https://docs.docker.com/compose/) template for orchestrating a [Flask](http://flask.pocoo.org/) application & a [Celery](http://www.celeryproject.org/) queue with [Redis](https://redis.io/) behind an nginx reverse proxy with SSL client certificate authentication.

### Installation

Download the sources:

```bash
git clone https://github.com/mattkohl/docker-flask-celery-redis
```

### Developement environment

In the developement environment you do not need to do much, as all your code is exposed via ports 5001 (api) and 5555 (flower), and running
on the localhost.

All you need to do is run
```bash
docker-compose -f docker-compose.yml.dev up -d --build
```

It will build all the necessary services for you and start them up on localhost. You can then edit `api/app.py`  
and `celery-queue/tasks.py` as you save them the services will be reloaded in real time.


### Production environment

Copy `example.env` into `.env`

```bash
cp example.env .env
```

And then edit the file to enter your hostname. Your api will be accessible from `api.<NGINX_HOSTNAME>` and 
flower will be running on `flower.<NGINX_HOSTNAME>`

Create Let's Encrypt SSL certificates for domains `api.<your.domain>` and `flower.<your.domain>`

```bash
sudo ./init-letsencrypt.sh.
```

(Based on [this post](https://medium.com/@pentacent/nginx-and-lets-encrypt-with-docker-in-less-than-5-minutes-b4b8a60d3a71).)

### Build & Launch

```bash
docker-compose up -d --build
```

This will expose the Flask application's endpoints on port `5001` as well as a [Flower](https://github.com/mher/flower) server for monitoring workers on port `5555`

To add more workers:
```bash
docker-compose up -d --scale worker=5 --no-recreate
```

To shut down:

```bash
docker-compose down
```

To change the endpoints, update the code in [api/app.py](api/app.py)

Task changes should happen in [queue/tasks.py](celery-queue/tasks.py)

---

adapted from [https://github.com/itsrifat/flask-celery-docker-scale](https://github.com/itsrifat/flask-celery-docker-scale)
