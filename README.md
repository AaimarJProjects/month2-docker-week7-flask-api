# month2-docker-week7-flask-api
## What it does
This is a Python REST API built with Flask, containerized with docker, that contains three endpoints: /health, POST /user, and DELETE /user/uid. The /health endpoint is used to determine if the container is alive before traffic is sent, POST /user endpoint gives each added user a uuid and takes the new user's name, age, sex, and email and adds it to a python dictionary but first checks if that users email already exist, and sends a 409 conflict http status code with a user already exists message to the client to prevent duplicate users, and DELETE /user/uuid endpoint takes a universal unique identifier(uuid) that is given to each user, and uses an if condition to see if it matches with an existing uuid in the python dictionary, and if it does then it deleted the user but if it doesn't match it returns a 404 NOT FOUND http status code with a message telling the client that this user was not found. So why did I use Flask web framework and not just python code with no web framework ? The reason was because without a web framework something has to handle the low level network code, like dealing with incoming bytes, understanding what each request is asking for, determining which function handles the request, and format a response and send it back. So that means I would have write code to do all of that myself every time I create a new project, and web frameworks like Flask solve this by handling all the low level network code for me, and I just code the functions.

## How to build it

### Docker caching explained
When building each layer Docker caches it. So if the image need to be rebuilt docker reuses what it built before from the previous build in the cache, and only rebuilds the layer that a change was made to, and all the other layers below it. This saves time on builds as docker doesn't have to not have to rebuild each layer.

### Docker build steps (How to build it)
```Dockerfile
FROM python
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY app.py .
EXPOSE 5000
CMD ["python", "app.py"]
```

### Explanation of Build Steps
1) The first line 'FROM python' pulls the official python image from docker hub and uses it as the base.
2) Second line 'WORKDIR /app', creates a work directory in the image where all my files are stored.
3) The third line 'COPY requirements.txt .', copies the requirements.txt file which contains all of my packages like Flask and it dependencies like jinja2, Werkzeung, etc.. into . which is the current directory the /app work directory.
4) Fourth line 'pip install -r requirements.txt', installs all the packages during build time.
5) Fifth line "COPY app.py .", does the same thing as the first copy instruction in line 3 but it copies the app.py file which contains my python code for the REST API I built using Flask into the /app work directory.
Originally I had just one copy instruction that copied both requirements.txt and app.py on line 3 but every time I made a change to app.py docker would have to rebuild that layer and install all of my packages again which makes the build process longer, so to prevent that I put app.py after installing the packages. 
6) Sixth line "EXPOSE 5000" which documents port 5000 the port that my container listens on.
7) The last line, line six 'CMD ["python", "app.py"] which runs app.py every time the container starts.

## How to run it
To run this image use 'docker run -d -p 5000:5000 aaimarjprojects/flask-api:v1'. 

### What each flag in the docker run command does
-d runs the container in the background so that it doesn't take up space in my terminal and -p publishes port 5000 on my host so that any traffic that comes to port 5000 on the host is forwarded to port 5000 on the container.

## How to test it
I tested all my endpoints using curl which is a tool that send hhtp requests. 

### Health Check 
curl -i http://<host_ip_address>:5000

Expected: 200 OK and Health message

### Create User
curl -i -X POST -H "Content_Type: application/json", -d '{"name": "<enter_name_here>", "age": <enter_age_here>, "sex":"<enter_sex_here>", "email": "<enter_email_here>"}' http://<host_ip_address>:5000/user 

Expected: 200 OK and User created message

### Delete User
curl -i -X DELETE http://<host_ip_address>:5000/user/<user_id>

Expected: 200 OK and User deleted message

### What each flag does
-i shows the response header and body so I can see if my endpoint is sending the correct response, -X specifies the http method, -H set the hhtp header and tells flask the body is json, and -d is the data that is being sent in the request body,

### What happens when trying to add a user that already exists
Result: 409 CONFLICT and a User already exists message 

### What happens when trying to delete a user that was already deleted
Result: 
404 NOT FOUND and User not found message.
