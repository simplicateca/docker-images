# Simplicate Docker Images

## How to use

    cd path/to/Dockerfile

    docker build . --no-cache

Find the IMAGE ID for the image that was just built

    docker images 

Tag that image as the latest

    docker tag e50079d21d7b simplicateweb/[image-name]:latest

Push the tag

    docker push simplicateweb/[image-name]:latest
