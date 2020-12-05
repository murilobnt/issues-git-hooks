## Isssues Git-Hooks

Issues Git-Hooks is a project that aims to provide a simple
integration with Git and GitHub issues, linking every commit to
an existing issue.

### Objective

The objective of this project is to prevent commits that don't
reference an issue in its comment. So, for example, the command
`git commit -m "Commit without an issue reference"` will not be
accepted, while `git commit -m "Issue #1: Adds something"` will,
as long as the Issue #1 exists and is open. It doesn't need to
have the word **Issue**, but the **#issue-number** is required.

This project also aims to provide log information (author, date
and commit information) in the issues, so this project will append
them to the commit message.

### Dependencies and Usage

This project's only dependency is
[Python3](https://www.python.org/downloads/).

In order to use this project, the scripts under the **hooks** directory must be
present at the **.git/hooks** directory of your repository. Once both scripts
are there, you will need to change the value of the repository field
of the .githook_config.json file. This is an example of how it
should look like:

```json
{
  "repository" : "murilobnt/issues-git-hooks"
}
```

The final step is to set an enviroment variable, named
GITHOOKS_GHTOKEN, containing a GitHub token. You can find
information on getting a token in the GitHub Docs'
[Creating a personal access token](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token) page.
It just needs the **repo** scope, in order to read the issues of
an private repositories (and to not get API rate limited by GitHub
so easily).

In order to set the environment variable, in Linux, you can alter
your **~/.bashrc** or **~/.zshrc** file and add the following line
to it:

```sh
export GITHOOKS_GHTOKEN='your-token-number-here'
```

And then just reopen your terminal or do the `source ~/.bashrc` or
`source ~/.zshrc`.

### What does it do

Issues Git-Hooks verifies for issues references in the commit
message string, like "#1" or "#2". If there's a possible issue
reference in the string, it calls for the GitHub API in order to
discover if the issue exists and it's open. If at least an issue
reference is found, the commit is generated.

Now that the commit is generated, it's wanted to add log
informations to the commit message, so it will be displayed in the
issues once the commit is pushed. In order to do that, this project
calls for the [git show](https://git-scm.com/docs/git-show) command
and gets its output. Now that it has access to the message of the
commit, plus the log data of the author and date of the commit, it
executes a
[git commit --amend](https://docs.github.com/en/free-pro-team@latest/github/committing-changes-to-your-project/changing-a-commit-message)
and rewrites the comment with the new information gathered.

Since amend generates a new commit, the **commit-msg** and
**post-commit** hooks get called again. In order to prevent that,
the project creates an temporary json file that is read if the
last commit was generated by the hooks. If positive, it just
ignores the hooks and the file is removed.

### License

Issues Git-Hooks is licensed under the [MIT License](https://github.com/murilobnt/issues-git-hooks/blob/master/LICENSE).
