Types of GitHub actions
There are three types of GitHub actions: 
container actions,
JavaScript actions,
composite actions.


Simple node js 
```
steps:
  - uses: actions/checkout@v1
  - name: npm install and build webpack
    run: |
      npm install
      npm run build
```

 container action to run containerized code
```
name: "Hello Actions"
description: "Greet someone"
author: "octocat@github.com"

inputs:
    MY_NAME:
      description: "Who to greet"
      required: true
      default: "World"

runs:
    uses: "docker" # you'll need to provide the path to the Docker image file
    image: "Dockerfile"

branding:
    icon: "mic"
    color: "purple"
```
