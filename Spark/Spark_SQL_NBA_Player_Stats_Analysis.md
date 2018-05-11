Spark SQL - NBA Player Stats Analysis
---------------------

The NBA game data from here: http://www.basketball-reference.com/leagues/NBA_2016_per_game.html

	# Create folder to prepare data
	$ mkdir -p /tmp/demo/data_local/input/spark_sql/nba/player_stats_per_game/
	$ cd /tmp/demo/data_local/input/spark_sql/nba/player_stats_per_game/

	
	# Copy local data to hdfs file folder
	$ hadoop fs -rm -R  /user/jwei/demo/spark_sql/nba/
	$ hadoop fs -mkdir -p  /user/jwei/demo/spark_sql/nba/
	$ hadoop fs -copyFromLocal /tmp/demo/data_local/input/spark_sql/nba/player_stats_per_game  /user/jwei/demo/spark_sql/nba/


	$ pyspark --packages com.databricks:spark-csv_2.10:1.3.0


	# Read in all stats
	## Using RDD
 	$ stats = sc.textFile('/user/randy/demo/spark_sql/nba/player_stats_per_game/*.csv')
	$ stats
	## Using Dataframe 
	$ stats = sqlContext.read.format('com.databricks.spark.csv').options(header='true', inferschema='true').load('/user/randy/demo/spark_sql/nba/player_stats_per_game/*.csv')
	$ stats.printSchema()
	
	# Give a name to Dataframe
	$ stats.registerTempTable("stats_tb")

	# Spark SQL 
	$ dfPlayers = sqlContext.sql("select age-min_age as exp,stats_tb.* from stats_tb join (select Player,min(age)as min_age from stats_tb group by Player) as t1 on stats_tb.Player=t1.Player order by stats_tb.Player, exp ")
 	$ dfPlayers # Check type
	$ dfPlayers.saveAsTable("xxx.Players") # Without xxx, it will be saved to default database; save to Hive
	
	hive> show tables; # Check if table saved in hive
	hive> select * from players limit 5;
	
	# Use dataframe API
	$ dfPlayers.groupBy("age").count().orderBy("age").show()

	# Use Spark SQL
	$ sqlContext.sql("Select * from Players where Player like '%Stephen Curry%'").show()
	$ sqlContext.sql("Select SUM(`PS/G`) from Players where Player like '%Stephen Curry%'").show()

