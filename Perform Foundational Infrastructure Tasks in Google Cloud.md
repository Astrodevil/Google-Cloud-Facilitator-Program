**Task 1: Create a bucket**


1.	In the console, click the Navigation menu > Storage.
2.	Click Create a bucket.


**Task 2: Create a Pub/Sub topic**


1.	In the console, click the Navigation menu > Pub/Sub > Topics.
2.	Click Create a topic.
* Make sure you remember the topic name, which will be used in Task 3.


**Task 3: Create the Cloud Function**


1.	In the console, click the Navigation menu > Cloud Functions.
2.	Click Create function.
3.	In the Create function dialog, enter the following values:
Field	               Value
Function to execute	 thumbnail
Runtime	             Node.js 8
Trigger	             Cloud Storage

**Task 4.	Copy the given index.js and package.json to the dialog.**

Make sure you replace the text REPLACE_WITH_YOUR_TOPIC with the topic you created in task 2, in line 15 of index.js.


**Task 5.	Upload a JPG or PNG image file to the bucket created in Task 1.**


Task 4: Remove the previous cloud engineer


1.	In the console, click the Navigation menu > I AM.
2.	Find the second user.
3.	Click the pencil icon, select Delete.



*Congratulations! You completed this challenge lab*

