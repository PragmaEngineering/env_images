`cd ubuntu_gotty && docker build -t ubotty:{version_number} .`

`docker run -p 1337:8080 ubotty:{version_number}`

**OR**

`cd ubuntu_gotty && ./start.sh`

visit localhost:1337
