# Project 1: Data modeling with Apache Cassandra
Sparkify, a music streaming startup has been collecting data on songs and user activity through their streaming app using CSV files.
### Purpose
The purpose of the project is
- to set up a Cassandra database and model 3 tables according to the 3 queries given below:
    1. **song_details_by_sessioninfo** - `To find the artist, song title and song's length in the music app history that was heard during sessionId = 338 and itemInSession = 4`
    2. **songinfo_by_userinfo** - `To find the name of artist, song (sorted by itemInSession) and user (first and last name) for userid= 10, sessionid = 182`
    3. **userinfo_by_songlistened** - `To find every user name (first and last) in my music app history who listened to the song 'All Hands Against His Own'`
- to determine proper Primary Key i.e partition column and clustering column for the 3 tables
- and finally, to consolidate all csv log files into a single csv file and extract data from it to the respective tables

>**Note: event_datafile_new.csv is the consolidated csv file**

**Query 1:** Tablename : **song_details_by_sessioninfo**

`SELECT artist, song, length from song_details_by_sessioninfo WHERE sessionId = 338 AND itemInSession = 4`

>Primary Key = sessionId (Partition Key) + itemInSession (Clustering Column)

- As sessionId is an unique column and it is used as the 1st condition in the where clause it is used as Partition column for uniform distribution of data
- As itemInSession is used as the 2nd condition in the where clause it is used as a clustering column by which data will be sorted within the Partition

**Query 2:**Tablename : **songinfo_by_userinfo**

`SELECT artist, song, firstName, lastName from songinfo_by_userinfo WHERE userId = 10 AND sessionid = 182 ORDER BY itemInSession ASC`

>Primary Key = (userId + sessionid) (Partition Key) + itemInSession (clustering column)

- As there can be multiple rows for same userId, we combine it with sessionid to make it our partition key so as to ensure uniform data distribution
- As the query needs to be ordered by itemInSession, it needs to become the cluustering column and can be stored in partitions in the ascending ordered. While fetching results order by can be used too.

**Query 3:**Tablename : **userinfo_by_songlistened**

`SELECT firstName, lastName from userinfo_by_songlistened WHERE song = 'All Hands Against His Own'`

>Primary Key = song + userId (Partition Column) : **Song + UserId** will make primary key unique and there will be uniform distribution of data

- As song is the only column used in the where clause we can combine it with usedId to make the partition key unique.
- There is no need of any clustering column

> **Finally all the 3 tables are loaded by selecting the required columns from event_datafile_new.csv and the select query results are returned as Pandas dataframe**
