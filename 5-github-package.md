Publish to GitHub Packages

Docker is an engine that allows you to run containers. Containers are packages of software that can run reliably in different environments. Containers include everything needed to run the application. Containers are lightweight in comparison to virtual machines. A Dockerfile is a text document that contains all the commands and instructions necessary to build a Docker Image. A Docker image is an executable package comprised of code, dependencies, libraries, a runtime, environment variables, and configuration files. A Docker container is a runtime instance of a Docker Image.

the workflow file to publish a Docker image to GitHub Packages
```yml
name: Publish to Docker
on:
  push:
    branches:
      - main
permissions:
  packages: write
  contents: read
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4
      # Add your test steps here if needed...
      - name: Docker meta
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/YOURNAME/publish-packages/game
          tags: type=sha
      - name: Login to GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.repository_owner }}
          password: ${{ secrets.GITHUB_TOKEN }}
      - name: Build container
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
```


o pull the Docker image, we need to log into Docker first.

Before we can use this Docker image, you will need to generate a personal access token (classic) that contains the following permissions:

Scopes for Personal Access Token (classic) 🪙

repo (all)
write:packages
read:packages


We will use this token to log in to Docker, and authenticate with the package.

Open your terminal (Bash or Git Bash recommended).
Use the following command to log in:
docker login ghcr.io -u USERNAME
Replace USERNAME with your GitHub username.
Enter your new Personal Access Token as the password.
Press Enter.


⌨️ Activity: Pull your image
Copy the pull command from the package instructions.
🔥 Tip: To reach this page, click the Code tab at the top of your repository. Then, find the navigation bar below the repository description, and click the Packages heading link screenshot of the pull command on the GitHub package page
Or alternatively, in the following URL replace YOURNAME, REPONAME, and browse to https://github.com/users/YOURNAME/packages?repo_name=REPONAME and click on the package name
Replace YOURNAME with your GitHub username.
Replace TAG with the image tag.
Paste the pull command into your terminal. It should look something like this:
docker pull ghcr.io/YOURNAME/publish-packages/game:TAG
Press Enter.
You should see output indicating that the pull was successful, like Status: Downloaded newer image for ghcr.io/YOURNAME/publish-packages/game:TAG. screenshot of successful Docker image output
We can't automatically verify this step for you, so please continue on to the next step below!
Step 5: Run your image
Nicely done grabbing your Docker image! ☺️

Let's trying running it.

⌨️ Activity: Run your image
Find your image information by typing docker image ls. screenshot of output from Docker image ls command: lists docker images, REPOSITORY TAG and docker URL
Use the following command to run a container from your image:
docker run -dp 8080:80 --rm <YOUR_IMAGE_NAME:TAG>
Replace YOUR_IMAGE_NAME with your image name under the REPOSITORY column.
Replace TAG with the image tag under the TAG column.
Press Enter.
If everything went well, you will see hash value as output on your screen.
Optionally, you can open localhost:8080 to see the page you just created.
We can't automatically verify this step for you, so please continue on to the next step below!
Finish
Congratulations friend, you've completed this course!

celebrate

Here's a recap of all the tasks you've accomplished in your repository:

You wrote a workflow that sends a code through a continuous delivery pipeline.
You built a fully deployable artifact.
You did so using GitHub Actions and GitHub Packages!
