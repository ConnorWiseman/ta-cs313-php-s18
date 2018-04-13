# CS313 - Heroku PHP Application Setup

See also the instuctions in I-Learn and Heroku's official example repository at  [heroku/php-getting-started](https://github.com/heroku/php-getting-started).


## Before beginning

In the following instructions, I use the angle brackets `<` and `>` to denote placeholder text. Do not attempt to run any of the commands below without replacing the angle brackets, and anything they contain, with an actual value.


## Install a text editor

Anything with syntax highlighting for HTML, PHP, and JavaScript will be adequate for the course. In no particular order, here are four options to consider. If you have a preferred editor not listed, use that instead.

* [Notepad++](https://notepad-plus-plus.org/)
* [Atom](https://atom.io)
* [Sublime Text](https://www.sublimetext.com/)
* [Visual Studio Code](https://code.visualstudio.com/)


## Install git

* [git](https://git-scm.com/downloads)

You will use git to push code changes to remote repositories at GitHub and at Heroku. git must be installed.


## Install the Heroku CLI

* [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli#download-and-install)

You will use the Heroku CLI to create and manage Heroku applications. The Heroku CLI must be installed.


## Install a local development stack

The course recommends either Bitnami's WAPP (Windows, Apache, PHP, and PostgreSQL) or MAPP (macOS, Apache, PHP, and PostgreSQL) stack for ease of development. If you use either of these, pay attention to the directory where they were installed. It will become useful shortly.

* [WAPP Stack](https://bitnami.com/stack/wapp)
* [MAPP Stack](https://bitnami.com/stack/mapp)

Advanced users who prefer more flexibility may install or download the individual component parts of a local development stack. However, setting up your own development environment is not discussed in the course. Proceed at your own risk.

* [PHP](http://us1.php.net/downloads.php)
  * Thread-safe for Apache
  * Non-thread-safe for mostly everything else
* [PostgreSQL](https://www.postgresql.org/download/)
* A web server
  * [Apache](https://httpd.apache.org/download.cgi)
  * [nginx](http://nginx.org/en/download.html)


## Install Composer

* [https://getcomposer.org/download/](https://getcomposer.org/download/)

When working with PHP applications, Composer is a required dependency of the Heroku CLI even if you choose not to use Composer to manage your PHP project's dependencies. During installation of Composer, you may be prompted for the path to a PHP executable. Browse to the location of the PHP executable that your local development stack is using if necessary.


## Ensure your system PATH is set correctly

Run the following from the command line.

```shell
git --version
heroku --version
composer --version
psql --version
```

For your future sanity, all of these **must** report version information to guarantee they have been installed correctly. See the subsection below for more information if any of them report an error instead.

### Add missing directories to your system PATH

Heroku and Composer should have added themselves to your system PATH. If they did not, uninstall and reinstall them.

If `psql` is missing, you must add `<Bitnami install directory>\PostgreSQL\bin` to your system PATH.

Adding a directory to the system PATH is done a little differently on different operating systems. See the following StackOverflow questions and answers for more information.
* [How to add a folder to PATH environment variable in Windows 10](https://stackoverflow.com/questions/44272416/)
* [Setting PATH environment variable in OSX permanently](https://stackoverflow.com/questions/22465332)
* [How to permanently set $PATH on Linux/Unix?](https://stackoverflow.com/questions/14637979/)


## Create a new GitHub repository

* [New repository](https://github.com/new)

Your GitHub repository for this course's probject **must** be public. Do not worry about including a `.gitignore` file, a `README.md` file, or a license.


## Clone your GitHub repository to your local machine

Once your repository has been created, run the following from the command line to download your GitHub repository to your local machine. Place your local repository somewhere accessible because you will be returning here again and again for the first half of the semester.

```shell
git clone <your GitHub repository's *.git url>
```


## Set up your application files

Every Heroku PHP application needs three things.

* An application configuration file called `Procfile`
* A JSON file describing PHP dependencies called `composer.json`
* A subdirectory containing files served to the public, called `/public` in this repository

In addition, your public-facing subdirectory should probably contain an `index.php` file to help verify that PHP was installed and working correctly.

Add these files to your local repository. See the sample files in this repository for examples on what they should contain.

Note that in Linux, file paths are case-sensitive. Heroku runs all its applications through Linux containers. `Procfile` and `composer.json` **must** therefore be capitalized exactly as they appear above.


## Push initial changes to GitHub

Perform the following from the command line within the directory of your local repository. The `git status` steps are technically optional, but will help to illustrate what is happening with your local repository as you work and is useful for newcomers to using git.

1. `git status` to display modified files
2. `git add .` to stage all modified files
  * `git add <file>` will stage one specific modified file
3. `git status` to display all staged files
4. `git commit -m "<message>"` to commit all staged files
  * Get in the habit of providing useful commit messages!
5. `git status` to display commit status
6. `git push` to push all code changes to GitHub
  * You may be required to sign in with your GitHub credentials at this point
7. `git status` to display commit status again

Open your GitHub repository in your browser and ensure that your files are appearing correctly.


## Configure your local web server

For simplicity's sake, I personally advise altering your local web server's configuration to serve files from outside its default `htdocs` folder.

If you installed your local web server manually, see its documentation for instructions on how to change the server root to your local repository's public-facing subdirectory.

For the Bitnami web stacks, you will need to edit `<Bitnami install directory>\apache2\conf\httpd.conf`. Find this file, then perform the following tasks.

1. Open `httpd.conf` in a text editor
2. Change the path specified by `DocumentRoot` to the path to the public-facing subdirectory in your local repository
3. Save `httpd.conf`
4. Restart your local web server

Ensure that your changes were successful by creating a simple HTML or PHP file in your local repository's public-facing subdirectory and navigating to it from localhost. You may need to clear your browser's cache or perform a "hard" refresh to fetch the most recent changes from your local web server.


## Configure your local PHP environment

The course expects students to utilize [PDO](http://php.net/manual/en/book.pdo.php) in their project. By default, the PDO driver for PostgreSQL may be disabled. Perform the following tasks to enable it.

1. Locate the `php.ini` configuration file your local development stack is using
  * You can find the exact path to your `php.ini` in the output of [`phpinfo()`](http://php.net/manual/en/function.phpinfo.php)
2. Open `php.ini` in a text editor
3. Find the `Dynamic Extensions` section
4. Uncomment `extension=pdo_pgsql` if necessary by removing the semicolon at the beginning of the line
5. Save `php.ini`
6. Restart your local web server

In addition, PHP has certain configuration settings that will make developing your project incredibly frustrating if they are not changed from the defaults.

* By default, PHP does not adequately report errors. This makes debugging problems in PHP applications more difficult.
* By default, PHP caches script execution. This forces developers to wait until the cached PHP scripts expire before the web server serves modified PHP files.

For your development environment, you will probably want to change these settings. The following tasks are _optional_ but strongly advised.

1. Locate the `php.ini` configuration file your local development stack is using
  * You can find the exact path to your `php.ini` in the output of [`phpinfo()`](http://php.net/manual/en/function.phpinfo.php)
2. Open `php.ini` in a text editor
3. Find the `Error handling and logging` section
4. Set `error_reporting` to `E_ALL`
5. Set `display_errors` to `ON`
6. Uncomment `error_reporting` and `display_errors` if necessary by removing the semicolon at the beginning of the lines
7. Find the `opcache` section
8. Set `opcache.enable` to `0`
9. Uncomment `opcache.enable` if necessary by removing the semicolon at the beginning of the line
10. Save `php.ini`
11. Restart your local web server


## Connect to your local PostgreSQL database

Ensure your PostgreSQL database is running. From the command line anywhere on your local machine, run the following to connect to your local PostgreSQL database. By default, the user provided by Bitnami's web stacks is `postgres`.

```shell
psql -U <user>
```

You can quit the `psql` client by entering `\q`.


## Create a Heroku application

From the command line in your local repository, run the following to generate a new Heroku application with a randomly assigned name. If prompted, sign in with your Heroku credentials.

```shell
heroku apps:create
```

You may also give your application a specific name by specifying it during creation:

```shell
heroku apps:create <application name>
```


## Ensure Heroku was added as a remote

From the command line in your local repository, run the following.

```shell
git remote -v
```

Your repository should have two different remotes, `heroku` and `origin`.



## Push your code to Heroku

From the command line in your local repository, run the following to push the code on your master branch to Heroku.

```shell
git push heroku master
```


## Open your Heroku application

From the command line in your local repository, run the following.

```shell
heroku open
```


## Provision your Heroku application with a PostgreSQL database

From the command line in your local repository, run the following to generate a new Heroku PostgreSQL with a randomly assigned name.

Note that Heroku only provides [recent versions of PostgreSQL](https://devcenter.heroku.com/articles/heroku-postgresql#version-support-and-legacy-infrastructure). Attempting to connect with a mismatched PostgreSQL client from your local machine will be difficult. If you need to, specify a version with the `--version` flag as indicated in Heroku's documentation.

```shell
heroku addons:create heroku-postgresql:hobby-dev
heroku addons:create heroku-postgresql:hobby-dev --version <version>
```

Afterward, check your Heroku configuration to see the contents of your remote `DATABASE_URL` environment variable.

```shell
heroku config
heroku config:get DATABASE_URL
```


## Connect to Heroku's PostgreSQL database

From the command line in your local repository, run the following to connect to Heroku's PostgreSQL database.

```shell
heroku pg:psql
```

If the above fails for any reason, copy your `DATABASE_URL` variable from the output of `heroku config` and try the following alternative.

```shell
psql <your copied DATABASE_URL variable>
```


## Setup complete

Congratulations! If you made it this far successfully, your PHP project repository, local development environment, and remote application host are good to go. If you encountered an issue along the way, please reach out to me through the class's Slack channel for help figuring out where things went astray.
