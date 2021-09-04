+++
title = "Beware of SQL injection with Spring Boot and Flyway"
date = "2017-05-01"
type = "post"
categories = ["Security", "Database", "Java"]
tags = [
    "java", "code", "exploit", "injection", "postgresql", "spring", "spring boot", "h2",
    "vulnerability", "flyway", "sql injection", "sql"
]
+++

So this is a hard to accomplish exploit, and is really only accomplishable by first exploiting another exploit first,
or by an employee with some level of trusted access, though this employee might not normally have actual database
access. I do believe you should be aware of it, as it leaves open an avenue you may not be thinking of.

## A sample application

So the first thing we need is an application that uses [Flyway][flyway], let's just use the
[Spring Boot Flyway Sample][sbfs]. The example is semi contrived, I discovered the problem though trying to use flyway
to create admin users for PostgreSQL.

All we need to do is add a second, or modify the existing insert statement in `V1__init.sql`, to
```sql
INSERT INTO person (first_name, last_name) VALUES ( '${adminfirst}', '${adminlast}');
```

If you run the code now, it'll error that the placeholders aren't set, so edit `application.properties`.
```properties
flyway.placeholders.adminfirst=Caleb
flyway.placeholders.adminlast=Cushing
```
running it you'll see the following output
```
[Person [firstName=Dave, lastName=Syer], Person [firstName=Caleb, lastName=Cushing]]
```
Our code is working and we're all set? right? Strings are quoted, so it's secure? well that'll make exploiting this
harder, but it should also be your first cue that this is vulnerable, injection secure code never has quotes like this.

Let's modify one of our placeholders to contain something a bit more nefarious.
```properties
flyway.placeholders.adminfirst=', ''); DROP TABLE person; --
```
Now if you run the code you'll notice the app fails to start during the `person` table being missing. Obviously you
could do something more subtle, like add a user to the database. I can already hear your protest though, you had to
modify a file to do this. To address that, unmodify the line back to `Caleb`, now before running the app set the
environment variable in your shell (or however you set environment variables)
```shell
export FLYWAY_PLACEHOLDERS_ADMINFIRST="', ''); DROP TABLE person; --"
```
Run the application again, you'll see the same error you had before, when it was in your properties file. Spring Boot
will override any defined property with an environment variable.

You can view the full code example [here][sample].

## Why it's important

