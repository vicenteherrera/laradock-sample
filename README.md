# laradock-sample

Sample of how to use laradock, based on:
https://laradock.io/getting-started/

# Basic usage

## 1. Clone this repository
```
git clone git@github.com:vicenteherrera/laradock-sample.git
```
This repo includes as a submodule a pinned version of Laradock that you have to retrieve also.
This is a good practice in case Laradock evolves with breaking changes, so you don't have to store your own copy of it, but you are in control of testing if what you are using is compatible with more recent commits.

## 3. Copy sample Laradock config to its folder
```
cp myenv laradock/.env
```

It is set up to use Ngnix, php-fpm and code from a `phpcode` folder.

Take a look at that file as it has in a single place almost every configuration you want to specify about your desired environment.

## 4. Start Laradock and test the project

```
cd laradock
docker-compose up -d nginx mysql
```

The first time it will pull containers, build dockerfile, create database files, so it will take a lot of time. Next executions extremely quicker in comparison.

When it finishes, visit: http://localhost

Modify `phpcode/index.php` and see that it is automatically used when accessing the URL again.

You can take a look at the `laradock/docker-compose.yaml` file to see how it is using your `.env` file to set up the docker environment. This kind of usage is standard in docker-compose, but is very convoluted and difficult to understand, it is meant only for you to modify the `.env` file. But when you learn more about docker-compose, you may end up completely ditching Laradock, and building your own docker-compose that includes only your requisites and is more specifically tailored to you.

## 5. Where are database files?

Database files are persisted in a `~/.laradock/data` folder. You shouldn't commit that to your repository, instead prepare scripts to backup and restore database content as those files database version dependant and can break if the database shuts downs unexpectly (your computer hangs out, runs out of battery, you kill ungraceful the docker container).

You can write a script that backups and restores databases each time you take up or down docker-compose.

## 6. Graceful stop everything

From the `laradock` directory, execute:
```
docker-compose down
```

# Using composer and other PHP based tools

In the previous example, the PHP engine is executed in docker-compose and you may not have one installed in your local machine.

To use the same PHP binary to execute development command line tools like Composer or PHP-Unit, you can execute a shell inside a container that has it set up with access to your source code, using:

```
docker-compose exec -u laradock workspace bash
```

You will find yourself in the `/var/www` directory and the code from your `phpcode` folder will be synced there owned by the `laradock` user, same as you are executing now, and you will have access to all command line PHP tools.