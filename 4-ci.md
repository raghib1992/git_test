# You see this default Node.js template workflow, in the newly created file node.js.yml.
```yml
name: Node.js CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:

    runs-on: ubuntu-latest

    strategy:
      matrix:
        node-version: [14.x, 16.x, 18.x]

    steps:
    - uses: actions/checkout@v3
    - name: Use Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    - run: npm ci
    - run: npm run build --if-present
    - run: npm test
```

# Automate reviews in GitHub using workflows
So far, we described starting the workflow with GitHub events such as push or pull-request. We could also run a workflow on a schedule, or on some event outside of GitHub.
Sometimes, we want to run the workflow only after a person performs an action. For example, we might only want to run a workflow after a reviewer approves the pull request. For this scenario, we can trigger on pull-request-review.
Another action we could take is to add a label to the pull request. In this case, we use the pullreminders/label-when-approved-action action.
```yml
on: pull_request_review
name: Label approved pull requests
jobs:
  labelWhenApproved:
    name: Label when approved
    runs-on: ubuntu-latest
    steps:
    - name: Label when approved
      uses: pullreminders/label-when-approved-action@master
      env:
        APPROVALS: "2"
        GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        ADD_LABEL: "approved"
        REMOVE_LABEL: "awaiting%20review"
```
Notice the block called env:. This block is where you set the environment variables for this action. For example, you can set the number of approvers needed. Here, it's one. The secrets.GITHUB_TOKEN authentication variable is required because the action must make changes to your repository by adding a label. Finally, you supply the name of the label to add.

along with all their tests, produce quite a bit of log information. It might be difficult to sort through it all. In the following sample, we show you how to move the test step to a dedicated test job. This job tests against multiple targets. Separating the build and test steps makes it easier to understand the log.

```yml
test:
  runs-on: ${{ matrix.os }}
  strategy:
    matrix:
      os: [ubuntu-latest, windows-latest]
      node-version: [16.x, 18.x]
  steps:
  - uses: actions/checkout@v3
  - name: Use Node.js ${{ matrix.node-version }}
    uses: actions/setup-node@v3
    with:
      node-version: ${{ matrix.node-version }}
  - name: npm install, and test
    run: |
      npm install
      npm test
    env:
      CI: true
```
# What are artifacts?
When a workflow produces something other than a log entry, the product is called an artifact. For example, the Node.js build produces a Docker container that can be deployed. This artifact, the container, can be uploaded to storage by using the action actions/upload-artifact and later downloaded from storage by using the action actions/download-artifact.

Storing an artifact preserves it between jobs. Each job uses a fresh instance of a virtual machine (VM), so you can't reuse the artifact by saving it on the VM. If you need your artifact in a different job, you can upload the artifact to storage in one job, and download it for the other job.

Artifact storage
Artifacts are stored in storage space on GitHub. The space is free for public repositories and some amount is free for private repositories, depending on the account. GitHub stores your artifacts for 90 days.

In the following workflow snippet, notice that in the actions/upload-artifact@main action there's a path: attribute. The value of this attribute is the path to store the artifact. Here, we specify public/ to upload everything to a directory. If we just wanted to upload a single file, we use something like public/mytext.txt.

```yml
build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: npm install and build webpack
        run: |
          npm install
          npm run build
      - uses: actions/upload-artifact@main
        with:
          name: webpack artifacts
          path: public/
```
#### we download the artifact. Now the test job can use the artifact for testing.
```yml
steps:
    - uses: actions/checkout@v3
    - uses: actions/download-artifact@main
      with:
        name: webpack artifacts
        path: public
```

Step 4: Add branch protections
Great job uploading test reports! 🥳

Take a look at the merge box, you'll notice you can merge this even though the review process hasn't been met.

Protected branches ensure that collaborators on your repository cannot make irrevocable changes to branches. Enabling protected branches also allows you to enable other optional checks and requirements, like required status checks and required reviews.

⌨️ Activity: Add branch protections
Go to Branches settings. You can navigate to that page manually by selecting the right-most tab in the top of the repository called Settings and then clicking Branches.
Click Add classic branch protection rule under "Branch protection rules".
Type main in Branch name pattern.
Check Require a pull request before merging.
Uncheck Require approvals.
Check Require status checks to pass before merging.
Check all build and test jobs that you'd like to see in the newly visible gray box.
Click Create.
