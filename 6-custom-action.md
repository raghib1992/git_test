GitHub Actions is a powerful feature that helps you to go from code to cloud, all from the comfort and convenience of your own repository. Here, you'll learn about the different types of GitHub actions and the metadata, syntax, and workflow commands to create custom GitHub actions.

hree different types of actions: Docker container actions, JavaScript actions, and composite run steps actions.

## Docker container actions
Create a Dockerfile to define the commands to assemble the Docker image.
Create an action.yml metadata file to define the inputs and outputs of the action. Set the runs: using: value to docker and the runs: image: value to Dockerfile in the file.
Create an entrypoint.sh file to describe the docker image.
Commit and push your action to GitHub with the following files: action.yml, entrypoint.sh, Dockerfile, and README.md.


## JavaScript actions
JavaScript actions can run directly on the runner machine, and separate the action code from the environment that's used to run the action. Because of this, the action code is simplified and can execute faster than actions within a Docker container.

As a prerequisite for creating and using packaged JavaScript actions, you need to download Node.js, which includes npm. As an optional step (but one that we recommend) is to use GitHub Actions Toolkit Node.js, which is a collection of Node.js packages that allows you to quickly build JavaScript actions with more consistency.

### The steps to build a JavaScript action are minimal and straightforward:
Create an action.yml metadata file to define the inputs and outputs of the action, as well as tell the action runner how to start running this JavaScript action.
Create an index.js file with context information about the Toolkit packages, routing, and other functions of the action.
Commit and push your action to GitHub with the following files: action.yml, index.js, node_modules, package.json, package-lock.json, and README.md.



## Composite run steps actions
Composite run steps actions allow you to reuse actions by using shell scripts. You can even mix multiple shell languages within the same action.
If you have many shell scripts to automate several tasks, you can now easily turn them into an action and reuse them for different workflows.
Sometimes it's easier to just write a shell script than using JavaScript or wrapping your code in a Docker container.




# Metadata and syntax needed to create an action  

## Name
The name of your action. Helps visually identify the action in a job.

## Description
A summary of what your action does.

## Inputs
Inputs are the parameters that allow you to specify data that the action expects to use during its runtime. GitHub stores these input parameters as environment variables.
```yml
inputs:
  firstNameStudent:
    description: 'First name of student'
    required: false
    default: '1'
  studentGrade:
    description: 'Grade of the student'
    required: true
```

## Outputs
Outputs are the parameters that allow you to declare data. Keep in mind that actions that run later in a workflow can use the output data that was declared in a previously run action.
```yml
outputs:
  average:
    description: 'The average grade of the students'
```


## Runs
As you learned previously, your action needs to have a runs statement that defines the command necessary to execute your action. Depending on how you're creating your action—whether you're using a Docker container, JavaScript, or composite run steps—the runs syntax is defined differently.

### runs for Docker actions
Docker container actions require the runs statement to configure the image the Docker action uses with the following arguments:

using: Needs to be set to docker to run a Docker container action
image: Docker image used as the container to run the action
```yml
runs:
  using: 'docker'
  image: 'Dockerfile'
```


### runs for JavaScript actions
JavaScript actions require that the runs statement take the following two arguments:

using: Application used to execute the code as defined in main
main: File that contains the action code; the application defined in using executes this file
```yml
runs:
  using: 'node12'
  main: 'main.js'
```




### runs for composite run steps actions
Composite run steps actions require that the runs statement take the following three arguments:

using: Needs to be set to "composite" to run a composite run step
steps: Run steps to run the action
steps[*].run: Command you want to run (can be inline or a script in your action repository)
For example, here's a runs statement for a composite run steps action that will run the script at filepath /test/script/sh:
```yml
runs:
  using: "composite"
  steps:
    - run: ${{ github.action_path }}/test/script.sh
      shell: bash
```

## Branding
An optional but fun feature is the ability to customize the badge of your action. The badge is displayed next to the action name in the GitHub Marketplace. You can use a color and Feather icon to create the badge. For branding, you'll need to specify the icon and color you wish to use.
```yml
branding:
  icon: 'shield'  
  color: 'blue'
```
