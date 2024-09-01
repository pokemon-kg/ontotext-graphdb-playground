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

* [Kevin Haller](mailto:contact@kevinhaller.dev)
