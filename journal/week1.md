# Week 1 — App Containerization
## Run Python
- Setup env vars. Only Run local env
```
cd backend-flask
export FRONTEND_URL="*"
export BACKEND_URL="*"
python3 -m flask run --host=0.0.0.0 --port=4567
cd 
..
```
#### For the above, please notice the following item:
 - make sure to unlock the port on the PORTS tab
 - open the link for 4567 in your browser
 - append to the url /api/activities/home
 - you should get back json 

## Container Backend

### Add Docker file
Create a file here: backend-flask/Dockerfile

```
FROM python:3.10-slim-buster

# Inside Containter
# make a new folder named inside container
WORKDIR /backend-flask

# Oustide Container -> Inside Container
# this contains the libraries want to install to run the app
COPY requirements.txt requirements.txt

#Inside Container
# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

#Oustdie Container -> Inside Container
# . means everything in the current directory
# first period . ~/backend-flask(outside container)
# second period . /backend-flask(inside container)
COPY . .

# Set Environment Variables(Env Vars)
# Inside container and will remain set when the container is running
ENV FLASK_ENV=development

#
EXPOSE ${PORT}

#CMD
# -m means module
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]
```

#### Builder Docker
```
docker build -t  backend-flask ./backend-flask
```
run `docker images` to check

#### Builder Run
```
# Not working 
docker run --rm -p 4567:4567 -it backend-flask
FRONTEND_URL="*" BACKEND_URL="*" docker run --rm -p 4567:4567 -it backend-flask

# working, --rm after stopping container, the container will be removed
docker run --rm -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask

# after stopping container, the container still existed. Although run `docker ps`, no any conatiner. but you can use `docker ps -a`, the containter will be existed

docker run -p 4567:4567 -it -e FRONTEND_URL='*' -e BACKEND_URL='*' backend-flask

# check
docker run --rm -p 4567:4567 -it  -e FRONTEND_URL -e BACKEND_URL backend-flask
unset FRONTEND_URL="*"
unset BACKEND_URL="*"

```

- check docker running, run a new terminal and run `docker ps`


## Container Fronted

### Run NPM Install
We have to run NPM Install before building the container since it needs to copy the contents of node_modules
```
cd frontend-react-js
npm i
```

### Build Container
```
docker build -t frontend-react-js ./frontend-react-js
```
### Run Container
```
docker run -p 3000:3000 -d frontend-react-js
docker run --rm -p 3000:3000 -d frontend-react-js

```

## Multiple Containers
### Create a docker-compose file

Create `docker-compose.yml` at the root of your project.

```
version: "3.8"
services:
  backend-flask:
    environment:
      FRONTEND_URL: "https://3000-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
      BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./backend-flask
    ports:
      - "4567:4567"
    volumes:
      - ./backend-flask:/backend-flask
  frontend-react-js:
    environment:
      REACT_APP_BACKEND_URL: "https://4567-${GITPOD_WORKSPACE_ID}.${GITPOD_WORKSPACE_CLUSTER_HOST}"
    build: ./frontend-react-js
    ports:
      - "3000:3000"
    volumes:
      - ./frontend-react-js:/frontend-react-js

# the name flag is a hack to change the default prepend folder
# name when outputting the image names
networks: 
  internal-network:
    driver: bridge
    name: cruddur
```

### Adding DynamoDB Local and Postgres
We are going to use Postgres and DynamoDB local in future labs We can bring them in as containers and reference them externally

Lets integrate the following into our existing docker compose file:

Postgres
```
services:
  db:
    image: postgres:13-alpine
    restart: always
    environment:
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes: 
      - db:/var/lib/postgresql/data
volumes:
  db:
    driver: local
```

## Top 10 Docker Container Security Best Practices with Tutorial
- Keep Host and Docker Updated to latest security Patches
- Docker daemon and containers should run in non-root user mode
- Image Vulnerability Scanning
- Trusting a Private vs Public Image Registry
- No Sensitive Data in Docker files or Images
- Use Secrect Management Services to Share Secrets
- Read only File system and Volume Docker
- Separate database for log term storage
- Use DevSecOps practice while building application security
- Ensure all Code is tested for vulnerabilities before production use

