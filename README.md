# Data Visualization
The following repository consist mainly of data relevant for performing visualizations and suggestions as to what data visualization tools exist.

## Data

Data regarding different regions, communes, and municipalities in Denmark is included in this repo.
- `regionsopdelt_postnumme_pr_22062021.xlsx` (a list of 1435 danish cities and contain the following columns AMTS_NR, ADRESSERINGSNAVN, KOMMUNE_NR, ADRESSERINGSNAVN_1, POSTNR, BYNAVN)
- The `geojson` folder contains geojson files that represent the different areas of different administrative organs in Denmark. Source - Geodatastyrelsen & Danske Kommuner - FOT data set - 2014-02-13 -Scale 1:500.000

Other relevant data sources regarding specifically Denmark include [1](https://dataforsyningen.dk/) and [2](https://dawadocs.dataforsyningen.dk/dok/api/landsdel)


## Apache superset - Docker setup:
Besides tools like `R-studio` and `Redash` (is an open-source BI tool that gives you access to intuitive dashboards and real-time alerts), `Apache superset` is another intersting data visualization tool.

The following chapter is a description on how to setup `apache superset` in docker. In the [guide description](https://superset.apache.org/docs/installation/installing-superset-using-docker-compose) provided by apache superset the following commands were executed:

```bash
$  git clone https://github.com/apache/superset.git
$  cd superset
$  docker compose -f docker-compose-non-dev.yml pull
```

Before running apache superset in your docker environment you need to change the `SUPERSET_SECRET_KEY` in `/docker/.env-non-dev` with one you have created yourself (this is due to an exploit in apache). Yo` can generate a strong secure key with (which is then added to the .env-non-dev file): 

```
$  openssl rand -base64 42.
```

Now spin up apache superset in docker using the following command
```
$  docker compose -f docker-compose-non-dev.yml up -d
```

IF you get an error like this one `“ValueError: Invalid decryption key` it is because you either ran the compose before changing the secret or maybe because of another problem such as old volumes saving the old data and not assigning you new changes. To fix this remove the volues and run the compose again: 

```bash 
$  docker-compose down -v #(or docker-compose down and docker volume prune), this will remove the old containers and old data 
$  docker compose -f docker-compose-non-dev.yml up -d
```

The key point is you need to remove old volumes such that your changes are eventually assigned. [SOURCE](https://github.com/apache/superset/issues/8538)

## SQLite db in apache superset:
You have to modify `PREVENT_UNSAFE_DB_CONNECTION` in the superset configuration (`config.py` file) changing the parameter from `True` to `False`:
```
PREVENT_UNSAFE_DB_CONNECTION = False
```

In order for apache superset to add a desired sqlitedb the database (or a folder containing databases) need to be mouted as a volume. Add the following volume under: “x-superset-volumes” (note this is just an example):

```
./sqlite_db/fire_incidents.db:/app/sqlite_db/fire_incidents.db
```

Now in the apache superset web GUI connect the database using a url/path to the .db file: `SQLALCGENY URL: sqlite:///sqlite_db/fire_incidents.db`

SOURCES:([1](https://github.com/apache/superset/issues/9748#issuecomment-1124323169), [2](https://github.com/Pipboyguy/superset/commit/22fb8ec10dfba11938489742ff878b543ef68c2b))
