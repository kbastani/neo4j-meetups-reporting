# Graph-based Reporting Platform

Welcome to the docs page for this project. This project is meant to be a sample application for building an end-to-end reporting platform using the *Neo4j* graph database. In addition to Neo4j, this project uses the *Node.js* software platform for each of its modules. The modules in this project include a web-based dashboard with charts, a documented REST API, and scheduled data import services into Neo4j.

* [Getting Started](#getting-started)
* [Analytics REST API](#rest-api)
* [Analytics Web Dashboard](#dashboard)
* [Analytics Data Import Scheduler](#data-import-scheduler)

# Getting Started

This section describes how to get started with this example project, a definition of prerequisites, example usage, and on how to get all of the project modules setup and running on your machine.

* [Prerequisites](#prerequisites)
  *  [Node.js](#nodejs)
  *  [Neo4j Graph Database](#neo4j-graph-database)
  *  [Meetup.com API Key](#meetupcom-api-key)

### Prerequisites

Below is a summary of prerequisites that are required for successfully running the modules of this project.

#### Node.js

[Node.js](http://www.nodejs.org/) is a software platform for scalable server-side and networking applications. Node.js applications are written in JavaScript, and can be run within the Node.js runtime on Windows, Mac OS X and Linux with no changes.

#### Neo4j Graph Database

[Neo4j](http://www.neo4j.org/) is a robust (fully ACID) and open-source graph database. A graph database is a database that uses graph structures with nodes, edges, and properties to represent and store data. A graph database is any storage system that provides index-free adjacency. Neo4j uses a declarative graph query language with SQL-like syntax called Cypher.

#### Meetup.com API Key

[Meetup](http://www.meetup.com/) is the world's largest network of local groups. Meetup makes it easy for anyone to organize a local group or find one of the thousands already meeting up face-to-face. More than 9,000 groups get together in local communities each day, each one with the goal of improving themselves or their communities. 

An [API key from Meetup.com](http://www.meetup.com/meetup_api/) is required for this sample application. 

# REST API

This section covers the graph-based reporting REST API. This REST API communicates with the Neo4j graph database and exposes the database layer to the front-end web dashboard. The REST API is a fork of [Neo4j Swagger](#the-neo4j-swagger-project). [Swagger](#the-swagger-project) is a specification and complete framework implementation for describing, producing, consuming, and visualizing RESTful web services.

* [Demo](#demo-1)
* [Setup](#setup)
* [Dependencies](#dependencies)
* [Swagger](#swagger)
  *  [The Swagger Project](#the-swagger-project)
  *  [The Neo4j Swagger Project](#the-neo4j-swagger-project)
* [Project Files](#project-files)

### Demo

A live online demo for the REST API is located here: [http://meetup-analytics-api.herokuapp.com/docs/](http://meetup-analytics-api.herokuapp.com/docs/)

![REST API demo](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/rest-api-demo.png "Reporting REST API demo")

### Setup

From the terminal, go to the `api` directory of the project and run `npm install`, after `node_modules` are installed, run `node app`. The reporting REST API will be started at `http://localhost:3000`

### Dependencies

Below are the Node.js module dependencies for the reporting REST API component.

```javascript
"dependencies": {
  "connect": ">= 1.8.x",
  "express": "3.x",
  "jade": ">=0.26.3",
  "neo4j": ">=1.1.0",
  "underscore": ">=1.3.1",
  "underscore.string": ">=2.3.3",
  "swagger-node-express": "1.3.x",
  "neo4j-swagger-ui": ">=0.0.11",
  "colors": "0.6.x",
  "async": ">=0.2.9",
  "moment": ">=2.4.0",
  "hat": ">=0.0.3",
  "mocha": ">= 1.6.0",
  "should": ">= 0.6.3",
  "random-name": ">=0.1.0"
}
```

### Swagger

The REST API module of this project is based on a fork of Swagger. See below for a brief summary for Swagger and its fork, Neo4j Swagger.

#### The Swagger Project

[Swagger](https://helloreverb.com/developers/swagger) is a specification and complete framework implementation for describing, producing, consuming, and visualizing RESTful web services. The overdonuthing goal of Swagger is to enable client and documentation systems to update at the same pace as the server. The documentation of methods, parameters, and models are tightly integrated into the server code, allowing APIs to always stay in sync. With Swagger, deploying managing, and using powerful APIs has never been easier.

#### The Neo4j Swagger Project

[The Swagger project was forked](https://github.com/tinj/node-neo4j-swagger-api) and customized by [Mat Tyndal](https://github.com/flipside) to use Neo4j as its data source. The REST API module of this project is extended from the Neo4j swagger project.

### Project Files

This section documents the directory structure of the REST API module of this project, example usage, and specifications for each API call.

* [api/app.js](#apiappjs)
* [api/neo4j/cypher.js](#apineo4jcypherjs)
* [api/models](#apimodels)
  *  [api/models/analytics.js](#apimodelsanalyticsjs)
    * [getWeeklyGrowthPercent](#getweeklygrowthpercent)
    * [getMonthlyGrowthPercent](#getmonthlygrowthpercent)
    * [getMonthlyGrowthPercentByTag](#getmonthlygrowthpercentbytag)
    * [getMonthlyGrowthPercentByLocation](#getmonthlygrowthpercentbytag)
    * [getCities](#getcities)
    * [getCountries](#getcountries)
    * [getGroupCountByTag](#getgroupcountbytag)
* [api/routes](#apiroutes)
  *  [api/routes/analytics.js](#apiroutesanalyticsjs)
    * [/analytics/weeklygrowth](#analyticsweeklygrowth)
    * [/analytics/monthlygrowth](#analyticsmonthlygrowth)
    * [/analytics/monthlygrowthbytag](#analyticsmonthlygrowthbytag)
    * [/analytics/monthlygrowthbylocation](#analyticsmonthlygrowthbylocation)
    * [/analytics/groupsbytag](#analyticsgroupsbytag)
    * [/analytics/cities](#analyticscities)
    * [/analytics/countries](#analyticscountries)

#### api/app.js

The `api/app.js` file is the entry-point to starting the REST API.

#### api/neo4j/cypher.js

The `api/neo4j/cypher.js` file is the Neo4j graph database driver for Node.js. This file is the helper module that makes RESTful requests to the Neo4j REST API.

```javascript
var neo4j = require('neo4j'),
    db = new neo4j.GraphDatabase('http://localhost:7474/'),
    _ = require('underscore');
```

Replace `http://localhost:7474/` with your desired Neo4j database endpoint when deploying to production.

#### api/models

The `api/models` folder contains files that manage Cypher queries that map to REST API endpoints. This is the data access layer that builds Cypher queries and executes transactions on a Neo4j database instance.

##### api/models/analytics.js

The `api/models/analytics.js` file contains a set of functions that are meant to manage parameterized transactions to the Neo4j graph database.

The Neo4j Cypher queries mapped to the `api/models/analytics.js` file are defined below.

* [getWeeklyGrowthPercent](#getweeklygrowthpercent)
* [getMonthlyGrowthPercent](#getmonthlygrowthpercent)
* [getMonthlyGrowthPercentByTag](#getmonthlygrowthpercentbytag)
* [getMonthlyGrowthPercentByLocation](#getmonthlygrowthpercentbytag)
* [getCities](#getcities)
* [getCountries](#getcountries)
* [getGroupCountByTag](#getgroupcountbytag)

---

##### getWeeklyGrowthPercent

Gets the weekly growth percent of meetup groups as a time series. Returns a set of data points containing the week of the year, the meetup group name, and membership count. Gets the time series that models the growth percent of meetup groups week over week.

###### Neo4j Cypher Query

```cypher
MATCH (d:Day)<-[:HAS_DAY]-(month:Month)
WHERE d.timestamp > { startDate } AND d.timestamp < { endDate }
WITH DISTINCT month
MATCH (month:Month)-[:HAS_DAY]->(day:Day { dayofweek: 1 })
MATCH (tag:Tag), (location:Location{ country: { country } })
WHERE tag.tag in { topics }
WITH tag, location, day
MATCH (tag)<-[:HAS_TAG]-(group:Group)-[:LOCATED_IN]->(location) WITH DISTINCT group, day
MATCH (group)-[:HAS_MEMBERS]->(stats:Stats)-[:ON_DAY]->(day)
WITH DISTINCT (day.month + "/" + day.day + "/" + day.year) as week, group.name as group, stats.count as members, day
ORDER BY day.timestamp
RETURN week, group, members
```

###### Parameter List

Below is a list of parameter names for this API call.

* `startDate`
* `endDate`
* `city`
* `country`
* `topics`
* `groups`

###### Parameter Usage

Below is a list of parameters for this API call, a description, and example usage.

**startDate**
* A date to retrieve results from.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'1/1/2014'
```

**endDate**
* A date to retrieve results until.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'4/1/2014'
```

**city**
* The city name where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'San Francisco'
```

**country**
* The country code where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'US'
```

**topics**
* A list of topics that a meetup group must have to be returned in the result set.
* Multiple topic names should be delimited by a comma.
  
*Example:*

```javascript 
['neo4j', 'mongodb', 'cassandra']
```

**groups**
* A list of names to match on meetup groups.
* Only groups with the name that are specified in the list are returned.
* Multiple topic names should be delimited by a comma. 
* Leave blank to ignore this field.
  
*Example:*

```javascript 
['Graph Database - San Francisco', 'Bay Area Graph Geeks']
```

###### Example Result

```javascript
[
  {
    "week": "1/6/2014",
    "group": "Bay Area Graph Geeks",
    "members": 117
  },
  {
    "week": "1/6/2014",
    "group": "Graph Database - San Francisco",
    "members": 623
  },
  {
    "week": "1/13/2014",
    "group": "Bay Area Graph Geeks",
    "members": 119
  },
  {
    "week": "1/13/2014",
    "group": "Graph Database - San Francisco",
    "members": 624
  }
]
```

---

##### getMonthlyGrowthPercent

Gets the monthly growth percent of meetup groups as a time series. Returns a set of data points containing the month of the year, the meetup group name, and membership count. Gets the time series that models the growth percent of meetup groups month over month.

###### Neo4j Cypher Query

```cypher
MATCH (d:Day)<-[:HAS_DAY]-(month:Month)
WHERE d.timestamp > { startDate } AND d.timestamp < { endDate }
WITH DISTINCT month
MATCH (month:Month)-[:HAS_DAY]->(day:Day { day: 1 })
MATCH (tag:Tag), (location:Location{ city: { city }, country: { country } })
WHERE tag.tag in { topics }
WITH tag, location, day
MATCH (tag)<-[:HAS_TAG]-(group:Group)-[:LOCATED_IN]->(location) WITH DISTINCT group, day
MATCH (group)-[:HAS_MEMBERS]->(stats:Stats)-[:ON_DAY]->(day)
WITH DISTINCT (day.month + "/" + day.day + "/" + day.year) as month, group.name as group, stats.count as members, day
ORDER BY day.timestamp
RETURN month, group, members
```

###### Parameter List

Below is a list of parameter names for this API call.

* `startDate`
* `endDate`
* `city`
* `country`
* `topics`
* `groups`

###### Parameter Usage

Below is a list of parameters for this API call, a description, and example usage.

**startDate**
* A date to retrieve results from.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'1/1/2014'
```

**endDate**
* A date to retrieve results until.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'4/1/2014'
```

**city**
* The city name where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'San Francisco'
```

**country**
* The country code where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'US'
```

**topics**
* A list of topics that a meetup group must have to be returned in the result set.
* Multiple topic names should be delimited by a comma.
  
*Example:*

```javascript 
['neo4j', 'mongodb', 'cassandra']
```

**groups**
* A list of names to match on meetup groups.
* Only groups with the name that are specified in the list are returned.
* Multiple topic names should be delimited by a comma. 
* Leave blank to ignore this field.
  
*Example:*

```javascript 
['Graph Database - San Francisco', 'Bay Area Graph Geeks']
```

###### Example Result

```javascript
[
  {
    "month": "1/1/2014",
    "group": "Bay Area Graph Geeks",
    "members": 116
  },
  {
    "month": "1/1/2014",
    "group": "Graph Database - San Francisco",
    "members": 622
  },
  {
    "month": "2/1/2014",
    "group": "Bay Area Graph Geeks",
    "members": 123
  },
  {
    "month": "2/1/2014",
    "group": "Graph Database - San Francisco",
    "members": 634
  }
]
```

---

##### getMonthlyGrowthPercentByTag

Gets the monthly growth percent of meetup group tags as a time series. Returns a set of data points containing the month of the year, the meetup group tag name, and membership count. Gets the time series that models the growth percent of meetup group tags month over month.

###### Neo4j Cypher Query

```cypher
MATCH (d:Day)<-[:HAS_DAY]-(month:Month)
WHERE d.timestamp > { startDate } AND d.timestamp < { endDate }
WITH DISTINCT month
MATCH (month:Month)-[:HAS_DAY]->(day:Day { day: 1 })
MATCH (tag:Tag), (location:Location{ city: { city }, country: { country } })
WHERE tag.tag in { topics }
WITH tag, location, day
MATCH (tag)<-[:HAS_TAG]-(group:Group)-[:LOCATED_IN]->(location) WITH DISTINCT group, day, tag
MATCH (group)-[:HAS_MEMBERS]->(stats:Stats)-[:ON_DAY]->(day)
WITH DISTINCT (day.month + "/" + day.day + "/" + day.year) as month, tag.tag as tag, sum(stats.count) as members, day
ORDER BY day.timestamp
RETURN month, tag, members
```

###### Parameter List

Below is a list of parameter names for this API call.

* `startDate`
* `endDate`
* `city`
* `country`
* `topics`
* `groups`

###### Parameter Usage

Below is a list of parameters for this API call, a description, and example usage.

**startDate**
* A date to retrieve results from.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'1/1/2014'
```

**endDate**
* A date to retrieve results until.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'4/1/2014'
```

**city**
* The city name where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'San Francisco'
```

**country**
* The country code where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'US'
```

**topics**
* A list of topics that a meetup group must have to be returned in the result set.
* Multiple topic names should be delimited by a comma.
  
*Example:*

```javascript 
['neo4j', 'mongodb', 'cassandra']
```

**groups**
* A list of names to match on meetup groups.
* Only groups with the name that are specified in the list are returned.
* Multiple topic names should be delimited by a comma. 
* Leave blank to ignore this field.
  
*Example:*

```javascript 
['Graph Database - San Francisco', 'Bay Area Graph Geeks']
```

###### Example Result

```javascript
[
  {
    "month": "1/1/2014",
    "tag": "neo4j",
    "members": 738
  },
  {
    "month": "2/1/2014",
    "tag": "neo4j",
    "members": 757
  },
  {
    "month": "3/1/2014",
    "tag": "neo4j",
    "members": 885
  }
]
```

---

##### getMonthlyGrowthPercentByLocation

Gets the monthly growth percent of meetup group locations and tags as a time series. Returns a set of data points containing the month of the year, the meetup group tag name, the city, and membership count. Gets the time series that models the growth percent of meetup group tags month over month, by city.

###### Neo4j Cypher Query

```cypher
MATCH (d:Day)<-[:HAS_DAY]-(month:Month)
WHERE d.timestamp > { startDate } AND d.timestamp < { endDate }
WITH DISTINCT month
MATCH (month:Month)-[:HAS_DAY]->(day:Day { day: 1 })
MATCH (tag:Tag), (location:Location{ city: { city }, country: { country } })
WHERE tag.tag in { topics }
WITH tag, location, day
MATCH (tag)<-[:HAS_TAG]-(group:Group)-[:LOCATED_IN]->(location) WITH DISTINCT group, day, tag, location
MATCH (group)-[:HAS_MEMBERS]->(stats:Stats)-[:ON_DAY]->(day)
WITH DISTINCT (day.month + "/" + day.day + "/" + day.year) as month, location.city as city, tag.tag as tag, sum(stats.count) as members, day
ORDER BY day.timestamp
RETURN month, tag, members, city
```

###### Parameter List

Below is a list of parameter names for this API call.

* `startDate`
* `endDate`
* `city`
* `country`
* `topics`
* `groups`

###### Parameter Usage

Below is a list of parameters for this API call, a description, and example usage.

**startDate**
* A date to retrieve results from.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'1/1/2014'
```

**endDate**
* A date to retrieve results until.
* Results will be returned for the entire week that the start date occurs within.

*Example:*

```javascript 
'4/1/2014'
```

**city**
* The city name where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'San Francisco'
```

**country**
* The country code where a meetup group resides.
* This field is case sensitive.
* Leave blank to query on world-wide meetup groups.

*Example:*

```javascript 
'US'
```

**topics**
* A list of topics that a meetup group must have to be returned in the result set.
* Multiple topic names should be delimited by a comma.
  
*Example:*

```javascript 
['neo4j', 'mongodb', 'cassandra']
```

**groups**
* A list of names to match on meetup groups.
* Only groups with the name that are specified in the list are returned.
* Multiple topic names should be delimited by a comma. 
* Leave blank to ignore this field.
  
*Example:*

```javascript 
['Graph Database - San Francisco', 'Bay Area Graph Geeks']
```

###### Example Result

```javascript
[
  {
    "month": "1/1/2014",
    "tag": "neo4j",
    "city": "San Francisco",
    "members": 738
  },
  {
    "month": "2/1/2014",
    "tag": "neo4j",
    "city": "San Francisco",
    "members": 757
  },
  {
    "month": "3/1/2014",
    "tag": "neo4j",
    "city": "San Francisco",
    "members": 885
  }
]
```

---

##### getCities

Gets a list of cities that meetup groups reside in. Returns a distinct list of cities for typeahead. Gets a distinct list of cities that a meetup group resides in.

###### Neo4j Cypher Query

```cypher
MATCH (location:Location)
RETURN DISTINCT location.city as city
```

###### Parameter List

*This query does not use parameters.*

###### Example Result

```javascript
[
  {
    "city": "New York"
  },
  {
    "city": "Brooklyn"
  },
  {
    "city": "Boston"
  },
  {
    "city": "Cambridge"
  }
]
```

---

##### getCountries

Gets a list of countries that meetup groups reside in. Returns a distinct list of countries for typeahead. Gets a distinct list of countries that a meetup group resides in.

###### Neo4j Cypher Query

```cypher
MATCH (location:Location)
RETURN DISTINCT location.country as country
```

###### Parameter List

*This query does not use parameters.*

###### Example Result

```javascript
[
  {
    "country": "US"
  },
  {
    "country": "GB"
  },
  {
    "country": "FR"
  },
  {
    "country": "DE"
  },
  {
    "country": "SE"
  },
  {
    "country": "DK"
  },
  {
    "country": "BE"
  },
  {
    "country": "NO"
  }
]
```

---

##### getGroupCountByTag

Gets a count of groups by tag. Returns a list of tags and the number of groups per tag. Gets a list of tags and the number of meetup groups per tag.

###### Neo4j Cypher Query

```cypher
MATCH (tag:Tag), (location:Location{ city: { city }, country: { country } })
WHERE tag.tag in { tags }
MATCH (tag)<-[:HAS_TAG]-(group:Group)-[:LOCATED_IN]->(location)
RETURN tag.tag as tag, count(group) as count
```

###### Parameter List

Below is a list of parameter names for this API call.

* `tags`

###### Parameter Usage

Below is a list of parameters for this API call, a description, and example usage.

**tags**
* A list of tags that a meetup group must have to be returned in the result set.
* Multiple tags should be delimited by a comma.
  
*Example:*

```javascript 
['neo4j', 'mongodb', 'cassandra']
```

###### Example Result

```javascript
[
  {
    "tag": "neo4j",
    "count": 2
  }
]
```

---

#### api/routes

The `routes` directory contains files that define a group of endpoints with a set of specifications for each REST API call. These specifications contain a `description`, `path`, `notes`, `summary`, `method`, `params`, `responseClass`, `errorResponses`, and `nickname`.

*  [api/routes/analytics.js](#apiroutesanalyticsjs)
  * [/analytics/weeklygrowth](#analyticsweeklygrowth)
  * [/analytics/monthlygrowth](#analyticsmonthlygrowth)
  * [/analytics/monthlygrowthbytag](#analyticsmonthlygrowthbytag)
  * [/analytics/monthlygrowthbylocation](#analyticsmonthlygrowthbylocation)
  * [/analytics/groupsbytag](#analyticsgroupsbytag)
  * [/analytics/cities](#analyticscities)
  * [/analytics/countries](#analyticscountries)

##### api/routes/analytics.js

The `analytics.js` file contains a set of REST API call definitions and specifications that query the Neo4j graph database with a set of parameters that return analytical results as JSON objects. See below for a set of available endpoints and notes about their return results.

![Neo4j Swagger REST API calls for Analytics routes.](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/Swagger_UI_analytics.png "Analytics REST API calls.")

The analytics API calls are as follows:

* [/analytics/weeklygrowth](#analyticsweeklygrowth)
* [/analytics/monthlygrowth](#analyticsmonthlygrowth)
* [/analytics/monthlygrowthbytag](#analyticsmonthlygrowthbytag)
* [/analytics/monthlygrowthbylocation](#analyticsmonthlygrowthbylocation)
* [/analytics/groupsbytag](#analyticsgroupsbytag)
* [/analytics/cities](#analyticscities)
* [/analytics/countries](#analyticscountries)

##### /analytics/weeklygrowth

![/analytics/weeklygrowth](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-weekly-growth.png "/analytics/weeklygrowth")

Returns a set of data points containing the week of the year, the meetup group name, and membership count.

```javascript
'spec': {
    "description": "Get weekly growth percent of meetup groups as a time series.",
    "path": "/analytics/weeklygrowth",
    "notes": "Returns a set of data points containing the week of the year, the meetup group name, and membership count.",
    "summary": "Get the time series that models the growth percent of meetup groups week over week.",
    "method": "GET",
    "params": [
        param.query("startDate", "A date to retrieve results from. Results will be returned for the entire week that the start date occurs within.", "string", true, true),
        param.query("endDate", "A date to retrieve results until. Results will be returned for the entire week that the start date occurs within.", "string", true, true),
        param.query("city", "The city name where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("topics", "A list of topics that a meetup group must have to be returned in the result set. Multiple topic names should be delimited by a comma.", "string", true, true),
        param.query("groups", "A list of names to match on meetup groups, only groups with the name that are specified in the list are returned. Multiple topic names should be delimited by a comma. Leave blank to ignore this field.", "string", false, false)
    ],
    "responseClass": "List[Analytics]",
    "errorResponses": [],
    "nickname": "getWeeklyGrowthPercent"
}
```

##### /analytics/monthlygrowth

![/analytics/monthlygrowth](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-monthly-growth.png "/analytics/monthlygrowth")

Returns a set of data points containing the month of the year, the meetup group name, and membership count.

```javascript
'spec': {
    "description": "Get monthly growth percent of meetup groups as a time series.",
    "path": "/analytics/monthlygrowth",
    "notes": "Returns a set of data points containing the month of the year, the meetup group name, and membership count.",
    "summary": "Get the time series that models the growth percent of meetup groups month over month.",
    "method": "GET",
    "params": [
        param.query("startDate", "A date to retrieve results from. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("endDate", "A date to retrieve results until. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("city", "The city name where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("country", "The country code where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("topics", "A list of topics that a meetup group must have to be returned in the result set. Multiple topic names should be delimited by a comma.", "string", true, true),
        param.query("groups", "A list of names to match on meetup groups, only groups with the name that are specified in the list are returned. Multiple topic names should be delimited by a comma. Leave blank to ignore this field.", "string", false, false)
    ],
    "responseClass": "List[Analytics]",
    "errorResponses": [],
    "nickname": "getMonthlyGrowthPercent"
}
```

##### /analytics/monthlygrowthbytag

![/analytics/monthlygrowthbytag](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-monthly-growth-by-tag.png "/analytics/monthlygrowthbytag")

Returns a set of data points containing the month of the year, the meetup group tag name, and membership count.

```javascript
'spec': {
    "description": "Get monthly growth percent of meetup group locations and tags as a time series.",
    "path": "/analytics/monthlygrowthbylocation",
    "notes": "Returns a set of data points containing the month of the year, the meetup group tag name, the city, and membership count.",
    "summary": "Get the time series that models the growth percent of meetup group tags month over month, by city.",
    "method": "GET",
    "params": [
        param.query("startDate", "A date to retrieve results from. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("endDate", "A date to retrieve results until. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("city", "The city name where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("country", "The country code where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("topics", "A list of topics that a meetup group must have to be returned in the result set. Multiple topic names should be delimited by a comma.", "string", true, true),
        param.query("groups", "A list of names to match on meetup groups, only groups with the name that are specified in the list are returned. Multiple topic names should be delimited by a comma. Leave blank to ignore this field.", "string", false, false)
    ],
    "responseClass": "List[Analytics]",
    "errorResponses": [],
    "nickname": "getMonthlyGrowthPercentByTag"
}
```

##### /analytics/monthlygrowthbylocation

![/analytics/monthlygrowthbylocation](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-monthly-growth-by-location.png "/analytics/monthlygrowthbylocation")

Returns a set of data points containing the month of the year, the meetup group tag name, the city, and membership count.

```javascript
'spec': {
    "description": "Get monthly growth percent of meetup group locations and tags as a time series.",
    "path": "/analytics/monthlygrowthbylocation",
    "notes": "Returns a set of data points containing the month of the year, the meetup group tag name, the city, and membership count.",
    "summary": "Get the time series that models the growth percent of meetup group tags month over month, by city.",
    "method": "GET",
    "params": [
        param.query("startDate", "A date to retrieve results from. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("endDate", "A date to retrieve results until. Results will be returned for the entire month that the start date occurs within.", "string", true, true),
        param.query("city", "The city name where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("country", "The country code where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("topics", "A list of topics that a meetup group must have to be returned in the result set. Multiple topic names should be delimited by a comma.", "string", true, true),
        param.query("groups", "A list of names to match on meetup groups, only groups with the name that are specified in the list are returned. Multiple topic names should be delimited by a comma. Leave blank to ignore this field.", "string", false, false)
    ],
    "responseClass": "List[Analytics]",
    "errorResponses": [],
    "nickname": "getMonthlyGrowthPercentByTag"
}
```

##### /analytics/groupsbytag

![/analytics/groupsbytag](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-groups-by-tag.png "/analytics/groupsbytag")

Returns a list of tags and the number of groups per tag.

```javascript
'spec': {
  "description": "Get a count of groups by tag.",
    "path": "/analytics/groupsbytag",
    "notes": "Returns a list of tags and the number of groups per tag.",
    "summary": "Gets list of tags and the number of groups per tag.",
    "method": "GET",
    "params": [
        param.query("tags", "A list of tags that a meetup group must have to be returned in the result set. Multiple tag names should be delimited by a comma.", "string", true, true),
        param.query("city", "The city name where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true),
        param.query("country", "The country code where a meetup group resides. This field is case sensitive. Leave blank to query on world-wide meetup groups.", "string", false, true)
    ],
    "responseClass": "List[Tag]",
    "errorResponses": [],
    "nickname": "getGroupCountByTag"
}
```

##### /analytics/cities

![/analytics/cities](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-cities.png "/analytics/cities")

Returns a distinct list of cities for typeahead.

```javascript
'spec': {
    "description": "Get a list of cities that meetup groups reside in.",
    "path": "/analytics/cities",
    "notes": "Returns a distinct list of cities for typeahead.",
    "summary": "Gets a distinct list of cities that a meetup group resides in.",
    "method": "GET",
    "params": [],
    "responseClass": "List[City]",
    "errorResponses": [],
    "nickname": "getCities"
}
```

##### /analytics/countries

![/analytics/countries](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/swagger-ui-analytics-get-countries.png "/analytics/countries")

Returns a distinct list of countries for typeahead.

```javascript
'spec': {
    "description": "Get a list of cities that meetup groups reside in.",
    "path": "/analytics/cities",
    "notes": "Returns a distinct list of cities for typeahead.",
    "summary": "Gets a distinct list of cities that a meetup group resides in.",
    "method": "GET",
    "params": [],
    "responseClass": "List[City]",
    "errorResponses": [],
    "nickname": "getCities"
}
```

# Dashboard

This section covers the graph-based analytics web dashboard. The dashboard is a web application that uses client-side JavaScript to communicate with the Neo4j Swagger [REST API](#rest-api) to populate a series of interactive chart controls with data. This web application uses bootstrap for the front-end styles and highcharts.js for the charting controls.

* [Demo](#demo-1)
* [Setup](#setup-1)
* [Dependencies](#dependencies-1)
* [Project Files](#project-files-1)

### Demo

A live online demo for the analytics dashboard is located here: [http://meetup-analytics-dashboard.herokuapp.com/](http://meetup-analytics-dashboard.herokuapp.com/)

![Analytics Dashboard Demo](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/dashboard-demo.png "Analytics Dashboard Demo")

### Setup

From the terminal, go to the `dashboard` directory of the project and run `npm install`, after `node_modules` are installed, run `node app`. The analytics dashboard will be started at `http://localhost:5000`

### Dependencies

Below are the Node.js module dependencies for the analytics web dashboard component.

```javascript
"dependencies": {
  "express": ">= 4.0.x"
}
```

### Project Files

This section documents the directory structure of the web dashboard module of this project, example usage, and outline of client-based JavaScript used to assemble report data and bind to the [REST API](#rest-api). 

* [dashboard/app.js](#dashboardappjs)
* [dashboard/dist/index.html](#dashboarddistindexhtml)
* [dashboard/dist/css/dashboard.css](#dashboarddistcssdashboardcss)
* [dashboard/dist/js/main.js](#dashboarddistjsmainjs)

#### dashboard/app.js

The `dashboard/app.js` file is the entry-point to starting the web dashboard.

#### dashboard/dist/index.html

The `dashboard/dist/index.html` is the index HTML file for the web dashboard. This file containts the HTML bootstrap markup for charting components and reporting components that are driven by data bindings to the [REST API](#rest-api) endpoints. The JavaScript that wires up and initializes the components of this page can be found in the [main.js](dashboard/dist/js/main.js) file.

#### Charts

Below is a list of charting controls and their associated report for the web dashboard.

* Line chart
  * [Meetup Tag Growth %](#meetup-tag-growth-)
* Bar chart
  * [Cumulative Meetup Growth](#cumulative-meetup-growth)
* Donut Chart
  * [Category Growth %](#category-growth-)
  * [Groups By Tag](#groups-by-tag)

##### Meetup Tag Growth %

![Meetup Tag Growth %](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/dashboard-line-chart-tag-growth.png "Meetup Tag Growth %")

This chart plots a line chart of the time series for a meetup group topic on Meetup.com. Each group on Meetup.com has a set of topics associated with it. This chart is meant to show the percent growth month over month. 

##### Cumulative Meetup Growth

![Cumulative Meetup Tag Growth](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/dashboard-bar-chart-cumulative-tag-growth.png "Cumulative Meetup Tag Growth")

This chart plots a bar chart of the cumulative growth of a meetup group topic on Meetup.com. Using the time series data of monthly growth from the [Meetup Tag Growth %](#meetup-tag-growth-) chart, the growth percents over the period are aggregated into a sum for each topic. This chart shows total growth percentage over the period.

##### Category Growth %

![Relative Tag Growth %](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/dashboard-donut-chart-relative-tag-growth.png "Relative Tag Growth %")

This chart plots an Donut Chart of the relative cumulative growth of a meetup group topic on Meetup.com. Using the data from [Cumulative Meetup Growth](#cumulative-meetup-growth), the percentage growth of each topic over the period is compared relative to one another as a ratio of 100.

##### Group Count By Tag

![Groups By Tag](https://raw.githubusercontent.com/kbastani/meetup-analytics/master/docs/images/dashboard-donut-chart-groups-by-tag.png "Groups By Tag")

This chart plots an Donut Chart of the number of groups in the region during the period for each topic. Each group is compared relative to one another as a ratio of 100.

#### dashboard/dist/css/dashboard.css

This file contains the main style rules that override and extend the bootstrap style rules for elements of the [dashboard/dist/index.html](#dashboarddistindexhtml) HTML file.

#### dashboard/dist/js/main.js

This file contains the main JavaScript code that initializes the client-side logic and events on the [dashboard/dist/index.html](#dashboarddistindexhtml) HTML file.

```javascript
var apiUrl = "http://localhost:3000/";
```

Replace `http://localhost:3000/` with the URL to your hosted analytics [REST API](#rest-api) when deploying to production.

# Neo4j

This section covers storage options and deployment with Neo4j.

**What is Neo4j?**

[Neo4j](http://www.neo4j.org/) is a robust (fully ACID) and open-source graph database. A graph database is a database that uses graph structures with nodes, edges, and properties to represent and store data. A graph database is any storage system that provides index-free adjacency. Neo4j uses a declarative graph query language with SQL-like syntax called Cypher.

**What is Cypher?**

[Cypher](http://www.neo4j.org/learn/cypher) is a declarative graph query language for the graph database, Neo4j that allows for expressive and efficient querying and updating of the graph store. Cypher is a relatively simple but still very powerful language. Very complicated database queries can easily be expressed through Cypher. This allows you to focus on your domain instead of getting lost in database access.

*Cypher Examples:*

```cypher
// Create a record for yourself
CREATE (you {name:"You", likes:"Neo4j" })
RETURN you
```

![Create a record for yourself](http://neo4j.com/wp-content/themes/neo4jzurb/assets/images/home-you.png "Create a node for yourself.")

```cypher
// Find your friends
MATCH (you {name:"You"})-[:FRIEND]->(yourFriends)
RETURN you, yourFriends
```

![Find your friends](http://neo4j.com/wp-content/themes/neo4jzurb/assets/images/home-friends.png "Find your friends.")

```cypher
// Find someone who can help you learn Neo4j
MATCH (you {name:"You"}), (expert {knows:"Neo4j"}),
  p = shortestPath( (you)-[*..5]-(expert) )
RETURN p
```

![Find someone who can help you learn Neo4j](http://neo4j.com/wp-content/themes/neo4jzurb/assets/images/home-shortest.png "Find someone who can help you learn Neo4j.")

### Neo4j Optional Schema

Running the following Cypher query will produce a result that shows how things in the database are related and how.

```cypher
// What is related, and how
MATCH (a)-[r]->(b)
WHERE labels(a) <> [] AND labels(b) <> []
RETURN DISTINCT head(labels(a)) AS This, type(r) as To, head(labels(b)) AS That
LIMIT 10
```

| This        | To           | That  |
| ------------- |:-------------:| -----:|
Stats|  ON_DAY| Day|
Day|  NEXT| Day|
Week| HAS_DAY|  Day|
Group|  LOCATED_IN| Location|
Group|  HAS_MEMBERS|  Stats|
Group|  HAS_TAG|  Tag|
Month|  HAS_DAY|  Day|
Year| HAS_MONTH|  Month|

### Neo4j Graph Data Model

The image below shows an example meta-model for the graph-based analytics Neo4j database.

![Neo4j Graph Data Model](https://raw.github.com/kbastani/meetup-analytics/master/docs/images/meetup-analytics-graph-gist.png "Neo4j Graph Data Model")

# Data Import Scheduler

This section covers scheduled data import services from an external REST API. 

* [Setup](#setup-2)
* [Dependencies](#dependencies-2)
* [Project Files](#project-files-2)

## Setup

From the terminal, go to the `scheduler` directory of the project and run `npm install`, after `node_modules` are installed, run `node app`. The Meetup group import scheduler will be started at `http://localhost:3001`. It will run once a day 5 minutes after midnight PST.

## Dependencies

```javascript
"dependencies": {
  "async": "^0.7.0",
  "cron": "^1.0.4",
  "express": "^4.0.0",
  "hat": "0.0.3",
  "meetup-api": "^0.1.3",
  "neo4j": "^1.1.0",
  "random-name": "^0.1.0",
  "underscore": "^1.6.0"
}
```

## Project Files

This section documents the directory structure of the data import scheduler component of the graph-based analytics platform. The purpose of this component is to make routine calls to the Meetup.com API with a set of criteria.

* [scheduler/app.js](#schedulerappjs)
* [scheduler/import/importer.js](#schedulerimportimporterjs)

### scheduler/app.js

The `scheduler/app.js` file is the entry-point to the data import scheduler component.

#### getPollingCities

The `getPollingCities` function retrieves a list of locations that will be used to import data from. The Meetup.com API is location and tag based for finding groups. The example function below will track meetup groups in `New York`, `Boston`, `Atlanta`, `Seattle`, `Los Angeles`, and `San Francisco`.

```javascript
function getPollingCities() {
    var cities = [{
        city: "New York",
        state: "NY",
        country: "US"
    }, {
        city: "Boston",
        state: "MA",
        country: "US"
    }, {
        city: "Atlanta",
        state: "GA",
        country: "US"
    }, {
        city: "Seattle",
        state: "WA",
        country: "US"
    }, {
        city: "Los Angeles",
        state: "CA",
        country: "US"
    }, {
        city: "San Francisco",
        state: "CA",
        country: "US"
    }];

    return cities;
}
```

#### iteratorCityCallback

From the list of cities provided from the `getPollingCities` function, the `iteratorCityCallback` function will iterate through each location and request groups from the Meetup.com API with the following criteria.

```javascript
{
  'topic': 'NoSQL',
  'country': city.country,
  'city': city.city,
  'state': city.state,
  'page': '100'
}
```

The `topic` property specifies that only Meetup groups with a topic listed on their profile will be imported, in this case groups with the topic `NoSQL`. Since Meetup groups can have multiple topics, many topics will be imported related to `NoSQL`.

The `iteratorCityCallback` function is below. 

```javascript
function iteratorCityCallback(count, length, cities) {
    if (count < length) {
        // Get the city for this iteration
        var city = cities[count];

        // Import group stats for day
        meetup.getGroups({
            'topic': 'NoSQL',
            'country': city.country,
            'city': city.city,
            'state': city.state,
            'page': '100'
        }, function (err, groups) {
            iteratorGroupCallback(0, groups.results.length, groups.results, count, length, cities);
        });
    }
    else
    {
      console.log("Import complete");
      process.exit();
    }
}
```

This is the main loop of the data import process from Meetup.com's API. Since Node.js is event-driven, callbacks are used to indicate the completion of a function. Due to this, an iterator is used to synchronously loop through import processes. This sequential import is required for the import process so that async writes to Neo4j do not cause a deadlock exception to be thrown.

You can read more about Neo4j deadlock exceptions here: [http://docs.neo4j.org/chunked/milestone/transactions-deadlocks.html](http://docs.neo4j.org/chunked/milestone/transactions-deadlocks.html)

#### getGroupImportParameters

The `getGroupImportParameters` constructs a JSON object from a group that is retrieved from the Meetup.com API. This object is constructed to be used as a parameter for Cypher transactions used to write group data to Neo4j. An example object is demonstrated below.

```javascript
var csvLine = {
    group_name: group.name,
    group_creation_date: group.created,
    group_creation_date_year: createdDate.year,
    group_creation_date_month: createdDate.month,
    group_creation_date_day: createdDate.day,
    group_location: group.city,
    group_country: group.country,
    group_state: group.state,
    group_tag: group.topics.map(function (d) {
        return d.name;
    }),
    last_month: yesterday.getMonth() + 1,
    last_day: yesterday.getDate(),
    last_year: yesterday.getFullYear(),
    group_stats: group.members,
    month: today.getMonth() + 1,
    day: today.getDate(),
    year: today.getFullYear(),
    day_timestamp: getTicks(today),
    day_of_week: today.getDay() + 1,
    week_of_year: getWeekOfYear(today)
};
```

### scheduler/import/importer.js

The `scheduler/import/importer.js` file defines Neo4j Cypher queries that take in a set of parameters which are produced from requests to the Meetup.com API for each group returned for a specific location.

```javascript
var _importGroupStats = function (params, options, callback) {

  var query = [
      'MERGE (group:Group { name: { csvLine }.group_name })',
      'ON CREATE SET group.created = toInt({ csvLine }.group_creation_date)',
      'ON CREATE SET group.year = toInt({ csvLine }.group_creation_date_year)',
      'ON CREATE SET group.month = toInt({ csvLine }.group_creation_date_month)',
      'ON CREATE SET group.day = toInt({ csvLine }.group_creation_date_day)',
      'MERGE (location:Location { city: { csvLine }.group_location, country: { csvLine }.group_country, state: { csvLine }.group_state })',
      'FOREACH (name in { csvLine }.group_tag |',
      '      MERGE (tag:Tag { tag: name }))',
      'MERGE (stats:Stats { group_name: { csvLine }.group_name, month: toInt({ csvLine }.last_month), day: toInt({ csvLine }.last_day), year: toInt({ csvLine }.last_year) })',
      'ON CREATE SET stats.count = toInt({ csvLine }.group_stats)',
      'MERGE (day:Day { month: toInt({ csvLine }.month), day: toInt({ csvLine }.day), year: toInt({ csvLine }.year) })',
      'ON CREATE SET day.timestamp = toInt({ csvLine }.day_timestamp)',
      'ON CREATE SET day.dayofweek = toInt({ csvLine }.day_of_week)',
      'MERGE (week:Week { year: toInt({ csvLine }.year), week: toInt({ csvLine }.week_of_year) })',
      'MERGE (week)-[:HAS_DAY]->(day)',
      'MERGE (month:Month { year: toInt({ csvLine }.year), month: toInt({ csvLine }.month) })',
      'MERGE (month)-[:HAS_DAY]->(day)',
      'MERGE (year:Year { year: toInt({ csvLine }.year) })',
      'MERGE (year)-[:HAS_MONTH]->(month)',
      'MERGE (lastDay:Day { month: toInt({ csvLine }.last_month), day: toInt({ csvLine }.last_day), year: toInt({ csvLine }.last_year) })',
      'MERGE (group)-[:LOCATED_IN]->(location)',
      'MERGE (group)-[:HAS_MEMBERS]->(stats)',
      'MERGE (stats)-[:ON_DAY]->(day)',
      'MERGE (lastDay)-[:NEXT]->(day)',
      'RETURN group.name as name'].join('\n');

  callback(null, query, params);
};
```

## Heroku Scheduler

In order to run the data import scheduler on Heroku, you'll need to use the Heroku Scheduler add-on. [Scheduler](https://addons.heroku.com/marketplace/scheduler) is an add-on for running jobs on your app at scheduled time intervals, much like cron in a traditional server environment.

To add the scheduler to your Heroku project, run the following command.

```console
$ heroku addons:add scheduler --app [app-name]
```

After Scheduler is added to your Heroku application, manage jobs in the [Scheduler dashboard](https://scheduler.heroku.com/dashboard). Add a job that runs once a day with the command `node app`. Once the data import process is completed each day, the application will automatically terminate its process so it can be called again.
