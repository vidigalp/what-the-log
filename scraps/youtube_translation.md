[0:02] [Music]
[0:11] will the will the chat questions come in here I don't know this is the first time actually see using this yes they will
[0:20] so um so the ones from YouTu
[0:28] well Al so I will also be able to see the chat
[0:58] question
[1:58] for
[2:28] like yep let's let's go live uh I think I know we've been live for a while I was just waiting for 9:30 or so Pacific time
[2:36] to get started but uh yeah why don't we kick things off uh hello welcome
[2:42] everyone uh to the next episode of the yab by DB Friday Tech talk and now this
[2:47] time we're streaming through the streamyard platform which I believe uh
[2:52] you know encompasses the LinkedIn live uh YouTube live and so on so there are multiple ways to um engage uh for our
[3:00] audience uh and please let us know if there if this different format there's any confusion or any any changes that
[3:07] you'd like to give us feedback on but please do keep your comments and all of those coming in as usual and uh then
[3:14] let's Jump Right In uh once again I'm Kanan mutar one of the co-founders at yab it's my pleasure to welcome you all
[3:21] to today's episode of the Friday Tech talk and we have uh PR uh with us today
[3:28] uh who's been uh was given multiple talks earlier on this series pleasure for me to welcome PR and please take it
[3:35] over PR thanks everyone I'm technical evangelist
[3:42] at hope you all having a great day today uh we are going to discuss how to model
[3:47] your data for a distributed database like UK remember data modeling is a very
[3:53] vast topic and this is just an introduction we'll first look into what
[3:58] data moding is all about and understand why data modeling is different for distribute databases as compared to
[4:05] model databases we will also de into how table data is distributed and I'll
[4:10] explain the uh significance of this little later we will explore the importance of primary keys and look into
[4:17] designing the right index for your queries if time permits so we will see how table partitioning is useful for
[4:23] managing large volumes of data first let's look into what data modeling is all about the database is
[4:30] used to store data but the business data has to be correctly represented with proper thems you have to correctly car
[4:37] the various entities into table schema and Define the relationships between them for example if you designing an
[4:43] e-commerce system you have to decide which information should be stored in the customer table products table and
[4:48] how the a table relates to both customer table and uh the a product St you need
[4:55] to decide the schema first by understanding the common access B so once you come to con or the access
[5:00] patterns you have to choose the best indexes for fast data retriever you will have to decide if you are going to
[5:06] partition your data or not so thinking about these and understanding these quite well will help you define various
[5:12] constraints like primary Keys foreign keys and unique Keys most people would have designed
[5:19] tables and indexes for monic databases like MySQL or posters when creating
[5:24] tables and indexes in a distribut database you need to be aware of certain of things let's look at each of
[5:31] firstly in a distributed database uh there are many notes and the table data will be spread across these multiple
[5:38] nodes even though the applications can connect to any single node or many nodes reads and writes most likely will span
[5:44] multiple notes because of this network latencies have to be factored also you
[5:50] could have placed some data in certain geographies and cross go cross region latencies could be
[5:57] high just to show you how you cover DB is similar and at the same time
[6:02] different from a monolithic poess take a look at this ra ug uses the same query layers just like post as posters and so
[6:10] queries look the same but storage layer is spread across multiple nodes so reads and wrs have to be
[6:17] coordinated between notes this is a very very important factor to remember when you model your data for distri
[6:24] database as we just discussed in YB the table data is spread across multiple
[6:30] it's absolutely important for you to understand how your data is distributed so that you can model your schema
[6:35] optimally starting is how data is distributed and store sorted let's look look at two of the starting screens that
[6:43] Prov first here is Hash the data here is distributed based on hash of the primary
[6:50] key the table data is split into tablets and each tablet owns a hash range and
[6:55] stores the keys that falls into that range the r of on the of the primary key
[7:01] and not on the data typ s on this is great for Point lookups and gives a good distribution with growth in data the
[7:08] table automatically splits into more tablets as needed and tables can also be pre-split during
[7:15] creation here I have a a table I don't know if you can see them my
[7:21] mouse things not Shin my mouse so we have a table here with names you're not
[7:27] able to see the mouse uh uh I don't know what setting is that I'm not going to mess with settings right now but so you
[7:34] have on the screen you have a table with names and if you notice each of the tablets have their
[7:41] own hash ranges names go rows with certain names
[7:46] go to certain tablet that's because for example hash of Felicia and hash of Jordan could actually fall into same
[7:52] tablet a t next we look at going to look at sh is
[7:59] in a way if you can think of uh uh you DB is actually a purely range system
[8:05] either we range sh on primary keys or hash of primary keys so in the case of range Harding each tablet owns a primary
[8:12] key range not the hash keys are globally ordered on primary keys so because of this continuous keys are together for
[8:19] example 1 2 3 4 maybe in the same tablet as they stor in the corresponding data
[8:25] types sort typically data could start growing in one taet before growing into
[8:31] another this is great for range lookups and point lookups
[8:36] in so that's so take a look at this visualization the same table with table
[8:42] of names now you have and Henry fall into a to H range owned by T1 or no T
[8:50] Noe two so because T1 owns A2 H range automatically and and Rose with and
[8:56] Henry will be stored in PR just a audio or a technical question
[9:04] uh like is is is there like an echo when
[9:10] when you talking the sound quality is is is okay but it's not as clear do you
[9:15] feel a similar thing when I'm speaking don't think so let
[9:23] See Echo or Echo transation this is good this is better um you just say a few
[9:31] more words hey this is I think it's fine um yeah it's it's
[9:37] not like very uh it's it's I would say 90% or so it's it's fine yeah let's keep
[9:46] going so to summarize as yeah I think one of the
[9:51] audience said there is an echo when you are speaking uh but not from me so uh
[9:57] okay all right so okay I turn on
[10:04] transation uh is this better hey this is PR I think it's fine yeah it's fine okay
[10:13] sorry so uh to summarize Shing hash has no has Shing has no ordering of flows
[10:19] but gives a good distribution it's great for Point lookups especially when you know exactly what you're looking for in
[10:24] the case of range Shing row are Ed on the primary Keys it's great for range lookups and point lookups like K 100 and
[10:31] K less than 100 both in both the cases tablets will split as data GRS
[10:39] understanding sorry to interrupt you again one suggestion was uh people are saying there is an echo so is it
[10:46] possible to try without the headphones just straight up on your laptop or
[10:54] sure but we can all follow what you're saying but it's that the clarity isn't uh like yeah the usual
[11:07] one uh no now you're on mute is good now yeah really good yeah
[11:16] really good but oh no now I can
[11:22] hear oh you can hear you you need the head the headphones I
[11:29] I'm not using my headphones okay okay hello hello hey
[11:35] this
[11:41] [Music] is repeating okay how about
[11:48] now it's good is good yeah all right all right to summarize
[11:55] sorry about that glitch to summarize hash has no ordering of the grow but gives you good distribution uh but again
[12:01] range Harding uh it's great for range lookups and for Point lookups uh it's
[12:06] very critical that you understand how your data is distributed because it's very critical for very important for
[12:12] modeling your data let's look at some sample data so
[12:19] this is the data set that we are going to use in the following slides for illustration for different concepts it's a sensus data set which contains
[12:26] information about people living in different zip codes um we have name age zip code and whether the person is
[12:32] employed or not and in this data setes multiple people can have the same Name Across zip code and ages just like
[12:38] normal first let's look at primary Keys primary Keys is a column or a set of columns that uniquely identifies the row
[12:45] let's look it into this deeper primary key as I said is the unique identifi for the row they should be chosen based on
[12:51] the most common access pattern typically suited for user ID order number uu IDs Etc if not this is very important if not
[12:59] explicitly specified yugabyte DB will automatically generate an internal row ID as primary key this is not visible to
[13:06] the user so always remember to remember to add a primary key when you create the table that's because if you define a
[13:13] primary key after the data is loaded the whole table has to be reordered and could take a lot of time depending upon
[13:19] the data size now let's look at the structure of the primary key the first set of columns are the sharding columns
[13:26] and those sharding columns Define how the table rows are distributed the second set of optional columns are for
[13:32] clustering which Define how the rows Within A Shard are ordered let's understand these with some
[13:38] examples let's create a table for the sensus data and set the primary key to
[13:44] ID hashing we are going to start with hashing here uh and so that we can look up the person's name person's
[13:50] information based on the ID here data is distributed based on the hash of the ID you might have to look up by the name uh
[13:57] but we will use an index for it that we'll look at it later you can see here that the person's
[14:05] information is almost randomly distributed across different nodes but the mapping of ID to tablet is
[14:10] deterministic so when looking for an ID the system exactly knows which tablet to go to and fetches the right
[14:19] data now for range sharting let's consider a Time series like scenario where you want to store the rows ordered
[14:25] by ID this scenario is based on date and time columns but for this example I'm going to stick with id id is usually a
[14:32] monotonically increasing number and let's assume that as we insert data we are going to explicitly in add a
[14:39] monotonically increasing ID so you choose range Ching when you want to retain the you can choose range sharting
[14:45] when you want to retain the original ordering of data when let's see how data is distributed when you range shart on
[14:53] ID so as the row com in the data is stored sorted on ID in ascending order
[14:58] as defined in in the primary key the data is already coming in ordered fashion as as said id is a monotonically
[15:04] increasing number the data grows in only one tablet as even though there are other tablets this is a significant key
[15:10] difference between hash and range Shing but this is only a corner case uh but I just want to point that out especially
[15:17] this a concern when the data is coming in in an already sorted manner of the primary key in the if the data is not
[15:24] coming in a sorted fashion then it's not even a concern we will later see an example of this even though you have
[15:30] three notes you'll be inserting data in one tablet so as the tablet grows here even here the tablet will
[15:37] split now let's see how we can keep rows with the same name together because previously the data was distributed
[15:44] based on ID say you want to uh uh save store the
[15:49] information of people with the same name in the same tablet this way when you look up for a name it will go to one
[15:55] tablet so basically this is data modeling in a distributed database so you want to understand your data is stored and then you easily go and help
[16:02] the system to go and fetch the data quickly for this you have to distribute your data based on name you have to
[16:08] specify the name column as the sharding column but because name is not unique in our data set that's because multiple
[16:15] people can have the same name you need to specify one or more additional columns as a part of the primary key
[16:21] this should be in the clustering part and not in the Shing part for now for the people with the same name rows will
[16:28] be sorted on the ID here you can see that all Michaels are stored in one tablet uh T2 in node
[16:35] one and all susens node 3 are stored in tablet T3 if you notice within the
[16:43] people with the same name the rows are ordered based on the ID that's because we have specified ID in the clustering
[16:49] part now let's see what happens when you range Shard on name and ID primary key
[16:54] has to be unique in the same same way just as we discussed in Shard you have to add ID in the scenario because name
[17:00] is not unique and your entire data will be distributed on name and ordered on
[17:06] name and then and then yeah so the in the
[17:11] previous example I just wanted to make a few points so you said yeah the entire data will be you know ordered by name
[17:18] and within entries that have the same name it'll be ordered by ID kind of the next level of sorting and uh this this
[17:26] kind of a composite primary key where you see multiple columns uh inside it's a pretty common pattern for example uh
[17:35] you may have a retail you may be modeling data for a retailer where you
[17:40] have orders and orders have line items and Order may have like five items uh
[17:45] like a shopping cart so a good primary key for that use case might be order ID
[17:52] followed by the line item number so that all data corresponding to a particular
[17:59] order is near each other right like in that left to right
[18:04] order and yeah just thought I'd give another another example of the same
[18:10] pattern thanks K so uh so as as K said now the rows are ordered on ID within
[18:17] the same name oh so one thing Point even here even as the data comes in an increasing
[18:23] order of ID they will go into different tablets because you are sorting you have distributed the database Bas on name and
[18:29] not ID so it's very critical now to summarize choose your primary key carefully as it is the most important
[18:35] part of your data model for your table um ugab by DB will automatically insert a hidden row ID if primary key is not
[18:41] specified remember to specify the primary key when creating the table as changing it later can be expensive uh
[18:47] keys with the same sharding criteria will end up in the same Shard and clustering columns Define the ordering
[18:53] within the partition I'll try to move a little bit fast because we are short on time we we looked at primary keys with
[18:58] which we can look up data but what if we need to look up some other data based on other criteria than the primary key of
[19:05] the table you can always do that the uh SQL queries will work but it'll be quite slow uh here's where indexes come in
[19:11] handy the purpose of an index is to speed up setup queries indexes are internally treated just like tables they
[19:16] are designed internally like tables in ugab DV and the data distribution is also very similar to and like tables
[19:24] multiple indexes can be attached to a table for speeding up different types of access patterns but remember rights can
[19:29] become slower as each right has to write update multiple indexes just like what we saw in primary
[19:36] keys index have partition and clustering Parts which Define the data distribution and ordering within the partition
[19:42] respectively additionally if you can you can store more columns in the index itself to save a trip to the uh table
[19:48] using the include Clause that's called a covering index we'll discuss it little later now consider query like uh select
[19:55] ID from census where zip code equal to 9408 five so our primary key for our
[20:00] table is ID uh this query will scan the whole table that's because the query
[20:07] executor does not know which rows have zip code 9485 so this could be very slow
[20:13] for a large table so to speed up the look up by ZIP code we add an index on zip code as this create index index zip
[20:20] name on sensus zip code so it could be by Def I mean we going to move into range sharing by default but it immed
[20:27] whether it's range or a hash it the performance is the same now the data of the index is distributed by ZIP code so
[20:35] the same zip code data uh will be stored in the same tablet of the index remember these tablets of the index tablets are
[20:42] quite different from the main tables tablets because they completely different uh uh Concepts but now when
[20:48] you want to look up on zip code it'll be very fast but remember the index stores
[20:53] currently Stores Only zip code if you have to fetch more columns in your select query the query has to go to the
[21:00] main table and fetch to summarize when you have a query like with an equality cost like zip code
[21:06] equal 94085 partition your index on that set of columns this way the query will touch one tablet or very few tablets the
[21:13] lookups would be very fast uh great for range scans too so when the query has columns that
[21:19] are not in the index just I mentioned before even though it uses index it has to go to the main table to fetch the remaining columns let's see how we can
[21:26] avoid them here we for the name when zip code is 9485 by the way 9485 is our headquarters
[21:34] ZIP code the rows with that zip code will be identified quickly with the help of the index but the index does not have
[21:42] the column name uh the name of the person so the query executor has to go
[21:47] to the table main table to fetch that column information in a distributed database like GB this can be expensive
[21:54] as the table and index could be in completely different noes So to avoid that trip to the table
[22:01] we can store the column uh name in the index itself using the covering part
[22:07] which using the include uh Clause this way the query will end up reading just
[22:12] from the index and not go to the uh table this is also known as index only
[22:17] scan so if you do explain analyze on the query you will see index only uh as an
[22:23] output so to summarize when creating an index to speed up a query add all columns in the select CLA to the
[22:28] covering index that are not in the part of the sharding so this makes the query run faster with an index only scan but
[22:35] you have to remember as you store more columns in the index you are trading space for performance now let's look at data
[22:42] ordering so yeah that's a good point Prim so for infrequent queries it may be okay
[22:49] just to take the latency hit of doing a second look up on you know going to the index first getting the pointer to the
[22:55] entry in the main table and then doing a second lookup on theable table but for frequently accessed queries and if
[23:01] latency is very important this tradeoff may be a good one to to you know duplicate the necessary columns both in
[23:08] the index and the main table the system is automatically doing that for you when you use that include Clause syntax in
[23:15] the index uh and yeah you rightly pointed out we essentially this is a
[23:20] space versus performance tradeoff but might be a good one for latency sensitive applications yeah that's thank
[23:27] you all right so let's look at data ordering say you want to find all the information
[23:34] about Michaels living in 94085 with the age uh less than 40 so the query will
[23:40] have to find the ZIP code 94085 first from your data set and look at all the Michaels living there and of the all
[23:47] from all the Michaels it has to filter out uh age less than 40 so this could be
[23:52] expensive if there are a lot of Michaels in living in 94085 but if we add age the clustering
[24:00] part all the row with all Michaels in 9485 will be ordered ascending on age so
[24:07] finding age less than 40 will be quick even if there are thousand rows but only uh 20 people 20 Michaels with less than
[24:14] 40 the query executor will immediately stop after Crossing uh the 20 uh Records
[24:20] so this is how clustering can be useful for range queries all you have to do is add age to the uh clustering part and
[24:28] essentially when uh when you add these columns to the index and you have this
[24:33] ascending sort of sort order essentially all your lookups are very efficient
[24:39] because I mean we all know that you know you can search sorted things in logarithmic time uh get to so instead of
[24:46] scanning know millions of users or whatever the number is in a particular zip code now you are actually just going
[24:53] to the the place where you know you start your search for Michael and you know you're really narrowing down your
[24:58] search space uh to logarithmic time which is like essentially like very
[25:05] efficient sounds good right so incidently placing the right columns in the clustering part
[25:11] will also boost the performance of order by claes because it's already sorted data is already sorted so if your
[25:17] queries will want to return data in dising order if most of your queries this is how that is what you have to
[25:23] decide when you actually model your index so this would still work because if if if the data is stored in ascending
[25:30] order and but you retrieving descending order it'll um we have certain optimizations but it is still better if
[25:36] you define your index with age descending so to summarize clustering defines the
[25:44] order within the each chart very useful for range closes with like for example age less than 40 or greater than 40 when
[25:51] uh especially when age is not in the primary when the column is not part of the primary key of the table remember to
[25:57] add the column the clustering part if you want to retrieve the rows with order buy use descending or ascending sort as
[26:04] needed uh let's look at data what data partitioning is and how it can be useful so we have about six minutes so you can
[26:10] split one large table into multiple partitions each partition is internally a table and it has its own
[26:17] tablets with this you can easily manage large data tables very useful for
[26:22] deleting uh bulk data for example you can easily drop older partitions and ret retain newer ones
[26:28] uh for example if you have you split it you want just to rain three months of data you want to read in the fourth
[26:34] month you can have a rolling window and drop the four oldest partition for scenarios like gdpr you can partition
[26:41] Place partitions of certain users in certain geographies uh remember this is only available in a y SQL API and not in
[26:48] yql because we have adopted this from postgress you can see in this illustration that all IDs in a specific
[26:55] range are in the spe same partition for example in node two you have one to th000 probably if you're collecting data
[27:02] you collected the F you Associated first th000 IDs to certain people and then you feel that that data is not valid anymore
[27:08] you can quickly drop that particular partition without affecting performance it's very quick so partitioning makes
[27:14] data management very very simple also you can create partitions based on user location so that all your
[27:21] users have fast access to data you can create one partition for users in East
[27:27] and one uh and place them in these regions so here I have four region uh cluster uh you have one in us uh sorry
[27:37] two regions in US West and two regions in Us East so your applications on users talking to the US east region will
[27:43] access only their data and so that they'll have very very low latency at the same time people in West will talk
[27:51] their data only in US West and they will have a very uh low latency uh so we call
[27:57] this latency of optimiz geop partitioning and here in this particular scenario now you can use the same
[28:03] concept of partitioning to partition your data based on the country of the user nationality of the user so that you
[28:09] can place your data of rows belonging to say India
[28:15] users in India and European users in Europe so this is very useful for um cases where you want to enforce law like
[28:24] gdpr so some final thoughts uh maybe I just uh since we have a little bit of time I
[28:30] just wanted I know you had a lot of content so maybe there are some areas where we had to go a little faster but I
[28:36] thought this one on partitioning uh I'll call out a few important points I think
[28:41] so firstly like you said Prim uh at the end of the day it looks like one logical table and uh this is essentially
[28:48] inspired by post's partition feature itself and in yaby I think like two
[28:54] common uh uses of among uh you know people that that come to ukap one
[29:00] example I would say is time based partitioning like let's say you have a lot of iot data or even you're a finance
[29:08] uh know credit card sort of business and you're keeping transactions credit card
[29:13] transactions right and you may have a retention requirement like to keep these transactions around for 7even years uh
[29:20] or maybe three years whatever be the case so logically this transaction history table looks like one table but
[29:27] underneath the covers it has many partitions maybe monthly partitions so for every month's transactions go into a
[29:33] child table which we call a partition and that month's partition itself uh is
[29:39] a regular table it could grow into terabytes it can have shards underneath the covers and so on but the really nice
[29:45] thing about it is now that you have these monthly partitions um uh
[29:51] administrative operations like you know dropping um a partition that's more than 3 years old is simply uh you know a
[29:58] simple drop ddl command like so you don't have to run an expensive job to you know Purge data that's older than 3
[30:05] years and so on so it makes like uh data management very easy so that is one use case and the other use case is uh you
[30:14] know taking something like the geolocation column of a table like you know the country they are residing in
[30:19] and partition the data by geography but you may say like okay why do I want to have different tables for uh people in
[30:26] different countries that's where the notion of table spaces comes in where uh
[30:31] you can say hey table space for North America is pinned to nodes in the
[30:37] yugabyte cluster that are in you know North America data centers and then you
[30:43] can so we you know yugabyte table space syntax uh you know has has essentially extended the postgress syntax to be able
[30:50] to specify these placement properties to say hey I want the uh table that that is
[30:56] for my Europe users to live in a table space that is pinned to servers and
[31:02] zones and data centers that are in Europe right so those are some of the common use cases we see of
[31:09] partitioning that's very true I just not touch upon table spaces because I had I felt that we might not have enough time
[31:15] yeah yeah I know I know it's okay to go a few minutes extra yeah go for it I think we almost done here so to conclude
[31:22] um uh so make sure that you understand how your data is distributed or shed uh
[31:28] be aware of the different parts of the primary keys and how you can best use them you can use our explain analyze
[31:35] command uh standard prois command to see how your quer is executed how the index
[31:41] can be used or improved I have not touched based on because that's a using
[31:47] explain analy and understanding the different parts of it it's a it's a pretty complex Topic in all all by
[31:53] itself data modeling might feel to be time consuming initially but the initial
[31:58] investment you put in you will uh reap a lot of benefits much later on for a longer time uh use partitioning for data
[32:05] management and uh using uh we with along with table spaces you can do a lot of
[32:12] magic thank you uh you can check out us uh check out all the code base on GitHub and if in case you have any questions
[32:18] please join our public slack yab by.com slack thank you that'll be
[32:23] all yeah and uh there was a question that has come in uh but it may be a
[32:29] little uh bit for a a topic of its own uh but the question was can you explain the internal architecture of how yob by
[32:36] DB works and then know the background processes and memory components and so on
[32:42] so that's like so you have to look at about seven or eight different yft topics for this just alone uh but uh you
[32:50] can check out our docs we have a good explanation on architecture section so you can understand how our query layer
[32:56] is designed or how storage layer we have a huge section on Doc TB which is our storage layer you can look at our
[33:02] transactions we have sections on how the data is distributed uh so docs would be
[33:09] the great place for it so yeah uh thanks s for that question like PR said uh at
[33:15] the end of the day yugabyte is an oltp database it's a transactional database but designed with uh you know elasticity
[33:23] resilience and scale in mind so uh it's a distributed transaction dat database
[33:28] uh that is designed and we have people and typically people might run us on a three Noe cluster but there are folks
[33:34] like you know 150 node uh type of cluster so it looks like a single logical database that is easy to expand
[33:41] um uh or Shrink and and be highly available uh you know can tolerate the failures of nodes zones
[33:49] regions uh and uh there's a lot behind the scenes there's the storage layer there's the query layer we support like
[33:55] a Cassandra compatible interface we call it yql we support a post bre compatible
[34:00] interface yql so there are many levels at which to discuss this some of the early yaby DB Friday Tech talks cover
[34:08] some of these topics but our docs um if you just search for yaby DB architecture in our documentation page you'll get a
[34:14] good sense of kind of like a a trail to walk through to cover uh the various
[34:21] topics and do continue to join us on some of these future talks uh with that why don't we um close
[34:29] the session for today I wanted to thank everybody for for joining today and see you all on another episode of uh you
[34:35] know Friday Tech talk Series so thanks PR to you thank you all thank you all uh
[34:42] it's it's been great uh but please take out our docs if in case you have any further questions thanks
[34:49] K should we hang around
