# IST769 Spring 2023 Final Exam

## Front Matter 

### Important Disclaimer

**This is an open book EXAM. The intent is to measure your knowledge of the course material. You are expected to work on this on your own. The exam period begins when this document is released.**

**Allowed During the Exam Period:**

- All Resources from this class.
- Content from the internet (pages, videos, posted stack overflow, questions, etc., generally anything you can find with a web search).
- Whatever we discuss in class on Monday.
- Asking the instructor for clarification on the Teams Channel ONLY (so everyone sees it).

**NOT Allowed During the Exam Period:**

- Bouncing ideas off your classmates, or collaborating on approaches. 
- Asking another human for clarification, advice, interpretation, or suggestions whether in person or online, whether synchronously or asynchronously.
- Solutions that use code we didn't learn in the class. Stick to Pyspark SQL, Pyspark Dataframes API, and SQL in Drill. No spark RDD or regular Python.
- Use of test aid websites like Chegg, Coursehero, etc. These won't be helpful, and they are evil anyways.

### Getting Started

Is highly recommended that you use vLab to complete this exam. If you don't you're on your own regarding getting this working on your own computer.

1. Check if there are containers running: `PS> docker ps`
2. Stop any running containers: `PS> docker ps -q | % { docker stop $_ }`
2. Clone this repository: `git clone https://github.com/mafudge/ist769sp23final`
3. Change into the repository directory: `cd ist769sp23final`
4. Build the dataloader container: `docker-compose build`
4. Bring up the docker environment: `docker-compose up -d`
5. The data loader service `dataloader` will import data into the databases to begin. To check on its status: `docker-compose logs dataloader`

## The iSchool Data Scientist

The iSchool has hired you as a data scientist to help them analyze the data from their student enrollment system. You have been asked to make sense of the relationships hidden within the data. Specifically you have been asked to analyze the ADS (Applied Data Science) and IS (Information Systems) programs. You have been provided with the following data sources:

- Course, program and student data can be found in the MongoDb database `ischooldb` under the `courses`, `terms`, `programs` and `students` collections respectively.
- The Student enrollment data was extracted to Minio, and can be found in the `enrollments` bucket. This consists of `sections.csv` and `enrollments.csv` files.

I suggest getting familiar with the data prior to attempting to solve the problems. Do this by querying the data using mongodb, Spark or Drill to get a sense of the layout and properties of the data. This is considered part of the exam and necessary to complete the challenges. If you would like to see the data structure in a file, you can look in the `dataloader/app/data` folder.

## Exam

**DO NOT USE CHAT-GPT OR OTHER GENERATIVE AI'S, PLEASE JUST STICK TO THE CLASS LESSONS**

### Instructions

Complete all answers in the `Exam.ipynb` file in the `work` folder! You will turn this file in to Blackboard in addition to your official exam document submission.

Each question is worth 5 points. 

For the highest possible marks, for each question, include the following in the submission document:
1. The TEXT of the code you wrote (this will be in your Exam.ipynb file)
2. A CLEAR screenshot of your code with your netid in the screenshot. (only screenshot the region, not the entire window!)
3. A CLEAR screenshot of the output of your code with your netid in the screenshot. (only screenshot the region, not the entire window!)
4. If you know your answer is NOT correct, explain what you tried/omitted/did not get correct, by adding comments to your code/commenting out code that does not run. This should appear in your text and screenshot.

### Advice for the Best Grade

- Review how to connect to `mongodb`, `minio`, `cassandra`, '`elasticsearch`, `kibana` and `neo4j` from `Spark`. Figure this out BEFORE you start the exam. 
- When you answer the questions, PySpark and use the DataFrames API. You should NOT need to use RDD or fall back to PoP, unless explicitly asked to do so. While you CAN solve these problems using both of those approaches, that is not what was taught in class. 
- There are many ways to solve these problems. If you get stuck, try a different approach. I expect everyone will have slightly different solutions.
- Your output needs to show the code solves the problem clearly and trivially. For example, if the question asks for customers who have not placed an order the query output should show that.
- Use the `Exam.ipynb` file in the `work` folder to write your code! And Clearly label your code with comments and markdown cells. You will turn in this file along with your exam submission document.
- If you cannot figure out the answer to the question, I suggest writing simpler code and using that as your answer. This way you can complete the next question in the exam. It is better to have running code that is incorrect than code that will not run.
- Questions you think are correct, but are not will be graded more harshly than questions which you know are incorrect.
- Every answer should be able to be executed multiple times without error, and with the same results.  If you load data, make sure the same data loads each time without adding extra records.

### Exam Questions

