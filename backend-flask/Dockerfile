FROM python:3.10-slim-bullseye

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