Ok, so in order to do this you have to have access to the environment that Flyway runs on, while migrations are running.
That'll never happen right? Take my [AWS][aws] [Docker Cloud][dc], you can protect AWS with multifactor (2fa), and
api credentials seem pretty secure. What about Docker Cloud? To access that control panel, which gives you access to
start, stop and modify the environment variables of servers with a simple gui, all you need is a password. This
reinforces my opinion that Docker doesn't care about your security. Yeah but they still need your password, sure
so how secure is that? do you use a different one for each site? if so how are you storing them? I mean with
[LastPass's recent zero days][lp] can you ever really be sure? What if they're an employee that you thought was
adequately restricted because they only have access to the cloud control panel ([25% of breaches are internal][dbir])?
What about your container build process? is that secure? Oh and let's also consider that by default, PostgreSQL, its
container, Flyway, and Spring Boot, do not log the SQL executed by the connection; so how are you going to figure
out if someone did exploit the system?

Is this a practical attack? probably not, and if they have access to your environment variables there's a good chance
you're already owned worse than this. Seriously though, how hard is it to set an environment variable? have you seen
[ShellShock][ss]? reality is, I don't know how hard it would be to create this exploit in your system, or how many
employees you have that could. Maybe all the employees that could already have SQL access anyways.

# A better way to provision PostgreSQL users in Docker

So my original [StackOverflow][so] question was trying to set up Postgres Users with flyway for my initial Docker
container configuration. I did find a better way to do this, instead of doing it with flyway, I'm doing it with
PostgreSQL itself. Now this is still and environment variable, and there's a whole lot of security implications to be
had with putting security credentials into environment variables; it's not really advised.

In order to do this you'll need to extend the [official PostgreSQL image][pg], it will automatically run shell files in
`/docker-entrypoint-initdb.d/`, the `-v` in the below script sets a postgres variable.

```shell
#!/bin/bash
set -e

psql -v ON_ERROR_STOP=1 \
    -v db="${POSTGRES_DB}"  \
    -v user_changeset="${DB_USER_CHANGESET}" \
    -v user_readwrite="${DB_USER_READWRITE}" \
    -v user_readonly="${DB_USER_READONLY}" \
    -v pass_changeset="${DB_PASS_CHANGESET}" \
    -v pass_readwrite="${DB_PASS_READWRITE}" \
    -v pass_readonly="${DB_PASS_READONLY}" \
    --username "${POSTGRES_USER}" \
    --dbname "${POSTGRES_DB}" \
    --file="init-user.sql"
```

now by default PostgreSQL variables are just as vulernable, but unlike Flyway, you can actually quote them properly.

```sql
BEGIN;
REVOKE ALL ON DATABASE :"db" FROM PUBLIC;
DROP SCHEMA public CASCADE;
CREATE SCHEMA application;

CREATE ROLE readonly;
CREATE ROLE readwrite WITH ROLE readonly;

ALTER ROLE readonly SET search_path TO application;

CREATE USER :"user_changeset" WITH ROLE readwrite
    PASSWORD :'pass_changeset';
CREATE USER :"user_readwrite" WITH ROLE readwrite
    PASSWORD :'pass_readwrite';
CREATE USER :"user_readonly" WITH ROLE readonly
    PASSWORD :'pass_readonly';

GRANT USAGE ON SCHEMA application TO readonly;
GRANT USAGE ON ALL SEQUENCES IN SCHEMA application TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA application TO readonly;
GRANT EXECUTE ON ALL FUNCTIONS IN SCHEMA application TO readonly;

GRANT TEMP ON DATABASE :"db" TO readwrite;
GRANT INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA application TO readwrite;

GRANT CREATE ON SCHEMA application TO :"user_changeset";
GRANT TRUNCATE, REFERENCES, TRIGGER ON ALL TABLES IN SCHEMA application TO :"user_changeset";
GRANT USAGE ON LANGUAGE plpgsql TO :"user_changeset";

GRANT CONNECT ON DATABASE :"db" TO :"user_changeset";
GRANT CONNECT ON DATABASE :"db" TO :"user_readwrite";
GRANT CONNECT ON DATABASE :"db" TO :"user_readonly";

ALTER SCHEMA application
OWNER TO :"user_changeset";

COMMIT;
```
So if you use `:"var"` you can quote an identifier, if you use `:'var'` you can quote a value. For more information on
this feature see your [PostgreSQL Documentation][pgdoc].

## TL;DR;

Beware of [`PropertySource`'s][propsrc] and conscious of how their values are used. Don't use [Flyway][flyway]'s
[placeholders][placehldr] with [Spring Boot][sb], instead disable them by setting `flyway.placeholderReplacement=false`.
Also your application should never have access to your databases super user, not even when running changesets.

I did report bugs

* [Flyway][fbug] decided that they will not be adding any quoting features
* [Spring][sbug] has yet to comment, though my report is more to allow self sanitization, and Spring Core, doesn't

implicitly properties to be set with environment variables (you can add this yourself though).

[lp]: http://www.zdnet.com/article/lastpass-zero-day-vulnerability-remotely-compromises-user-accounts/
[dbir]: http://www.zdnet.com/article/ransomware-incidents-surge-education-a-hot-bed-for-data-breaches-according-to-verizon/
[dc]: https://cloud.docker.com/
[aws]: https://aws.amazon.com/
[flyway]: https://flywaydb.org
[placehldr]: https://flywaydb.org/documentation/migration/sql
[propsrc]: https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html
[sb]: https://projects.spring.io/spring-boot/
[sbfs]: https://github.com/spring-projects/spring-boot/blob/master/spring-boot-samples/spring-boot-sample-flyway
[so]: https://bitbucket.org/xenoterracide/sql-injection-with-spring-boot-and-flyway
[pg]: https://hub.docker.com/_/postgres/
[pgdoc]: https://www.postgresql.org/docs/9.6/static/app-psql.html#APP-PSQL-INTERPOLATION
[fbug]: https://github.com/flyway/flyway/issues/1500
[sbug]: https://jira.spring.io/browse/SPR-15187
[ss]: https://blog.cloudflare.com/inside-shellshock/
[sample]: https://bitbucket.org/xenoterracide/sql-injection-with-spring-boot-and-flyway
