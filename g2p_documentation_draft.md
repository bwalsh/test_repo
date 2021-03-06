## Summary

This API endpoint allows users to search for genotype-phenotype associations in the GA4GH datastore. The user can search for associations by building queries composed of features, phenotypes, and/or evidence terms. The API is designed to accommodate search terms specified as either a string, external identifier, ontology identifier, or as an 'entity' (See Data Model section). This flexibility in the schema allows a variety of data to be stored in the database and allows users to express a wide range of queries.

Users will receive an array of associations as a response. Associations contain description and environment fields in addition to the relevant feature, phenotype, and evidence fields for that instance of association.

## API

### Request
The G2P schemas define a single endpoint `/genotypephenotype/search` which accepts a POST of a [request body](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) in JSON. The request may contain a feature, phenotype, and/or evidence, which are combined as a logical AND to query the underlying datastore. Missing types are treated as a wildcard, returning all data.

### Response
[Responses](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) of matching data are returned as a list of  [FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132)s.

## Data Model
The POST data sent as part of `/genotypephenotype/search` must be in JSON format and must obey the [SearchFeaturesRequest](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) schema. [SearchFeaturesResponse](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) is the response from `POST /genotypephenotype/search`, also expressed as JSON.

As shown below, the SearchFeaturesRequest and SearchFeaturesResponse records each have their own data structures, but they use many of the same types (see the 3rd table for shared data-types). Many types rely heavily on the concept of an [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) (see end of document for discussion on usage of OntologyTerms).

Due to the flexibility of the data model, users have a number of options for specifying each query term. For instance, a feature can be defined as either a string, an external identifier, an ontology identifier, or as a 'feature entity'.

The SearchFeaturesRequest record has the following data structure (sub-fields are indented):

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [SearchFeaturesRequest](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L102) | feature, phenotype, evidence | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L105) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, GenomicFeatureQuery | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L108) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, PhenotypeQuery | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L111) | `null`, `string`, ExternalIdentifierQuery, OntologyTermQuery, EvidenceQuery | n/a |
| [ExternalIdentifierQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L69) | ExternalIdentifier | n/a |
| [OntologyTermQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L61) | OntologyTerm | n/a |
| [GenomicFeatureQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L46) | Feature | n/a |
| [PhenotypeQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L54) | PhenotypeInstance | n/a |
| [EvidenceQuery](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L34) | OntologyTerm | check gabriel's changes??? |

SearchFeaturesResponse has the following structure:

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [SearchFeaturesResponse](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L130) | associations, [nextPageToken](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L141) | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[associations](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotypemethods.avdl#L134) | FeaturePhenotypeAssociation | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[FeaturePhenotypeAssociation](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L132) | id, features, evidence, phenotype, description, environmentalContexts | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[features](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L147) | Feature | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L153) | Evidence | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[Evidence](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L113) | OntologyTerm, description | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[phenotype](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L161) | PhenotypeInstance | n/a |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;\|---&nbsp;[environmentalContexts](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L49) | id, OntologyTerm, description | n/a |


Shared data types:

| Item  | Allowed Types/Sub-field Types | Description/Example?? |
| :------------ | :-----------: | :-------------------: |
| [OntologyTerm](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L10) | ontologySource, id, name | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[ontologySource](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/ontologies.avdl#L15) | `string` | n/a |
| [Feature](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | id, parentIds, featureSetId, referenceName, start, end, strand, featureType, attributes | The first six fields here are strings/arrays of strings |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[strand](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L134) | [Strand](???????? not sure where to find in schema) |  n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[featureType](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L105) | OntologyTerm |  n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[attributes](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/sequenceAnnotations.avdl#L54) | `string`, ExternalIdentifier, OntologyTerm | Each attribute is a name that maps to an array of one or more values, called vals |
| [PhenotypeInstance](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L77) | id, OntologyTerm, qualifier, ageOfOnset, description | n/a |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[qualifier](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L90) | `null`, OntologyTerm | i.e. an array of OntologyTerm(s) |
| &nbsp;&nbsp;&nbsp;\|---&nbsp;[ageOfOnset](https://github.com/ga4gh/schemas/blob/be171b00a5f164836dfd40ea5ae75ea56924d316/src/main/resources/avro/genotypephenotype.avdl#L97) | `null`, OntologyTerm | n/a |
| id | `null`, `string` | Note: Some id's are just strings, some also take null.... |
| description | `null`, `string` | n/a |
| name | `null`, `string` | n/a |

## Use cases

1) This is an example of a query formatted in JSON. (Ideally I should explain the biological significance of the query here, say `37da8697` has to do with a phenotype like "back pain") In this case, the `phenotype`, `feature`, and `evidence` fields are all defined using `ExternalIdentifier`s which point to a specific item in a database. 
<pre>
{
  "pageToken": null,
  "phenotype": {
    "ids": [
      {
        "identifier": "37da8697",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "feature": {
    "ids": [
      {
        "identifier": "4841bf74",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "pageSize": null,
  "evidence": {
    "ids": [
      {
        "identifier": "DB00619",
        "version": "*",
        "database": "FOO"
      }
    ]
  }
}
</pre>

2) This query defines `feature` but leaves the `phenotype` and `evidence` fields undefined. All associations which include the specified `feature` (which is defined using the `ExternalIdentifier` type) will be returned.
<pre>
{
  "pageToken": null,
  "phenotype": null,
  "feature": {
    "ids": [
      {
        "identifier": "4841bf74",
        "version": "*",
        "database": "http:\/\/ohsu.edu\/cgd\/"
      }
    ]
  },
  "pageSize": null,
  "evidence": null
}
</pre>

3) This query defines the `phenotype`, `feature`, and `evidence` terms using strings. When this occurs, the database is searched for all matches to those strings (not sure about the specifics here, or how the *wild syntax works). `GIST` stands for 'Gastrointestinal stromal tumor', and `imatinib` is a cancer drug. `KIT` is a gene implicated in the pathogenesis of several cancer types, including GIST.
<pre>
{
  "pageToken": null,
  "phenotype": "GIST",
  "feature": "KIT *wild",
  "pageSize": null,
  "evidence": "imatinib"
}
</pre>

4) It might be good to include a query which uses `GenomicFeatureQuery` or an `OntologytermQuery` which is an array of multiple `OntologyTerms`s. This will demonstrate more use cases....

## Future work
(copied from old document)
Schema constraints: there are several fields within the schemas that are defined as non-null. This may be fine when creating an entity from a data store, however, they are problematic when creating an entity to be used in a query.

## Discussion of Ontologies
I'm not sure how we want to include this. See the bottom of this page: https://github.com/ga4gh/g2p-team/issues/14#issuecomment-173657435
