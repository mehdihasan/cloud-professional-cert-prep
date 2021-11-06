# Introduction to Docker

gcloud config set project \
  $(gcloud projects list --format='value(PROJECT_ID)' \
  --filter='qwiklabs-gcp')

gcloud auth list

gcloud config list project


## Hello World

docker run hello-world

docker images

docker run hello-world

docker ps


## Build

mkdir test && cd test

```bash
cat > Dockerfile <<EOF
# Use an official Node runtime as the parent image
FROM node:6
# Set the working directory in the container to /app
WORKDIR /app
# Copy the current directory contents into the container at /app
ADD . /app
# Make the container's port 80 available to the outside world
EXPOSE 80
# Run app.js using node when the container launches
CMD ["node", "app.js"]
EOF
```

```bash
cat > app.js <<EOF
const http = require('http');
const hostname = '0.0.0.0';
const port = 80;
const server = http.createServer((req, res) => {
    res.statusCode = 200;
      res.setHeader('Content-Type', 'text/plain');
        res.end('Hello World\n');
});
server.listen(port, hostname, () => {
    console.log('Server running at http://%s:%s/', hostname, port);
});
process.on('SIGINT', function() {
    console.log('Caught interrupt signal and will exit');
    process.exit();
});
EOF
```

docker build -t node-app:0.1 .


docker images


## Run

docker run -p 4000:80 --name my-app node-app:0.1


curl http://localhost:4000


docker stop my-app && docker rm my-app

docker run -p 4000:80 --name my-app -d node-app:0.1
docker ps

docker logs [container_id]


## Debug

docker logs -f [container_id]

docker exec -it [container_id] bash

docker inspect [container_id]

docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' [container_id]


## Publish
[hostname]= gcr.io
[project-id]= your project's ID
[image]= your image name
[tag]= any string tag of your choice. If unspecified, it defaults to "latest".


gcloud config list project


docker tag node-app:0.2 gcr.io/$GOOGLE_CLOUD_PROJECT/node-app:0.2


docker push gcr.io/$GOOGLE_CLOUD_PROJECT/node-app:0.2



Stop and remove all containers:
docker stop $(docker ps -q)
docker rm $(docker ps -aq)


You have to remove the child images (of node:6) before you remove the node image. Replace [project-id].
docker rmi node-app:0.2 gcr.io/$GOOGLE_CLOUD_PROJECT/node-app node-app:0.1
docker rmi node:6
docker rmi $(docker images -aq) # remove remaining images
docker images



docker pull gcr.io/$GOOGLE_CLOUD_PROJECT/node-app:0.2
docker run -p 4000:80 -d gcr.io/$GOOGLE_CLOUD_PROJECT/node-app:0.2
curl http://localhost:4000