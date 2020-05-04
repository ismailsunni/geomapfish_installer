# GEOMAPFISH INSTALLER

This project contains a packaged version of the [software geomapfish v.2.5](https://github.com/camptocamp/c2cgeoportal/).

Please use the [online documentation](https://geomapfish.org/) for further infos on normal geomapfish utiliszation or management.

## Using this installer

This installer contains the base docker images needed to perform a full installation and build of the application, and the code of a basic geomapfish installation.

1. Clone this **Geomapfish Installer** project:

    ```shell
    git clone https://github.com/danduk82/geomapfish_installer.git
    ```

2. Clone the [**package_gmf**](https://github.com/danduk82/package_gmf) project inside the _geomapfish_installer_ directory:

    ```shell
    cd geomapfish_installer
    git clone https://github.com/danduk82/package_gmf
    ```

3. Install the images and build the sample project simply type:

    ```shell
    ./install.sh -i
    ```

Then visit [your local install](https://localhost:8484/) in a webrowser.

## Full Text Search

### Create _tsearch_ table

Below is an example of creating _tsearch_ table for the full text search.

```bash
docker exec package_gmf_db psql -c "
    INSERT INTO main.tsearch (the_geom, layer_name, label, public, role_id, lang, ts)
    SELECT
        ST_TRANSFORM(wkb_geometry, 2056),
        'Country',
        name,
        't',
        NULL,
        NULL,
        to_tsvector('en', name) || to_tsvector('de', name)
    FROM ne_10m_admin_0_sovereignty;
    "
```

### Dump _tsearch_ table

Below is an example of create a dump of _tsearch_ table.

```bash
docker exec -i package_gmf_db pg_dump -d gmf_package_gmf  --table main.tsearch -F c > "tsearch.dmp"
```

### Restore _tsearch_ table

Below is an example of restoring _tsearch_ table a dump previously created.

```bash
docker exec -i package_gmf_db pg_restore -d gmf_package_gmf --clean < "tsearch.dmp"
```
