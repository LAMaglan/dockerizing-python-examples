# General info

Two examples where we are dockerizing python applications,
<br>
following the tutorial from [here](https://www.youtube.com/watch?v=bi0cKgmRuiA&t=7s)

# Example 1: imdb scraper

## Step 0: Have some python script

Have some python script. In this case, we are using
<br>
code to scrape IMDB based on this [repo](https://github.com/python-engineer/python-fun/blob/master/moviepicker/main.py)

## Step 1: Prepare Docker file

<br>
Create a `Dockerfile`, where you have take a base image
<br>
add the python file to a specified location, and install necessary modules
<br>
Then, run any processes that you want, like:

```
CMD [ "python", "./main.py"]
```

## Step 2: Build the docker image

Note: assumes docker is installed on your machine.
<br>
Run:

```
docker build -t <chosen-name-of-image> <path>
```

<br>
which builds the image in sequential order of lines
<br>
in the dockerfile (except for the CMD line)
<br>

## Step 3: Run (simple) docker image

Do:
<br>

```
docker run <name-of-image-from-previous-step>
```

Which will run the CMD line from the dockerfile
<br>
If the main process finishes, then the container will shut down

## Step 4: Run docker image with options

If the process takes user input, have to run in a different way:
<br>

```
docker run -t -i <name-of-image>
```

i = interactive mode
<br>
t = gives us sudo terminal

# Example 2: fastapi app

## Step 1: create virtual image and install dependencies

Using venv in this tutorial:

```python
python3 -m venv venv # create venv
. venv/bin/activate #activate venv
```

install dependencies:

```python
pip install fastapi
pip install uvicorn #the "webserver"
```

## Step 2: create the fastapi-app

Make a subdirectory. Tutorial code taken from officla FastAPI example
<br>
(similar to flask syntax)

## Step 3: test app locally

Two ways.
<br>

From commandline

```
uvicorn main:app --reload
```

put at end of FastAPI python script

```
uvicorn.run(app)
```

If latter, then have to also run the python script
<br>

## Step 4: dockerize FastAPI app

Make sure you are in root dir
<br>
get dependency requirements

```bash
pip freeze > requirements.txt
```

<br>
Create Dockerfile
<br>
but now, using command `WORKDIR` to for better structure
<br>
(this is like a combination of `mkdir` and `cd`)
<br>
Install dependencies to our image
<br>

```docker
COPY requirements.txt .

RUN pip install -r requirements.txt

```

copy files from local machine to desired image area

```docker
COPY ./app ./app
```

## Step 5: build image and run container

Similar to before

```bash
docker build -t <chosen-name-of-image> <location-of-dockerfile>
```

But now, have to map the port.
<br>
FIRST have to add that in uvicorn call

```python
#port=8000 is default
uvicorn.run(app, port=8000, host="0.0.0.0")
```

(rebuild docker image if necessary).
<br>
Then, run docker container with some extra paramaters

```bash
docker run -p <port-outside:port-from-container> <name-of-image>
```

like this:

```bash
docker run -p 8000:8000 <name-of-image>
```

if the port mapping is not done, the opened URL will display nothing

## Additional: inspecting container process

Can get id of running container with `docker ps`
<br>
Can get the shell inside the container by doing:

```docker
docker exec -it <container-id> /bin/sh
```
