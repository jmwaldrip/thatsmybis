# That's My BIS; a tool for managing loot council guilds in World of Warcraft Classic
This project serves as a place to host resources and crap for the guild. Links to stuff. Links to polls. Calendars, etc. It aims to delegate all of the user management to the guild's Discord server.

This project shouldn't become a place for active communication (ie. chatting); instead, the guild Discord should maintian that role.

Part of the goal of this project is to make it so our members won't miss important information posted to the Discord server. Ideally, this will be done in the form of a bot that grabs important announcements from Discord and saves them onto the site. (that bot doesn't exist as of this writing - Nov 16, 2019)

PHP was chosen as the language for this project. Why? Simply because the person who took the initiative of making this project was most familiar with PHP.

Laravel was chosen as the framework for the project. Why? It's a very popular PHP framework with a lot of nice tools, and the guy who made this repo was familiar with it.

(Nov 16, 2019) A CMS for Laravel hasn't been chosen yet, but one will be chosen and added in.

## FAQ

### Q: How can I contribute?
Clone the repo, create a new branch (don't use `master`), push it to github, open a new pull request, and let me (Lemmings19) know that you'd like me to review your contribution. If it's all good, we'll merge it into master.

### Q: Where are the docs for the framework being used?
This project uses [Laravel](https://laravel.com/). It's pretty powerful and has some nice docs. You can read through them [here](https://laravel.com/docs/6.x).

### Q: Where are the pages?
`/resources/views`. They're PHP templates made with the [Blade](https://laravel.com/docs/6.x/blade) templating engine, so you'll see some non-html stuff mixed in there.

### Q: Where's the Javascript?
`/public/js`. Right now, (2019-11-21) there's nothing setup to minify, transpile, or cache bust it. Just a barebones implementation.

For some pages, it's at the bottom of the template. This **does not** follow best practices, but was instead done in the interest of saving time and pushing out a quick MVP.

### Q: Where's the CSS?
`/public/css`. Not using SASS, minifying, or any of that jazz just yet. (2019-11-21)

### Q: How do I add a page?
Start by adding it into `/routes/web.php` ([docs here](https://laravel.com/docs/6.x/routing)), then handle it in the appropriate controller in `/app/Http/Controllers`. It's possible to skip using a controller and do it inline in the `web.php` file, but that can get messy.

### Q: How do I access the database?
There isn't a GUI yet (such as phpMyAdmin), so you'll need to use the terminal or set up a GUI yourself.

### Q: I have more questions.
Reach out to Lemmings19 here on GitHub or on Discord if you can find me. :)

## Local Environment Setup
The easiest way to get a local envrionment setup is with Laravel's configuration for Vagrant. Vagrant is a tool that makes it relatively painless to spin up a virtual machine containing a standardized dev environment for a specific project. This means that rather than configuring your operating system to have the appropriate packages, webserver, databases, and other requirements for this project, you just download and boot up a virtual machine that already has all that crap set up. This allows many developers to run the same dev environment, reducing troubleshooting and headaches, and putting more focus on the project itself.

Now, there's still going to be a little bit of work and learning curve involved in getting that VM setup. But believe me, it's better than the alternative.

Laravel has a custom environment for Vagrant. They call their environment 'Homestead', and that's what we're going to be setting up. It's easiest to get this up and running on Linux, but it's not much more work to get it running on Windows.

Best place to start? [RTFM](https://www.urbandictionary.com/define.php?term=RTFM): https://laravel.com/docs/6.x/homestead

tl;dr

- Download and install VirtualBox: https://www.virtualbox.org/wiki/Downloads
- Download and install Vagrant: https://www.vagrantup.com/downloads.html
- Download/clone the laravel/homestead repo, run `bash init.sh` (or `init.bat` in Windows), configure `Homestead.yaml`
- Generate .ssh keys or use the ones you have. (In Windows: `ssh-keygen -t rsa -C "your_email@example.com"`)
- A Windows configuration for `Homestead.yaml` looks like this:
    ```
    ---
    ip: "192.168.10.10"
    memory: 2048
    cpus: 2
    provider: virtualbox

    authorize: C:\Users\Joe\.ssh\id_rsa.pub

    keys:
        - C:\Users\Joe\.ssh\id_rsa

    folders:
        - map: C:\projects\thatsmybis
          to: /home/vagrant/code/thatsmybis

    sites:
        - map: thatsmybis.local
          to: /home/vagrant/code/thatsmybis/public

    databases:
        - thatsmybis

    features:
        - mariadb: false
        - ohmyzsh: false
        - webdriver: false

    # ports:
    #     - send: 50000
    #       to: 5000
    #     - send: 7777
    #       to: 777
    #       protocol: udp
    ```
- Make sure the hosts have been setup (read Hostname Resolution in the [docs](https://laravel.com/docs/6.x/homestead#configuring-homestead))
- Run `vagrant up` while in the Homestead directory.
- Run `vagrant ssh`
- Run `composer install` from `/home/vagrant/code/thatsmybis`
- Create a file named `.env` in `/home/vagrant/code/thatsmybis`. Base it off of `.env.example` or ask another dev for what details to fill in.

## Server Environment Setup

I used Ubuntu 18.04. Here's some notes for stuff I had to install and configure:

```
sudo apt update
sudo apt install
sudo apt install apache2
sudo apt install software-properties-common
sudo add-apt-repository ppa:ondrej/php
sudo apt update
sudo apt install php7.3-fpm
sudo apt install php7.3
sudo apt install mysql-server
sudo mysql_secure_installation
sudo service apache2 restart
sudo apt-get install php7.3-mbstring
sudo apt-get install php7.3-dom
sudo apt-get install php7.3-zip
sudo apt-get install php7.3-curl
sudo apt-get install php7.3-mysql
sudo a2enmod rewrite
sudo nano /etc/apache2/apache2.conf
    > modified `<Directory /var/www/>` to have `AllowOverride All` instead of `AllowOverride None`
sudo service apache2 restart
cd /var/www/html
sudo mkdir thatsmybis
sudo chown ubuntu: thatsmybis
git clone https://github.com/Lemmings19/thatsmybis.git thatsmybis
cd thatsmybis
git config --global credential.helper store
git pull
chmod -R 777 storage
    > this is probably bad
touch .env
nano .env
    > copy in environment settings
composer install
sudo mysql -u root -p
    > CREATE USER 'laravel'@'localhost' IDENTIFIED BY 'enter_a_password_here';
    > GRANT ALL PRIVILEGES ON *.* TO 'laravel'@'localhost';
    > UPDATE user SET plugin='mysql_native_password' WHERE User='laravel';
    > FLUSH PRIVILEGES;
    > exit;
sudo service mysql restart
```

# Permissions

kodeine\acl library was used for the permissions.

**NOTEWORTHY:** That library expected the User model to be the one with the roles and permissions. We did not use the User model. We used the Member model. Because of that, some references to 'user' or 'user_id' might not make any sense whatsoever. Have fun with that. I sure did. /s

**ALSO:** There's an override. The CheckGuildPermissions middleware will flag someone as a SUPER ADMIN if they're the guild's owner in the DB or (if I implement it), if they're one of the devs. the `hasPermissions()` function in the Member model will allow this flag to override the normal permissions. Science!

# Roles

Roles are loaded from the Discord server.

## Laraval Custom Configurations

- `ONLY_FULL_GROUP_BY` for SQL has been disabled in `database.php` by changing `strict` to `false`. This is to allow for writing simpler `GROUP BY` clauses in queries. If you can fix the `group by` complications caused by `strict`, you're welcome to turn it back on. I tried. It required mutilating my `SELECT` statements, and even then I couldn't get it to 100% work the way it did before 5.7 when it just assumed `ANY_VALUE()` on non-aggregated columns (even when I told it to use `ANY_VALUE()`). Good luck. ([SO thread](https://stackoverflow.com/questions/34115174/error-related-to-only-full-group-by-when-executing-a-query-in-mysql))
