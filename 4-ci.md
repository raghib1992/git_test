Automate reviews in GitHub using workflows
So far, we described starting the workflow with GitHub events such as push or pull-request. We could also run a workflow on a schedule, or on some event outside of GitHub.
Sometimes, we want to run the workflow only after a person performs an action. For example, we might only want to run a workflow after a reviewer approves the pull request. For this scenario, we can trigger on pull-request-review.
Another action we could take is to add a label to the pull request. In this case, we use the pullreminders/label-when-approved-action action.
```yml
steps:
     - name: Label when approved
       uses: pullreminders/label-when-approved-action@main
       env:
         APPROVALS: "1"
         GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
         ADD_LABEL: "approved"
```
Notice the block called env:. This block is where you set the environment variables for this action. For example, you can set the number of approvers needed. Here, it's one. The secrets.GITHUB_TOKEN authentication variable is required because the action must make changes to your repository by adding a label. Finally, you supply the name of the label to add.
