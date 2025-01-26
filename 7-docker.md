# Running Jobs in Docker Container
```yml
name: Use Docker in Github-Action

on: [workflow_dispatch]

jobs:
  docker-job:
    runs-on: ubuntu-latest
    container:
      image: node:20.3-alpine3.17
      # credentials:
      #   username:
      #   password:
      env:
        API_URL: some-url.com
      ports:
        - 80
      # volumes:
      #   - vol_name:/path/in/container
      #   - /path/to/container
      #   - /path/in/host:/path/in/container
      # options: --cpus 1
    steps:
      - name: Log Node & OS Versions
        run: |
          node -v
          cat /etc/os-release
      - name: Log Env
        run: echo $API_URL
```

# Runing steps in docker container
```yml
jobs:
  docker-job:
    runs-on: ubuntu-latest
    container:
      image: node:20.3-alpine3.17
      # credentials:
      #   username:
      #   password:
      env:
        API_URL: some-url.com
      ports:
        - 80
      # volumes:
      #   - vol_name:/path/in/container
      #   - /path/to/container
      #   - /path/in/host:/path/in/container
      # options: --cpus 1
    steps:
      - name: Log Node & OS Versions
        run: |
          node -v
          cat /etc/os-release
      - name: Log Env
        run: echo $API_URL
      - name: Container in a Step
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: /usr/local/bin/node
          args: -p 2+3
      - name: Log node version
        uses: docker://node:18.16-alpine3.17
        with:
          args: -v
```
# Shared volume and network
```yml
name: Docker
on: [workflow_dispatch]

jobs:
  docker-job:
    runs-on: ubuntu-latest
    container:
      image: node:20.3-alpine3.17
      # credentials:
      #   username:
      #   password:
      env:
        API_URL: some-url.com
      ports:
        - 80
      # volumes:
      #   - vol_name:/path/in/container
      #   - /path/to/container
      #   - /path/in/host:/path/in/container
      # options: --cpus 1
    steps:
      - name: Log Node & OS Versions
        run: |
          node -v
          cat /etc/os-release
      - name: Log Env
        run: echo $API_URL
      - name: Create a file
        run: echo 'Some text' > text.txt
      - name: Container in a Step
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: /usr/local/bin/node
          args: -p 2+3
      - name: Log node version
        uses: docker://node:18.16-alpine3.17
        with:
          args: -v
      - name: Show file contents
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: cat
          args: text.txt
```
# Custom Entry point
```yml
name: Docker
on: [workflow_dispatch]

jobs:
  docker-job:
    runs-on: ubuntu-latest
    container:
      image: node:20.3-alpine3.17
      # credentials:
      #   username:
      #   password:
      env:
        API_URL: some-url.com
      ports:
        - 80
      # volumes:
      #   - vol_name:/path/in/container
      #   - /path/to/container
      #   - /path/in/host:/path/in/container
      # options: --cpus 1
    steps:
      - name: Log Node & OS Versions
        run: |
          node -v
          cat /etc/os-release
      - name: Log Env
        run: echo $API_URL
      - name: Create a file
        run: echo 'Some text' > text.txt
      - name: Container in a Step
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: /usr/local/bin/node
          args: -p 2+3
      - name: Log node version
        uses: docker://node:18.16-alpine3.17
        with:
          args: -v
      - name: Show file contents
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: cat
          args: text.txt
      - uses: actions/checkout@v3
      - name: Run a bash script
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: ./script.sh
          args: "Some string"
```

### Script
```sh
#!/bin/sh
echo "$@"
node -v
```
## Send api message to lack
- Create new webhook api in slack `api.slack`
```yml
name: Docker
on: [workflow_dispatch]

jobs:
  docker-job:
    runs-on: ubuntu-latest
    container:
      image: node:20.3-alpine3.17
      # credentials:
      #   username:
      #   password:
      env:
        API_URL: some-url.com
      ports:
        - 80
      # volumes:
      #   - vol_name:/path/in/container
      #   - /path/to/container
      #   - /path/in/host:/path/in/container
      # options: --cpus 1
    steps:
      - name: Log Node & OS Versions
        run: |
          node -v
          cat /etc/os-release
      - name: Log Env
        run: echo $API_URL
      - name: Create a file
        run: echo 'Some text' > text.txt
      - name: Container in a Step
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: /usr/local/bin/node
          args: -p 2+3
      - name: Log node version
        uses: docker://node:18.16-alpine3.17
        with:
          args: -v
      - name: Show file contents
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: cat
          args: text.txt
      - uses: actions/checkout@v3
      - name: Run a bash script
        uses: docker://node:18.16-alpine3.17
        with:
          entrypoint: ./script.sh
          args: "Some string"
      - name: Send a slack message
        uses: docker://technosophos/slack-notify
        env:
          SLACK_WEBHOOK: ${{ secrets.SLACK_WEBHOOK }}
          SLACK_TITLE: From Github Actions
          SLACK_MESSAGE: "Actor: ${{github.actor}}, Event: ${{github.event_name}}"
          SLACK_COLOR: "#723fc4"
```
# Using Service in Github Action 
```yml
name: Test Api
on: [push, workflow_dispatch]

jobs:
  test-api:
    runs-on: ubuntu-latest
    container: ubuntu:20.04
    services:
      app:
        image: alialaa17/simple-node-api
        # ports:
        #   - 3001:3000
        env:
          MONGO_HOSTNAME: mongo
      mongo:
        image: mongo
        # ports:
        #   - 27017:27017
    steps:
      - name: Install curl
        run: |
          apt-get -y update
          apt-get -y install curl
      - name: Dump Services Context
        run: echo '${{ toJson(job.services) }}'
      - name: Post a user
        run: 'curl -X POST http://app:3000/api/users -H ''Content-Type: application/json'' -d ''{"username": "hello","address": "dwded"}'''
      - name: Get Users
        run: curl http://app:3000/api/users
```

# Pushig Docker iamge to docker and ghcr.io
```yml
name: Build & Publish Docker Image
on:
  release:
    types: [published]

jobs:
  push-to-dockerhub-and-ghcr:
    runs-on: ubuntu-latest
    permissions:
      packages: write
      contents: read
    steps:
      - uses: actions/checkout@v3
      - name: Login to Dockerhub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}
      - name: Log in to GHCR
        uses: docker/login-action@v2
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Extract Metadata
        id: metadata
        uses: docker/metadata-action@v4
        with:
          images: |
            alialaa17/simple-node-api
            ghcr.io/${{ github.repository }}
          tags: |
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
      - name: Build & Publish Docker Image
        uses: docker/build-push-action@v4
        with:
          context: .
          file: ./Dockerfile
          push: true
          tags: ${{ steps.metadata.outputs.tags }}
          labels: ${{ steps.metadata.outputs.labels }}
```
