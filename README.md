# API overview:
The general idea behind this api, mgmt-590-rest-api, is to have an interactive question-answering api which can provide answers using a given model by accepting input questions and context. Moreover, extra functionality has been added to allow users to add, view and delete the models in the database. The users can use any of these models to get the answers. 

cloud base url of api mgmt.-590-rest-api: https://mgmt-590-rest-api-nxhbjedikq-uc.a.run.app/

# API Routes:
## 1.	Route 1: “/models”
This route can accept methods ‘GET’,’PUT’ and ‘DELETE’ methods.

### a.	GET /models:
This route allows a user to obtain a list of the models currently loaded into the server and available for inference.

Sample request url: https://mgmt-590-rest-api-nxhbjedikq-uc.a.run.app/models

Sample request body: NA

Sample response:
[ { "name": "distilled-bert", 
"tokenizer": "distilbert-base-uncased-distilled-squad", 
"model": "distilbert-base-uncased-distilled-squad" }, 
{ "name": "deepset-roberta", 
"tokenizer": "deepset/roberta-base-squad2", 
"model": "deepset/roberta-base-squad2" }, 
{ "name": "bert-tiny", 
"tokenizer": "mrm8488/bert-tiny-5-finetuned-squadv2", 
"model": "mrm8488/bert-tiny-5-finetuned-squadv2" } ]


### b.	PUT /models:
This route allows a user to add a new model into the server and make it available for inference. The newly added model will be inserted into database table “models”.

Sample request url: https://mgmt-590-rest-api-nxhbjedikq-uc.a.run.app/models

Sample request body:
{ "name": "bert-tiny", 
"tokenizer": "mrm8488/bert-tiny-5-finetuned-squadv2", 
"model": "mrm8488/bert-tiny-5-finetuned-squadv2" } 


Sample response body:
[ { "name": "distilled-bert",
 "tokenizer": "distilbert-base-uncased-distilled-squad", 
"model": "distilbert-base-uncased-distilled-squad" }, 
{ "name": "deepset-roberta", 
"tokenizer": "deepset/roberta-base-squad2", 
"model": "deepset/roberta-base-squad2" },
 { "name": "bert-tiny", 
"tokenizer": "mrm8488/bert-tiny-5-finetuned-squadv2", 
"model": "mrm8488/bert-tiny-5-finetuned-squadv2" } ]

### c.	DELETE /models?model=\<model name\>:
This route allows a user to delete an existing model on the server such that it is no longer available for inference. The model record will be deleted from the database table “models”.


Sample request url: https://mgmt-590-rest-api-nxhbjedikq-uc.a.run.app/models?model=bert-base 

Sample request body: NA

Sample response:
[ { "name": "distilled-bert", 
"tokenizer": "distilbert-base-uncased-distilled-squad", 
"model": "distilbert-base-uncased-distilled-squad" }, 
{ "name": "deepset-roberta", 
"tokenizer": "deepset/roberta-base-squad2", 
"model": "deepset/roberta-base-squad2" } ]

## 2.	Route 2: “/answer”

This route can accept POST and GET methods.

### a.	POST /answer?model = \<model name\>
This route uses the model passed in the request query parameters to answer a question, given the context provided in the JSON payload. If no model name is passed, a default model will be used to answer the question(in this case distilbert-base-uncased-distilled-squad).Moreover, the details of these executions are logged into database in table "answer_history".

Sample request url: https://mgmt-590-rest-api-nxhbjedikq-uc.a.run.app/answer?model=bert-base

Sample request body:
{
"question": "who did holly matthews play in waterloo rd?",
"context": "She attended the British drama school East 15 in 2005, and left after winning a high-profile role in the BBC drama Waterloo Road, playing the bully Leigh-Ann Galloway.[6] Since that role, Matthews has continued to act in BBC's Doctors, playing Connie Whitfield; in ITV's The Bill playing drug addict Josie Clarke; and she was back in the BBC soap Doctors in 2009, playing Tansy Flack."
}

Sample response:
{
    "answer": "Leigh-Ann Galloway",
    "context": "She attended the British drama school East 15 in 2005, and left after winning a high-profile role in the BBC drama Waterloo Road, playing the bully Leigh-Ann Galloway.[6] Since that role, Matthews has continued to act in BBC's Doctors, playing Connie Whitfield; in ITV's The Bill playing drug addict Josie Clarke; and she was back in the BBC soap Doctors in 2009, playing Tansy Flack.",
    "model": "bert-base",
    "question": "who did holly matthews play in waterloo rd?",
    "timestamp": 1622152488
}


### b.	GET / answer?model=\<model name\>&start=\<start datetime\>&end=\<end datetime\>

This route returns recently answered questions. Database table answer_history is queried to get the history of the requests.

Query Parameters:
 
 ●\<model name\> (optional) - Filters the results by providing a certain model name, such that the results only include answered questions that were answered using the provided model. 

● \<start timestamp\>(required) - The starting timestamp, such that answers to questions prior to this timestamp won't be returned. This should be a Unix timestamp.
 
 ● \<end timestamp\>(required) - The ending timestamp, such that answers to questions after this timestamp won't be returned. This should be a Unix timestamp.


Sample request url: http://10.242.251.153:8080/answer?start=1522081879&end=1722081879

Sample request body: NA

Sample response:
[ { "timestamp": 1621602784, 
"model": "deepset-roberta", 
"answer": "Leigh-Ann Galloway", 
"question": "who did holly matthews play in waterloo rd?", 
"context": "She attended the British drama school East 15 in 2005, and left after winning a high-profile role in the BBC drama Waterloo Road, playing the bully Leigh-Ann Galloway.[6] Since that role, Matthews has continued to act in BBC's Doctors, playing Connie Whitfield; in ITV's The Bill playing drug addict Josie Clarke; and she was back in the BBC soap Doctors in 2009, playing Tansy Flack." }, 
{ "timestamp": 1621602930, 
"model": "distilled-bert", 
"answer": "Travis Pastrana", 
"question": "who did the first double backflip on a dirt bike?", 
"context": "2006 brought footage of Travis Pastrana completing a double backflip on an uphill/sand setup on his popular /"Nitro Circus/" Freestyle Motocross movies. On August 4, 2006, at X Games 12 in Los Angeles, he became the first rider to land a double backflip in competition. Having landed another trick that many had considered impossible, he vowed never to do it again." } ]

# Dependencies:
The API is using the below libraries
1.	Transformers
2.	Flask
3.	Sqlite3
4.	Urllib
5.	Datetime
6.	Time
7.	Os

# Building and running the Docker image:
1.	A Docker file should be first created with all the dependencies and the requirements.
This file contains info such as dependencies, relative target location of the code and other dependent files in target machine. Moreover, the file can also include the commands that should be executed when the docker is run.

Sample Dockerfile:
$ cat Dockerfile

FROM tensorflow/tensorflow

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY answer.py /app/answer.py

CMD ["python3", "/app/answer.py"]


2.	Build the docker image using the below command.

docker build -t mgmt590 .

This creates the docker image mgmt590 in current working directory.

3.	Once the docker image is created, it can be executed using the super user(sudo) using below command.

Sudo docker run -it -p 8080:8080 mgmt590 python /app/answer.py

