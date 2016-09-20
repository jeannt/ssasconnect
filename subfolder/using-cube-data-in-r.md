## Using SSAS cube data in R solutions

*This is a very early draft of an article in response to frequent user questions about getting cube data to use in SS R Services

### Basic data warehousing
For the sake of people who are newer to SSAS, let's briefly review the distinction between transactional data, data in a data ware house, and data in an OLAP cube (or tabular model).

**Transactional data**
Relational databases are designed to support applications by securely storing data from individual transactions in such a way that the transactions can be rolled back if there is a problem, audited, or backed up. Transactional data often has very fine-grained timestamped, and is highly normalized -- really not the sort of data to use in an R model without some additional summary and maybe creating a view or two.

**Data warehouses**
In contrast, a data warehouse or data mart (there are differences we won;t go into) captures the big picture across an enterprise or projects by summarizing and filtering transactional data so that managers and analysts can dig intro trends, create reports, and generate metrics that describe business performance. If you are getting data from a database for use in R, you are probably getting it from a data warehouse or other reporting database. 

**Cubes**
A cube is built by using data from a data warehouse -- in other words, data that has already been somewhat organized and summarized. To build the cube, analysts decide what are the most important dimensions to use in analyzing data, and embed those in the cube design. For example, a multinational corporation will typically use a Geography dimension to sort its sales reports into hierarchies such as Region -> Country -> State -> City. A time dimension might have multiple hierarchies, such as one hierarchy for calendar year reporting, and another for fiscal year.
After all the necessary dimensions are created, the developer will also figure out what kind of calculations are most commonly used by the business. For example, a retailer might have several tables of sales tax percentages to apply to different categories of products, in different regions, or tables of currency rate calculations. 
When all of these possible aggregations have been defined, the raw numbers from the data warehouse are crunched to create the cube.

Therefore, the cube by definition is already a set of aggregated numbers. If you use an MDX query to return data using the predefined dimensions and slice them by supported attributes, you can get highly aggregated data without having to write a line of R code.
The flip side is that if you need to compute an aggregate not supported by the cube's design, queries can be slow.

**Tabular models**
A tabular model is like a cube, in that it has to be designed by someone who knows the business, using specialized tools, and is typically based on a data warehouse. However, a tabular model doesn't anticipate all the aggregates you might need and store them on disk for fast access-- instead, it computes aggregates very fast in memory. 

Typically tabular models run on computers that have lots of memory, and lightweight tabular models also run natively in Excel (2013 and later). However, for truly gigantic organization with lots of data, there is no substitute for a well-designed cube. 

For the purposes of getting data into R, there isn't a huge difference between tabular models and cubes. Both run in Analysis Services and require that you have a provider that supports MDX or DAX. Accessing a cube requires that you know some MDX, whereas tabular models support DAX and MDX.

### But what's the point?

From this horribly simplified overview, here are the key points for R developers:

+ Many of the summary functions that you are thinking of applying to your data in R have already been created in the cube. **So don't reinvent the wheel** -- enlist the help of a SQL developer to get some of their most common MDX queries published as a report that you can consume. (More about consuming MDX later.)
+ A transactional database is not a great source of data. Use the data warehouse instead.
+ Don't query raw data. Look for a database view that someone has already set up. If you don't see a useful view, create one, or ask your friendly SQL dev to do it for you. There are a bajillion optimizations that a SQL developer or DBA can perform to make queries run fast.