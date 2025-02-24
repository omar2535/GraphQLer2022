
# GraphQLer 2022 

### Setup

Install ubuntu dependencies

```sh
sudo apt-get install sox ffmpeg libcairo2 libcairo2-dev texlive-full
```

Create the virtual environments

```shell
python3 -m venv .env
```

Activate the virtual environment

```shell
source .env/bin/activate
```

Install dependencies

```shell
pip3 install -r requirements.txt
```



--------

### Basic Usage

```shell
usage: 
        GraphQLer - a stateful fuzzing tool on GraphQL
         [-h] --mode {compile,fuzzing}
                                                                        [--test] [--url URL]
                                                                        [--wordlist WORDLIST]
                                                                        [--introspection-json INTROSPECTION_JSON]
                                                                        [--save SAVE] [--schema SCHEMA]
                                                                        [--fuzzer FUZZER] [--no-name-mapping]

options:
  -h, --help            show this help message and exit
  --mode {compile,fuzzing}, -m {compile,fuzzing}
  --test, -t
  --url URL, -u URL
  --wordlist WORDLIST, -w WORDLIST
  --introspection-json INTROSPECTION_JSON, -i INTROSPECTION_JSON
  --save SAVE, -o SAVE
  --schema SCHEMA
  --fuzzer FUZZER
  --no-name-mapping
```

--------

### Building Test Schema

Build from Introspection JSON file

```shell
python3 main.py --mode compile --introspection-json <introspection-json-file-path> --save <dir-to-save-schemas>
```

or, Build from GraphQL server URL

```shell
python3 main.py --mode compile -u <GraphQL-server-url> --save <dir-to-save-schemas>
```

you can use `--no-name-mapping` to bypass name mapping method and perform ID only check for input arguments.

Test Schemas will be saved to folder `<dir-to-save-schemas>`, including:

```
<dir-to-save-schemas>
|____schema.json					// parsed GraphQL schema JSON
|____query_parameter_list.yml		// description of GraphQL Query parameters
|____mutation_parameter_list.yml	// description of GraphQL Mutation parameters
|____mutation_function_list.yml		// description of GraphQL Mutation function types ("Create" | "Update" | "Delete")
```


-------

### Fuzzing Testing

We could use `fuzzing` mode to do fuzzing tests after preparing the test schemas.

```shell
python3 main.py --mode fuzzing -u <GraphQL-server-url> --schema <dir-of-schemas>
```

The path `<dir-of-schemas>` is the previously created schema directory (generated from `compile` mode).


The default fuzzer is a random fuzzer which generates a random value for each input argument. Three types of fuzzers are available and use `--fuzzer` to specify the fuzzer type.

```
--fuzzer <constant | random | wordlist>
```

For wordlist mode, use `--wordlist` to specify a wordlist for string type arguments.

```
--wordlist <wordlist-file-path>
```

After testing, results will be saved in `<dir-of-schemas>`.


----

### Setting up Test Server

To run the test server, use
```shell
cd ./test_server && node ./server.js
```
Make sure you start the test server from the directory `./test_server`.
 
The server will be listening to port `4000`, an Apollo GraphQL Console will be available at `http://localhost:4000/graphql`.

The test server schema and resolver is located at `./test_server/data/schema.gql` and `./test_server/data/schema.js`.

