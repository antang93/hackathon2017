Hackathon 2017
==============

Welcome to hackathon 2017!

Download the kickoff deck for more information on the business problem, the hackathon agenda, the data, and more! Find it in the data folder: [Hackathon 2017 kickoff deck](https://github.com/tresata/hackathon2017/blob/master/data/hackathon_kickoff_deck2017.pdf)

Table of Content: 

* [Getting Started](https://github.com/tresata/hackathon2017#getting-started)
* [Machines](https://github.com/tresata/hackathon2017#machines)
* [HDFS](https://github.com/tresata/hackathon2017#hdfs)
* [Data](https://github.com/tresata/hackathon2017#data)
* [Hive](https://github.com/tresata/hackathon2017#hive)
* [Spark](https://github.com/tresata/hackathon2017#spark)
* [pySpark](https://github.com/tresata/hackathon2017#pyspark)
* [SparkR](https://github.com/tresata/hackathon2017#sparkR)
* [Anaconda](https://github.com/tresata/hackathon2017#anaconda)
* [Scalding](https://github.com/tresata/hackathon2017#scalding)
* [Tresata Software](https://github.com/tresata/hackathon2017#tresata-software)
* [Resource Manager](https://github.com/tresata/hackathon2017#resource-manager)

## Getting Started

You can obtain a username and login information from one of the Tresata representatives or from sponsor's room.

ssh into a server where you can access the BBBS data.

    > ssh <username>@hack02.datachambers.com OR
    > ssh <username>@hack03.datachambers.com OR
    > ssh <username>@hack04.datachambers.com OR
    > ssh <username>@hack05.datachambers.com
    

and enter the password you were given.

We made Hive, Spark, pySpark, R and Anaconda command-line interfaces available, and included a tool to compile and run simple Scalding or Spark scripts on-the-fly.

## Machines

We have a Hadoop cluster with one master and four slaves. The slaves have 32 cores, 6 X 1TB data drives, and 128GB of RAM each. You will have ssh access to the slaves.

Please spread yourselves out across the machines.

## HDFS
To access your HDFS location, you need to use hadoop fs commands (some reference: http://www.folkstalk.com/2013/09/hadoop-fs-shell-command-example-tutorial.html). For example, to take a look at your home directory on HDFS, use

    > hadoop fs -ls

or

    > hadoop fs -ls /user/username

## Data

**Data Dictionary** : It's present on /home/shared/data-dictionary and Slack hannel for DATA and [here](https://github.com/tresata/hackathon2017/blob/master/datadictionary)

**LOCAL**
You can find the data on local (all machines) in the /home/shared/bbbs/ directory

```
├── interviews
│   ├── B108.doc
│   ├── B109.doc
│   ├── B120.doc
│   ├── B124.doc
│   ├── B130.doc
│   ├── ...
│   ├── ...
│   ├── ...
│   └── PCL9.doc
├── matches
│   ├── all
│   │   ├── child_volunteer_keys.bsv
│   │   ├── match_details_new.bsv
│   │   ├── match_details_old.bsv
│   │   ├── youth_outcome_reports_new.bsv
│   │   └── youth_outcome_reports_old.bsv
│   ├── active
│   │   ├── match_details.bsv
│   │   └── youth_outcome_reports.bsv
│   └── unsuccessful
│       ├── match_details_new.bsv
│       ├── match_details_old.bsv
│       └── youth_outcome_reports.bsv
├── question_ids.bsv
└── unmatched
    └── rtbm_reports.bsv
```


**HDFS**
You can find the data on HDFS in the /data folder

    /data/bbbs/matches/all/child_volunteer_keys.bsv
    /data/bbbs/matches/all/match_details_new.bsv
    /data/bbbs/matches/all/match_details_old.bsv
    /data/bbbs/matches/all/youth_outcome_reports_new.bsv
    /data/bbbs/matches/all/youth_outcome_reports_old.bsv
    /data/bbbs/matches/active/match_details_new.bsv
    /data/bbbs/matches/active/youth_outcome_reports.bsv
    /data/bbbs/matches/unsuccessful/match_details_new.bsv
    /data/bbbs/matches/unsuccessful/match_details_old.bsv
    /data/bbbs/unmatched/rtbm_reports.bsv

## Hive

Give Hive a whirl and run a sample query:

    > hive

Try pasting the following query into the hive command-line interface:

    hive> show tables;
    OK
    active_match_details_new
    active_youth_outcome_reports_new
    all_child_volunteer_keys
    all_match_details_new
    all_match_details_old
    all_youth_outcome_reports_new
    all_youth_outcome_reports_old
    question_ids
    unsuccessful_match_details_new
    unsuccessful_match_details_old
    unsuccessful_youth_outcome_reports_new
    
    hive> set hive.cli.print.header=true;
    hive> select * from active_match_details_new limit 10;

This will return all the fields for the first ten items in the active_match_details_new table.

If you'd like to create a file from the command line, you can use a create table command:

    hive> create table test row format delimited fields terminated by '|' stored as textfile as select * from active_match_details_new limit 10;

You can then extract the table from the hive warehouse for a table named test:

    df-source-cat --input hive%bbbs.question_ids > textfile

## Spark

**Spark-shell** can be found at /usr/local/lib/spark/bin

Now give the Spark-shell a test:

    > /usr/local/lib/spark/bin/spark-shell --num-executors 4 --executor-cores 1 --executor-memory 1G


Read in the data and run a simple query that calcuates the unique count of ChildZip:

    val df = spark.sqlContext.read.parquet("/data/bbbs-parquet/matches/active/match_details_new.parquet")
    df.groupBy("ChildZip").count().collect()


Note that for your "production" run on the dataset you might want to increase resources used on the cluster:

    --num-executors 4 --executor-memory 4G --executor-cores 4

Keep in mind that a spark-shell takes up these resources on the cluster even when you do not use them so please do not keep a spark-shell with "production" resources open unused.  


## pySpark

**pySpark** can be found at /usr/local/lib/spark/bin


You can also do the same query using a python version of the Spark shell.

    >  /usr/local/lib/spark/bin/pyspark --num-executors 4 --executor-cores 1 --executor-memory 1G

Read in the data and run a simple query that calcuates the unique count of ChildZip:

    df = sqlContext.read.parquet("/data/bbbs-parquet/matches/active/match_details_new.parquet")
    df.groupBy("ChildZip").count().collect()

Note that for your "production" run on the dataset you might want to increase resources used on the cluster:

    --num-executors 4 --executor-memory 4G --executor-cores 4

Keep in mind that a pyspark takes up these resources on the cluster even when you do not use them so please do not keep a pyspark shell (interpreter) with "production" resources open unused.  

## SparkR

**SparkR** can be found at /usr/local/lib/spark/bin

You can also do the same query using a R version of the Spark shell.

    >  /usr/local/lib/spark/bin/sparkR --num-executors 4 --executor-cores 1 --executor-memory 1G


## Anaconda
Anaconda is a completely free Python distribution from [Continuum Analytics](https://www.continuum.io). It includes more than 400 of the most popular Python packages for science, math, engineering, and data analysis. See [the packages included with Anaconda](http://docs.continuum.io/anaconda/pkg-docs).


Getting failimar with conda: http://conda.pydata.org/docs/using/index.html


## Scalding

In addition to the Hive and Spark shells, we're also packaging eval-tool and df-eval-tool. These are tools to compile and run Scalding and Spark scripts without having to create a project. If you create a file called test.scala with the following contents:

    import com.twitter.scalding._
    import com.tresata.scalding.Dsl._
    import com.tresata.scalding.util.ScaldingUtil

    (args: Args) => {
      new Job(args) {
        ScaldingUtil.sourceFromArg(args("input"))
          .groupBy('ChildZip) { _
            .size('Zip_Count)
          } 
          .write(ScaldingUtil.sourceFromArg(args("output")))
      }
    } 


you can run a query on the data set sample from the command-line:

    > eval-tool test.scala --hdfs --input bsv%/data/bbbs/matches/active/match_details_new.bsv --output bsv%zip_counts.bsv

This will generate a bar-separated file called 'zip_counts' in your HDFS home directory, containing the zip numbers along with their total counts.

df-eval-tool

    import com.twitter.scalding.Args
    import com.tresata.spark.sql.Job
    import com.tresata.spark.sql.source.Source

    (args : Args) =>
      new Job(args) {
        override def run = {
          val fapi = Source.fromArg(args, "input").read.fieldsApi
    
          fapi
            .groupBy('ChildZip) { _
              .size('Zip_Count)
            }
            .write(Source.fromArg(args, "output"))
        }
      }

run df-eval-tool

    > df-eval-tool test.scala --input bsv%/data/bbbs/matches/active/match_details_new.bsv --output bsv%zip_counts.bsv

## Tresata Software

#### TREK
Data Inventory Engine built specifically to catalog, profile and report data ontology, quality and format attributes for all data in Hadoop. TREK rapidly profiles and inventories “as-is” data stored in Hadoop across all rows and columns to create an informed view of all valuable enterprise data feeds stored in a single Hadoop cluster.

TREK can be accessed via https://hack01.datachambers.com:5601

For login, it's the same username and password you use or SSH.

## Resource Manager

http://hack01.datachambers.com:8088/
