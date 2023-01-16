# Exercise 1

## Description

This project uses *Docker Compose* to build/deploy a basic php application in a local environment. An NGiNX proxy container receives the http requests and proxies them to the php-fpm container where the application lives. The application is monitored by a New Relic agent located inside the application container and sends the metrics to a New Relic daemon container.

### Project Tree

```
.
├── app
│   ├── Dockerfile
│   ├── .dockerignore
│   ├── entrypoint.sh
│   └── index.php
├── config
│   ├── newrelic
│   │   └── newrelic.ini
│   ├── nginx
│   │   └── nginx.conf
│   └── php-fpm
│       └── www.conf
├── docker-compose.yml
├── .env
└── README.md
```

### Dependencies

-  **OS**: Linux (developed and tested on Ubuntu 20.04)
-  **Docker** (developed and tested with 20.10.1)
-  **Docker Compose** (developed and tested with v2.3.3)
- Your unix user must belong in the docker group (run `groups $USER` to verify)

### Installation
Clone the repository

### Configuration
The configuration files for the NGiNX and the PHP-FPM are located in the `config` directory.

- **PHP-FPM default version**: 7.3.27-fpm-buster
- **NGIiX default version**: 1.20
- **NewRelic daemon default version**: 10.4.0

Change the versions by editing the `.env` file in the root directory of the project. 

> **Note:** The default configuration works

### Run

1. Go to the root directory of the project
2. `docker compose build` to build the application image
3. `docker compose up -d` to deploy the stack
4. From a browser go to http://10.10.0.10 (the address can be changed by editing the `.env` file)
5. `docker compose down` to delete the deployed stack or `docker compose down --rmi all` 
    to remove all the corresponding images as well

> **Note:** When the configuration for NGiNX, PHP-FPM or the NewRelic is changed you need to restart the stack by running `docker compose restart`


# Exercise 3

**Assumptions**: The way I noticed that a private key was committed to a repository was during the review of
the Pull Request created by the developer. Also, there are at least 2 other reviewers and one of them is the tech lead
or a senior software engineer. 

3.1
My comment would be a comment to the Pull Request and not a direct message to the developer.
This way there will be transparency and everyone related can see it and comment as well.

**My comment**:
We shouldn't allow any kind of secrets to be placed in the application code. Secrets should be provided to the application
during the configuration and should be different for each environment. This makes our code flexible and easy to maintain
when secrets change as its agnostic to the actual secret. Last but not least, storing plaintext secrets to repositories it's
a big security risk as anyone with access to the repository can see it and it potentially can be exploited by outsiders.

`@senior_software_engineer` If this not a common way for us to store our secrets I suggest we to cancel this pull request and 
we make sure that there is no commit in the git history with the secret.
  If this is something we do on a constant basis and there are other secrets too, then I think the Pull Request could be merged as it
doesn't add more complexity or security risk than we already have. But in this case I find it extremely essential to change this way
of doing things.

3.2
**Assumption**: There was a reply from the senior software engineer telling that we store our secrets in repositories. I replied 
that I will contact them in an internal slack channel where the whole engineering team has access to.

**Message**:
I think that we need to discuss how we can avoid storing secrets in repositories. This obviously is something that we need
to resolve as it will make us more flexible and more secure. Storing secrets in repositories isn't our invention, it's a bad practice
that a lot of teams (big companies as well) do for saving time or money. From my point of view and my experience this is a debt that someday
deterministically will be paid. Thankfully there are practices and awesome tools that can help us to change this. We can utilize a Secret 
Management tool that will allow us to securely store our secrets(passwords, keys, tokens etc) in an accessibility-controlled way. So we need to
make a double research, on what tool or managed service we should use and what are the changes we need to do when it comes to our 
application code. My initial suggestions for the tool would be `HashiCorp Vault` as it's open source, has a big community behind it, it's well
tested and supported and it is considered an industry standard. `AWS Secrets Manager` sounds a nice choice too as it's a managed service. 
Both have pros and cons of course but the most crucial is that we will have a vault for our secrets. So, this is my perspective on the matter,
I'd love your feedback related on everting I mentioned and of course your perspective on the matter.