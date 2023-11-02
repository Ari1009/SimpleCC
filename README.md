# Simple

## Goal

This library is designed to clean fields within a large set of documents using specified paths and clean functions. A document can be in JSON, BSON (from gopkg.in/mgo.v2/bson), or more generally, a `map[string]interface{}`.

## Use Cases

Some interesting use cases include:

- Anonymizing certain fields (such as passwords, usernames, credit cards).
- Fixing typos across a set of documents.
- Updating dates in a dataset for testing purposes.
- These changes can be applied during an export from an oriented document database like MongoDB or Elasticsearch.

## How-To

### Configuration

To associate an element in a document with a clean method, a configuration file in TOML format is required:

```toml
["leaf1"]
method="constantCleaner"
args=[]

["node2.leaf2"]
method="constantCleaner"
args=[]

["node2.leaf4"]
method="constantCleaner"
args=[]

["node3.node31.node311.node3111.leaf32"]
method="constantCleaner"
args=[]

["leaf4"]
method="constantCleaner"
args=[]

["node5.node51.node511.leaf5"]
method="constantCleaner"
args=[]

```
For each X.Y.Z path, these rules are applied:

* X, Y, and Z can be any field names.
* If X is an array, the library looks for Y field in each array element.
* The clean method is applied to Z with the given args.
In the example above, only the constantCleaner method is defined, but the properties can contain different clean methods.

## clean function

```go
// define a struct type
type constantValueCleaner struct {
}

// this type must implement clean method
func (c *constantValueCleaner) Clean(value interface{}) (changed interface{}, err error) {
  changed = 1234
  err = nil
  return 
}

```

This clean method only returns _1234_ constant for any given values.

## all together

```go
// init
propertiesReader := ... // properties reader (from string, file, ...)
cleaners := make(map[string]simple.ValueCleaner) // map cleaner type names given in properties file with a real cleaner instance
cleaners["constantCleaner"] = &constantValueCleaner{} // fill cleaners map 
jsonCleaner := simple.Newsimple(propertiesReader,cleaners) // initialize the json cleaner

// call
jsonCleaner.Clean(objJson) // clean an unmarshalled json object
```

More example in [node_test.go|node_test.go]




