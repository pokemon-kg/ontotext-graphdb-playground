# Pokémon KG - GraphDB instance

This repository offers a `docker-compose` file, which provides a running GraphDB
instance with the [Pokémon KG](https://pokemonkg.org/) loaded. Hence, an
up-to-date version of Docker is required (or any other equivalent container
engine). The `khaller/graphdb-free` container image is used. The documentation
of this image can be found [here](https://github.com/khaller93/graphdb-free).

## Running GraphDB

Starting the GraphDB instance is as easy as running the following command.

```bash
$ docker-compose up
```

The GraphDB instance can now be found at [http://localhost:7270](http://localhost:7270).

After a successful start of the container image, the data of the GraphDB
instance is stored in the `db` folder. On Linux systems with SELinux enabled,
the run of the container might fail, due to missing permission to read/write to
this directory on the host filesystem. Then you can add a proper SELinux label
to this directory with '`chcon -Rt svirt_sandbox_file_t ./db`', or disable
SELinux with '`setenforce 0`'.

```
db
├── data
├── logs
├── README.md
└── work
```

Moreover, `.lock` files might be added to the `config` folder. These `.lock`
files indicate that the initialization of the `pokemon` repository has been
performed successfully. If you want to re-initialize the repository for some
reason, you can delete these `.lock` files manually and restart the container.
Alternatively, you can use the provided `clean-locks.sh` to delete these `.lock`
files.

```
config
├── config.ttl
├── init.lock
├── sparql
├── sparql.lock
└── toLoad
```

## Sample queries

### Search for Snorlax using FTS index

```sparql
PREFIX con: <http://www.ontotext.com/connectors/lucene#>
PREFIX con-inst: <http://www.ontotext.com/connectors/lucene/instance#>
PREFIX poke: <https://pokemonkg.org/ontology#>

SELECT ?entity ?p ?o {
    ?entity a poke:Species ;
            ?p ?o .
    [] a con-inst:esm ;
       con:query "Snorlax" ;
       con:entities ?entity .
}
ORDER BY ASC (?entity)
```

### List all Pokémons ordered by Nationl Dex number

```sparql
PREFIX rdfs: <http://www.w3.org/2000/01/rdf-schema#>
PREFIX poke: <https://pokemonkg.org/ontology#>

SELECT ?pokemon ?label {
    <https://pokemonkg.org/instance/pokedex/national> poke:hasPokédexEntry ?entry .
    ?entry poke:describesPokémon ?pokemon ;
    	poke:entryNumber ?nr .
    ?pokemon a poke:Species ;
		rdfs:label ?label .
    FILTER(lang(?label) = "en") .
}
ORDER BY ASC (?nr)
```

# Contact

* Kevin Haller - [contact@kevinhaller.dev](mailto:contact@kevinhaller.dev)