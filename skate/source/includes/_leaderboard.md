# Leaderboards

## Get the top scores

> Fetching the data:

```java
Sort sort = Sort.ASCENDING;
String tag = "level one";
Date startDate = null;
Date endDate = null;
ScoreList scores = client.getTopScores(sort, tag, startDate, endDate);
```

```shell
curl "https://tmwild.com/api/leaderboards"
  -X GET
  -H "Authorization: Bearer myAPIKey"
```

> Example Response:

```json
[
  {
    "id": 1,
    "user_id": 10,
    "score": 400,
    "tag": "level_3",
    "created_at": "2015-07-12T12:16:22"
  },
  {
    "id": 2,
    "user_id": 8,
    "score": 120,
    "tag": "level_3",
    "created_at": "2015-07-12T12:16:22"
  }
]
```

This retrieves the top or bottom scores of your leaderbaord.

### Query Parameters

Parameter | Required | Type | Default | Description
--------- | -------- | ---- | ------- | -----------
`sort`    | no       | String | `"descending"` | The order the results will be returned, either `"ascending"` or `"descending"`.
`tag`     | no       | String |       | A leaderboard tag to filter the results with.
`start_date` | no    | Date String | The beginning of time. | The start date for a date range filter.
`end_date` | no      | Date String | Now | The end date for a date range filter.  Must be after `start_date`.




## Get a user's scores

> Fetching the data:

```java
int[] userIds = new int[]{13, 14};
Sort sort = Sort.ASCENDING;
String tag = "level one";
Date startDate = null;
Date endDate = null;
ScoreList scores = client.getUserScores(userIds, sort, tag, startDate, endDate);
```

```shell
curl "https://tmwild.com/api/leaderboards?user_id=1"
  -X GET
  -H "Authorization: Bearer myAPIKey"
```

> Example Response:

```json
[
  {
    "id": 3,
    "user_id": 1,
    "score": 450,
    "tag": "full_armor",
    "created_at": "2015-07-12T12:16:22"
  },
  {
    "id": 2,
    "user_id": 1,
    "score": 430,
    "tag": "bonus_level",
    "created_at": "2015-07-12T12:16:22"
  },
  {
    "id": 1,
    "user_id": 1,
    "score": 400,
    "tag": "full_armor",
    "created_at": "2015-07-12T12:16:22"
  }
]
```

This retrieves a list of a user's scores in descending order of time created.

### Query Parameters

Parameter | Required | Type | Default | Description
--------- | -------- | ---- | ------- | -----------
`user_id` | yes      | Integer |      | The ID for the user to query. To query multiple users at once, comma separate `user_id`s.
`sort`    | no       | String | `"descending"` | The order the results will be returned, either `"ascending"` or `"descending"`.
`tag`     | no       | String |       | A leaderboard tag to filter on.
`start_date` | no    | Date String | The beginning of time. | The start date for a date range filter.
`end_date` | no      | Date String | Now | The end date for a date range filter.  Must be after `start_date`.




## Create a new score

> Creating the score:

```java
int userId = 1;
int score = 220;
String tag = "level two";

Score s = new Score(userId, score, tag);
client.saveScore(s);
```

```shell
curl "https://tmwild.com/api/add_score"
  -X POST
  -H "Authorization: Bearer myAPIKey"
  -H "Content-Type: application/json"
  -d '{"user_id":1,"score":220,"tag":"level two"}'
```

> Example Response:

```json
{
  "id": 5,
  "user_id": 1,
  "score": 220,
  "tag": "level two",
  "created_at": "2015-07-12T12:16:22"
}
```

This endpoint creates a new score in the leaderboard and returns the score object back upon success.

### Data Parameters

Parameter | Required | Type | Description
--------- | -------- | ---- | -----------
`user_id` | yes      | Integer | The ID of the user to create a leaderboard entry for.
`score`   | yes      | Integer | The score for the entry in the leaderboard.
`tag`     | no       | String | An identification tag for a leaderboard entry.




## Create a new score and list nearby scores

> Creating the score:

```java
int userId = 1;
int score = 220;
String tag = "level two";
Score s = new Score(userId, score, tag);
String filterTag = tag;

int radius = 2;
ScoreList scores = client.saveScoreAndList(s, radius, Sort.ASCENDING, filterTag);
```

```shell
curl "https://tmwild.com/api/add_score_and_list"
  -X POST
  -H "Authorization: Bearer myAPIKey"
  -H "Content-Type: application/json"
  -d '{"user_id":1,"score":220,"tag":"level two","radius":1}'
```

> Example Response:

```json
[
  {
    "id": 53,
    "user_id": 8,
    "score": 222,
    "tag": "level two",
    "created_at": "2015-03-12T17:12:22"
  }, {
    "id": 5,
    "user_id": 1,
    "score": 220,
    "tag": "level two",
    "created_at": "2015-03-14T18:26:22"
  }, {
    "id": 28,
    "user_id": 3,
    "score": 219,
    "tag": "level one",
    "created_at": "2015-07-11T17:46:22"
  }
]
```

Add a new score and receive the scores above and below the new score. The
parameters for adding the new score are in the POST data, while the
parameters for listing nearby scores are in the URL parameters. The standard
pagination does not apply to this endpoint. `page_size` and `offset` are ignored,
instead relying on the `radius` and new score entry to dictate which scores are
returned.

### Data Parameters

Parameter | Required | Type | Description
--------- | -------- | ---- | -----------
`user_id`   | yes    | Integer | The ID of the user to create a leaderboard entry for.
`score`     | yes    | Integer | The score value for the entry in the leaderboard.
`tag`       | no     | String | An identification tag for a leaderboard entry.

### Query Parameters

Parameter | Required | Type | Default | Description
--------- | -------- | ---- | ------- | -----------
`radius`    | yes    | Integer |      | The number of scores to return above and below the user's score.
`sort`    | no       | String | `"descending"` | The order the results will be returned, either `"ascending"` or `"descending"`.
`filter_tag` | no    | String |       | A leaderboard tag to filter on. Must be either empty or the same as `tag`.
