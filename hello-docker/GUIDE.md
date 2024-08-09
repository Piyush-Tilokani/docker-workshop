# Simple hello-world docker app 

# Step 1
- Create a folder named `docker-tutorial`
- `cd docker-tutoraial`
- Create a folder named `hello-docker`
# Step 2
- Create a file named `HelloWorld.js`
- Write simple JavaScript code to print hello world

`HelloWorld.js`
  ```js
  console.log("Hello world")
  ```
# Step 3
- In the `hello-docker` folder, create a new file named `Dockerfile` (no extensions, just "Dockerfile")
- In the Dockerfile, write the following commands

`Dockerfile` 
  ```Dockerfile
  # Specify the base image of the container
  FROM node:20-alpine

  #Define the working directory in the container
  WORKDIR /app

  #Copy all the contents of host directory to working directory
  COPY . .

  #Define the default program that runs when the container is instantiated
  CMD node HelloWorld.js
  ```

# Step 4
- To create a docker image from our `Dockerfile`, we need to do the following:
  1. Make sure you are inside the `hello-docker` directory and your  `Dockerfile` exists in the same directory
  2. Run `docker build -t hello-img .` (Do not miss the `.` at the end)
  
  This command builds a docker image named `hello-img` usng the docker file in the current directory `.`
  3. Now, to check if the image has been successfully created, you can run the following command
  `docker images`

It should show something like this

![Screenshot 2024-08-10 024445](https://github.com/user-attachments/assets/df7f5c87-b68a-4da0-a5c9-0d5887373e41)

# Step 5
- Now, to finally run your docker image, type the following command:

`docker run hello-img`

- This should print "Hello world" on the console

## Congratulations you have created your first docker container

  