Unless you are explicitly instructed otherwise, answer each of the following using PySpark / Spark SQL. For any queries you write make sure to include a `printSchema()` and a sample of the output which  demonstrates the code is correct clearly and trivially.

1. Create a spark session that is configured to connect to `mongodb`, `minio`, `cassandra`, '`elasticsearch` and `neo4j`.
2. Demonstrate you can read the process-oriented data `enrollments` and `sections` from `minio` using PySpark. 
3. Demonstrate you can read the reference-oriented data `terms`, `students`, `courses`, and `program` reference data from `MongoDb` using PySpark. 
4. Prepare the `section` data for loading into `cassandra` and `elasticsearch` with Spark or Spark SQL. Just PREPARE it do not LOAD it. Remember that we want this data to be as wide as possible, so include all relevant reference data. For example, the `section` data should include `term` attributes like `year`,  `academic year`, etc... and from `course`, attributes like `credits`, `name`, `prerequisites`, etc... 
5. Use the `cassandra-driver` example from class to write python code to connect to cassandra from within Jupyter and create a keyspace named `ischooldb`. Design a cassandra table called `sections` to store the data from question 4. Appropriate key design is important! Please explain your justification for key below your table definition. Provide clear evidence that your table was created by querying the empty table in spark and use `printSchema()` to show the schema. 
6. Load the data frame you created in question 4 into the `cassandra` table you created in question 5. Demonstrate the data is in the table by querying back it with PySpark. Make sure you can run the code multiple times and each time it replaces the existing data.
7. Since we did not learn how to create a custom elasticsearch mapping, before you can load the data into `elasticsearch` you will need to flatten the nested data. For example, `elective_in_programs` should generate 2 columns `course_is_elective_for_IS` and `course_is_elective_for_DS`. You'll need to repeat this step for `required_in_programs`. Omit the `course_prerequisites` and `course_key_assignments` column. 
8. Load the data frame you created in question 7 into `elasticsearch`, under the index `sections`.  Demonstrate the data is in the index by querying back it with PySpark. 
9. Similar to question 4, prepare the `enrollments` for loading into `cassandra` and `elasticsearch` with Spark or Spark SQL. For this wide table we want to include the same reference data for `sections` but include the `student` attributes and the `program` data associated with the student. 
10. Load the data frame you created in question 8 into `elasticsearch`, under the index `enrollments`. This time, just Omit all array types to make the problem simpler (`elective_courses`, `key_assignments`, `course_prerequisites`, etc...)
11. Write spark to clear the `neo4j` database of all nodes and relationships.
12. Load the `courses` and `program` data into `neo4j` as nodes. Exclude the `requirements`, `electives` and `prerequisites` from the node attributes. Demonstrate the data in `neo4j` by querying back it using one or more Cypher queries. NOTE: the Neo4J `name` attribute is what will display on the node bubbles.
13. Load the `requirements` and `electives` data into `neo4j` as relationships to the nodes you created in step 12. Use the `program` data to form the `required` and `elective` course relationships. Demonstrate the relationships in `neo4j` are present by querying back it using a Cypher query to display the relationships.
14. Load the `prerequisites` into `neo4j` as relationships to the `course` nodes you created in step 12. Demonstrate the relationships in `neo4j` are present by querying back it using a Cypher queries to display the relationships.
15. Write a Cypher query to display courses which are required in both the `IS` and `DS` programs.
16. Write a Cypher query to retrieve the `course code`, `course title`, and the count of programs the course is a requirement in. Write as a Cypher query but retrieve the  output as a Spark DataFrame. 
17. Create an `enrollments` index pattern in `kibana` from the `enrollments` index in `elasticsearch`. Demonstrate it works by querying the enrollments enrollments for student `Aiden Knowone`.
18. Create a `sections` index pattern in `kibana` from the `sections` index in `elasticsearch`. Add a dervied column to the `sections` index pattern to calculate the ratio of `enrollment/capacity`.  Demonstrate it works by querying the `IST659` `M001` sections.
19. Build a `kibana` student dashboard, from the `enrollments` index pattern. When a student is selected by name, show their program and gpa on a card(s). Provide a list of courses taken and include course name and letter grade. Create a pie chart that demonstrates how many credits the student has completed in the program.
20. Build a `kibana` course dashboard, from the `sections` index pattern. Allow for the selection of the `academic year` then display three relevant visualization of your choosing. For example, this could be: a summary of courses for that term, bar charts of enrollment to capacity for each course, counts of courses required in program vs electives, etc...


### Turning it in

- Complete the `E2-Submission-Form.docx`.
- Include your `Exam.ipynb` with completed questions. 
- Make sure your name and email are in BOTH files where you are prompted to add them.
- turn both files into blackboard prior to the deadline. 
