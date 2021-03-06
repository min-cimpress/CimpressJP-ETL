# CimpressJP ETL Framework

<h2>A framework for performing data pipeline / ETL tasks in Go.</h2>

## Introduction
The main construct in etl framework is Pipeline. A Pipeline has a series of PipelineStages, which will each perform some type of data processing, and then send new data on to the next stage. Each PipelineStage consists of one or more DataProcessors, which are responsible for receiving, processing, and then sending data on to the next stage of processing. DataProcessors each run in their own goroutine, and therefore all data processing can be executing concurrently.

Here is a conceptual drawing of a fairly simple Pipeline:
<pre>
+--Pipeline------------------------------------------------------------------------------------------+
|                                                                       PipelineStage 3              |
|                                                                      +---------------------------+ |
|  PipelineStage 1                 PipelineStage 2          +-JSON---> |  CSVWriter                | |
| +------------------+           +-----------------------+  |          +---------------------------+ |
| |  SQLReader       +-JSON----> | Custom DataProcessor  +--+                                        |
| +------------------+           +-----------------------+  |          +---------------------------+ |
|                                                           +-JSON---> |  SQLWriter                | |
|                                                                      +---------------------------+ |
+----------------------------------------------------------------------------------------------------+
</pre>
## Example
```go
/**
* this code snippet explains how to set up a simplest pipeline to perform the following operations
* . Read from sql
* . Transform the data
* . Dump to Google bigquery
*/

// define a sql query processor
users := processors.NewSQLReader(db, query.UsersQuery())

// define a custom data transformer processor
transformer := transformer.NewUserTransformer()

// define a bigquery output processor*/	
bigqueryconfig := &processors.BigQueryConfig{
	JsonPemPath: <<your json pem>>, 
	ProjectID: <<your projectid>>, 
	DatasetID: <<your bigquery dataset>>
}	
bigquery := processors.NewBigQueryWriter(bigqueryconfig, <<your big query table>>)

// create the sql => custom transform => bigquery static pipeline*/
pipeline, err := pipeline.SQL_Transform_BigQuery(users, transformer, bigquery)

// run the pipeline
result = <-pipeline.Run()
```

## Use-case
- Handling extracting data from application databases, transforming it into reporting-oriented formats, and then loading it into dedicated reporting databases.
- When you have data stored in disparate locations that can't be easily tied together, for example, if you have some CSV data stored on S3, some related data in a SQL database, and want to combine them into a final CSV or SQL output.

## Install
For Shift_JIS encoding / decoding support
* go get -u golang.org/x/text/encoding/japanese

## Contact
mju@cimpress.com
