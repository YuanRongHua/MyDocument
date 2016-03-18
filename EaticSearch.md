# Elasticsearch Clients #


	
Elasticsearch is a highly scalable open-source full-text search and analytics engine. It allows you to store, search, and analyze big volumes of data quickly and in near real time. It is generally used as the underlying engine/technology that powers applications that have complex search features and requirements.

中文指南：http://kibana.logstash.es/content/index.html
## java ##


- Java API:https：//www.elastic.co/guide/en/elasticsearch/client/java-api/current/index.html
- Java source：https://git.oschina.net/.yuanronghua/CodeShard.git
- 时区问题：eaticSearch采用的处理时间统一为UTC时区时间long类型的处理，与中国所在时区UTC+8相差8个小时,ES接受的时间参数进行处理是会统一转换成UTC时区时间进行处理,建议使用long类型的时间参数，这样ES会获取客户端时区进行转换成UTC时区，时区问题可以参考Kibana统一在前台进行处理，在UTC时间基础上+8个小时进行查询展现。

		RangeQueryBuilder rangeQueryBuilder = QueryBuilders.rangeQuery("@timestamp");
    	rangeQueryBuilder.gte("1449590400000");
    	rangeQueryBuilder.lte("1449590400000");
- 关键字查询

    	QueryBuilder queryKeyword = QueryBuilders.commonTermsQuery("message_field","*keyword*");
- 时间统计图表

		searchRequestBuilder.addAggregation(AggregationBuilders
		.dateHistogram("dateHistogram").field("@timestamp")
		.interval(Interval.DAY).preZone("+8:00")
		.preZoneAdjustLargeInterval(true));

- 高亮显示关键字

		searchRequestBuilder.addHighlightedField("message")
				.setHighlighterFragmentSize(2147483647);
		searchRequestBuilder
				.setHighlighterPreTags("<span style=\"color:red\">");
		searchRequestBuilder.setHighlighterPostTags("</span>");

- 查询日志数据

		SearchResponse actionGet = searchRequestBuilder.execute().actionGet();
		Map<String, HighlightField> highlightsource = hit.highlightFields();
		HighlightField titleField = highlightsource.get("message");		
		for (SearchHit hit : hits.getHits()) {
			Map<String, Object> source = hit.getSource();
			result.setLog_time(source.get("@timestamp"));
			String message = "";
			if (titleField != null) {
				Text[] titleTexts = titleField.fragments();
				for (Text text : titleTexts) {
					message += text;
				}
			} else {
				message = (String) source.get("message");
			}
			result.setMessage(message);
		｝


- 查询图表数据

		Aggregations aggregations = actionGet.getAggregations();
		Map<String, Object> groupRecods = new LinkedHashMap<String, Object>();
		InternalDateHistogram internalDateHistogram = aggregations
		.get("dateHistogram");
		for (Bucket b : internalDateHistogram.getBuckets()) {
			groupRecods.put(DateUtil.getDateFromUTCToGMT8(b.getKey()),
			b.getDocCount());
		}






