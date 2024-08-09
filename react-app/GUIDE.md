# Containerisation of React app using Dockerfile 

# Step 1
- Inside the `docker-tutorial` directory, create a folder named `react-app`
- `cd react-app`
- Create a react app using the following command

`npm create vite@latest`
# Step 2
- In the `react-app` folder, create a new file named `Dockerfile` (no extensions, just "Dockerfile")
- In the `Dockerfile`, write the following commands

`Dockerfile`
```Dockerfile
# set the base image to create the image for react app
FROM node:20-alpine

# create a user with permissions to run the app
# -S -> create a system user
# -G -> add the user to a group
# This is done to avoid running the app as root
# If the app is run as root, any vulnerability in the app can be exploited to gain access to the host system
# It's a good practice to run the app as a non-root user
RUN addgroup app && adduser -S -G app app

# set the user to run the app
USER app

# set the working directory to /app
WORKDIR /app

# copy package.json and package-lock.json to the working directory
# This is done before copying the rest of the files to take advantage of Docker’s cache
# If the package.json and package-lock.json files haven’t changed, Docker will use the cached dependencies
COPY package*.json ./

# sometimes the ownership of the files in the working directory is changed to root
# and thus the app can't access the files and throws an error -> EACCES: permission denied
# to avoid this, change the ownership of the files to the root user
USER root

# change the ownership of the /app directory to the app user
# chown -R <user>:<group> <directory>
# chown command changes the user and/or group ownership of for given file.
RUN chown -R app:app .

# change the user back to the app user
USER app

# install dependencies
RUN npm install

# copy the rest of the files to the working directory
COPY . .

# expose port 5173 to tell Docker that the container listens on the specified network ports at runtime
EXPOSE 5173

# command to run the app
CMD npm run dev
```
- In the same directory, make a file `.dockerignore`

`.dockerignore`
```.dockerignore
node_modules/
```
  
# Step 3
- Let's try to build our docker container from our `Dockerfile`

`docker build -t react-img .` (Do not miss the `.` at the end)
- Let's try to run our docker image

`docker run react-img`

- You may run into the following problem

![Screenshot 2024-08-10 030327](https://github.com/user-attachments/assets/1080ac73-3cd5-44f2-ab7d-ae71b7cd3a79)

This is because we had exposed the port in our docker container bur did not map it to port of the host(actual machine)

- To fix this problem, we need to map the host port to the container port using the following command

  `docker run -p 5173:5173 react-img`
- You might still see an error which is due to vite comfigurations
- navigate to `react-app/packjage.json` and change it to the following
```package.json
  {
  "name": "react-app",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite --host", 
    "build": "vite build",
    "lint": "eslint .",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@eslint/js": "^9.8.0",
    "@types/react": "^18.3.3",
    "@types/react-dom": "^18.3.0",
    "@vitejs/plugin-react": "^4.3.1",
    "eslint": "^9.8.0",
    "eslint-plugin-react": "^7.35.0",
    "eslint-plugin-react-hooks": "^5.1.0-rc.0",
    "eslint-plugin-react-refresh": "^0.4.9",
    "globals": "^15.9.0",
    "vite": "^5.4.0"
   }
  }
```
- Now if we try to run the vite app, using the follwong command, it should work fine

`docker run -p 5173:5173 react-img`

# Step 4
- Syncing the changes in host code with the container
- To sync the changes in conatiner on chage in the host, we need to reconfigure the `react-app/vite.config.js` file

`react-app/vite.config.js`
```vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    watch: {
      usePolling: true,
    },
  },
})
```
- Now run the image with the follwing command
  
  `docker run -p 5173:5173 -v "$(pwd):/app" -v /app/node_modules react-img`
# Thanks for following along with me

