Spark SQL: Uber Dispatch Data Analysis
---------------------

Spark SQL uses a type of Resilient Distributed Dataset called DataFrames.  
DataFrames are composed of Row objects accompanied with a schema which describes  
the data types of each column. A DataFrame may be considered similar to a table  
in a traditional relational database. DataFrames may be created from a variety of input sources including CSV text files.

The spark-csv package is described as a “library for parsing and querying CSV data  
with Apache Spark, for Spark SQL and DataFrames”. This library is compatible with 
Spark 1.3 and above.


	$ mkdir -p /tmp/demo/data_local/input/spark_sql/uber/
	$ cd /tmp/demo/data_local/input/spark_sql/uber/

	# Uber Dispatch Data can be obtained from following
	$ wget https://raw.githubusercontent.com/fivethirtyeight/uber-tlc-foil-response/master/Uber-Jan-Feb-FOIL.csv

	# Reading csv is not originally supported by Spart. Need to download a package. 
	# Package version is decided by Spark version.
	# https://github.com/databricks/spark-csv
	$ pyspark --packages com.databricks:spark-csv_2.10:1.3.0

	# Generate dataframe
	$ df = sqlContext.read.format('com.databricks.spark.csv').options(header='true', inferschema='true').load('file:///tmp/demo/data_local/input/spark_sql/uber/Uber-Jan-Feb-FOIL.csv')
	
	# Check if dataframe is generated correctly
	$ df 
	$ df.printSchema()

	# Give a table name to the dataframe
	$ df.registerTempTable("uber")


	# Spark SQL operations 
	$ distinct_bases = sqlContext.sql("select distinct dispatching_base_number from uber") 
	$ distinct_bases
	DataFrame[dispatching_base_number: string]
	
	# Count the number of rows using dataframe API (vs Spark SQL later)
	$ distinct_bases.count()
	
	# Collect all the data to generate an array for printing
	$ distinct_bases.collect()
	$ for b in distinct_bases.collect(): print b
	
	
	$ df.printSchema()


	# Determining which Uber bases is the busiest based on number of trips
	$ sqlContext.sql("""select distinct(`dispatching_base_number`), 
                                sum(`trips`) as cnt from uber group by `dispatching_base_number` 
                                order by cnt desc""").show()


 	# The 5 busiest days based on number of trips in the time range of the data
 	# Next can check whey these days are the busiest
 	# When the amount of data is small, we can use show()
 	# When multiple line paste doesn't work, use ":paste"
	$ sqlContext.sql("""select distinct(`date`), 
                                sum(`trips`) as cnt from uber group by `date` 
                                order by cnt desc limit 5""").show() 
                                

