## Twitter Trending Topology

The topology for this example is as follows:



The topology code implementation is as follows:

topology.newStream(&quot;spout&quot;, spout)

    .each(new Fields(&quot;tweet&quot;), new HashtagExtractor(), new Fields(&quot;hashtag&quot;))

    .groupBy(new Fields(&quot;hashtag&quot;))

    .persistentAggregate(new MemoryMapState.Factory(), new Count(), new Fields(&quot;count&quot;))

    .newValuesStream()

    .applyAssembly(new FirstN(10, &quot;count&quot;))

    .each(new Fields(&quot;hashtag&quot;, &quot;count&quot;), new Debug());

This code does the following:

1. Creates a new stream from the spout. The spout retrieves tweets from Twitter, and filters them for specific keywords (love, music, and coffee in this example).
2. HashtagExtractor, a custom function, is used to extract hash tags from each tweet. These are emitted to the stream.
3. The stream is grouped by hash tag, and passed to an aggregator. This aggregator creates a count of how many times each hash tag has occurred. This data is persisted in memory. Finally, a new stream is emitted that contains the hash tag and the count.
4. Because we are only interested in the most popular hash tags for a given batch of tweets, the FirstN assembly is applied to return only the top 10 values, based on the count field

The spout

The spout, TwitterSpout, uses Twitter4j to retrieve tweets from Twitter. A filter is created (love, music, and coffee in this example), and the incoming tweets (status) that match the filter are stored in a linked blocking queue. Finally, items are pulled off the queue and emitted to the topology.

The HashtagExtractor

To extract hash tags, getHashtagEntities is used to retrieve all hash tags that are contained in the tweet. These are then emitted to the stream.

Enable Twitter

Use the following steps to register a new Twitter application and obtain the consumer and access token information needed to read from Twitter:

1. Go to Twitter Apps and click the Create new app button. When filling in the form, leave the Callback URL field empty.
2. When the app is created, click the Keys and Access Tokens tab.
3. Copy the Consumer Key and Consumer Secret information.
4. At the bottom of the page, select Create my access token if no tokens exist. When the tokens have been created, copy the Access Token and Access Token Secret information.
5. In the TwitterSpoutTopology project you previously cloned, open the resources/twitter4j.properties file, add the information you gathered in the previous steps, and then save the file.

Build the topology

Use the following code to build the project:

    cd [directoryname]

    mvn compile

Test the topology

mvn compile exec:java -Dstorm.topology=com.ashwin.twitterstorm.TwitterTrendingTopology

After the topology starts, you should see debug information that contains the hash tags and counts emitted by the topology. The output should appear similar to the following:

DEBUG: [Quicktellervalentine, 7]

DEBUG: [GRAMMYs, 7]

DEBUG: [AskSam, 7]

DEBUG: [poppunk, 1]

DEBUG: [rock, 1]

DEBUG: [punkrock, 1]

DEBUG: [band, 1]

DEBUG: [punk, 1]

DEBUG: [indonesiapunkrock, 1]
