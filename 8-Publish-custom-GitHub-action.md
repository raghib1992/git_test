# Public
Workflows in any repository can use public actions. If you're developing an action with the intent to make it open source or make it publicly available through the GitHub Marketplace, we recommend (and in most cases it's required) that the action has its own repository instead of bundling it with other application code. This allows you to version, track, and release the action just like any other piece of software. This makes it easier for the GitHub community to discover the action, narrows the scope of the code base for developers fixing issues and extending the action, and separates the action's versioning from the versioning of other application code.

# Private
When an action is in a private repository, only workflows in the same repository can use the action. With private actions, you can store the action's files in any location in your repository. If you plan to combine action, workflow, and application code in a single repository, we recommend storing the action in the .github directory. For example, .github/actions/action-a and .github/actions/action-b.

# Document your action
It can be very frustrating to use a new tool or application when the documentation is vague or even missing. It's important to include good documentation with your action so that others can see how it works, whether you plan to make it public or private. The first thing to do is creating a good README.md file for your action.
