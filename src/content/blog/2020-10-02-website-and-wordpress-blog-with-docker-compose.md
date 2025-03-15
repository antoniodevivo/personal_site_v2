---
title: 'Setting up a profile website and a WordPress blog with Docker compose'
description: 'Beginner level'
icon: "mountains"
pubDate: 'Oct 02 2020'
heroImage: "/src/assets/blog-2.png"
---

If you are creating your own personal website, take a look to this post 😉<br>

We’ll see together how to create a **secure website** and serve a **wordpress** blog on the same domain.<br><br>

---
import CodeFromGitHub from '../components/CodeFromGitHub.astro';
---

# Esempio di Codice

Ecco il codice dal mio repository:

<CodeFromGitHub url="https://raw.githubusercontent.com/tuoUsername/tuoRepository/main/percorso/file.py" />

## 1. The concept
Let’s take a look to the concept behind this method.

![project-flow](/src/assets/flow-blog-2.png)

We’ll use a Nginx server for redirect all api calls where we want.<br><br>


## 2. The docker-compose.yaml

Let’s begin seeing what our **YAML** file for docker compose:

{% gist f3e15a60a4942f5e96aed6e55440e552 docker-compose.yaml %}

To reduce the navigation complexity of the post, I divided everything into three sections, one for each post.<br>

In each of these you will find the piece of the yaml file inherent and the explanation of the various configuration files<br><br>

## 3. Reverse proxy container

### YAML part
{% gist 48de21b0b7762412301368c95ea680ef reverse-proxy.yaml %}

For the **reverse proxy** container we’re using a nginx v1.7 server, linking 80 and 443 ports and settings volumes for configuration and **ssl certificates**.

### nginx.conf

Let’s take a look to the basic nginx configuration for reverse proxy:

{% gist e9966e9dc651608b00d9b27e776bdc39 nginx.conf %}

How are mapped:

- the server catch any api call direct to **https** port on domain example.com and www.example.com
- the **location** **/** (root) is set to **re-route** http calls to http://profile_site:443
- the **location** **/blog** is set to **re-route** http calls to http://blog:443

The **sublocations** for **css**, **js** and **php** are for redirecting calls **to wordpress static files** in the right direction

**Note:** http://profile_site and http://blog are defined in docker network

It’s also present the part for redirect http request to https.<br><br>

## 4. Personal site container

### YAML part

{% gist a02d7c57c3acdc67645c0af0679a3cf2 personal_site.yaml %}

The /var/www/http/profile_site will be the root folder for our website<br>

### nginx.conf

{% gist 1e393dbc8c139466def43ff340cfe677 nginx.conf %}
<br><br>

## 5. MySQL Container

### YAML part

{% gist c84b10bd2eeb62b84945953b86e9efe0 db.yaml %}

Here we should consider a couple things:

- The volume **./blog/db_data **should contain all data of our database to avoid to loosing them
- The command **–default-authentication-plugin=mysql_native_password** **–innodb-flush-method=O_DSYNC** allow us to use password as authentication method<br><br>

## 6. WordPress container

### YAML part

{% gist 75eb3d2ddc747fad469f69f9a3550f4e blog.yaml %}

This part is also very intuitive. The files for Apache inserted in the volumes we will see them shortly.
I would like to make just a couple of clarifications:

- The **– db : mysql** under **links** key it’s for assign an alias for db in the docker network. It might be useful to find the container of the database more easily
- The volume **./blog/wordpress_data** will contain all wordpress site automatically, to avoid to loose data

### 000-default.conf

{% gist 2f54aae51ee0138c76605c4907528eb6 000-default.conf %}

A really simple file, to set the server inside the wordpress container so that it exposes only port 443 to call up our blog<br>

### apache2.conf

{% gist f711ffdcb74947bc743823aa30d4cdec apache2.conf %}

Just add these 3 lines to have your **HTTP** calls re-routed to **HTTPS**

**Note**: the Apache rewrite module is enabled by default in the wordpress docker image. If not, follow the steps below:

- Execute **docker exec -it blog_container_name bash** from the host to enter in the container
- Run **a2enmod rewrite** for enable rewrite module
- Run **systemctl restart httpd** for reboot apache service
<br>

### ports.conf

{% gist 2143554274df59c38de86fca323be641 ports.conf %}

Just comment all lines or delete them for unset other access points to apache server

<br><br>

That’s all!

You’ll find all the project files with some security features at this link: [https://github.com/antoniodevivo/personal_site](https://github.com/antoniodevivo/personal_site)

I have tried to make this article as **streamlined** as possible, so that you can have a **quick** dynamic on how to **set up** your deployment environment.

I’m sure you will be able to find the answers to any doubts on Google 😉

If not, write to me in private.


Thanks for reading the article 😀

<br>