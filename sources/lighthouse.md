page_title: Shippable CI User Guide | Shippable
page_description: This section describes various concepts pertaining to Shippable CI
page_keywords: getting started, questions, documentation, shippable, config, yml

# Shippable CI User Guide

## Connecting your GitHub and Bitbucket accounts

If you want to use Shippable to build both GitHub and Bitbucket
repositories, you can connect the two accounts in order to get a
consolidated view of all your projects in one Shippable account.

To connect your accounts, sign in with the GitHub/BitBucket account that
you want to be your primary account. You will go through the auth flow
and land on your Shippable dashboard.

If you signed in with GitHub, you can click on the BitBucker icon on the
top right of your dashboard and then follow the auth flow for Bitbucket.

If you originally signed up with Bitbucket, you can click on the GitHub
icons on the top right of your dashboard and then follow the auth flow
for GitHub.

Once your accounts are both connected to Shippable, you should see a
consolidated list of orgs and projects in your account. You can sign in
with either of your credentials after this point.

* * * * *

## Permissions

We closely mimic GitHub and Bitbucket permissions for Orgs and projects.
Anyone who has access to an organization or repository in
GitHub/Bitbucket will also have access to build information and/or
repository and build actions on Shippable. This happens automatically,
so if you enable a repository in your Org on Shippable and another team
member signs in, they will see the enabled repository and build history
already present in their account.

We support 2 roles -

**Owner :** Owners have all privileges for an Org or Project. They can
enable, run and delete projects, upgrade pricing plans, and view/run,
cancel, and delete builds.

**Collaborator :** Collaborators can enable projects and view/run builds
on Shippable. They cannot delete enabled projects or upgrade pricing
plans.

* * * * *

## Minions

Minions are Docker based containers that run your builds and tests. You
can think of them as Build VMs. Each minion runs one build at a time, so
the more minions you have, the more concurrency you will get.

Minions are automatically provisioned whenever a build is triggered and
it will get deleted after the build finishes execution. We will
automatically add additional minions, as appropriate, based on your
subscription plan.

Each minion starts from a base image and can be customized by specifying
`before_install` scripts in the YML file. A minion can be configured to
run any package, library, or service that your application requires.
There are some preinstalled tools and services that you can use to
customize your minions even further.

### Operating systems

All our Linux minions start from a vanilla base image from the Docker
registry. In your YML file you can use the `build_image` option to
specify the image you want to use. We support all images as a starting
point for your minion. Minions can be further customized by using the
`before_install` and `install` tags in `shippable.yml` that is in the
root of your code repository.

* * * * *

## Pull requests

Shippable will integrate with github to show your pull request status on
CI. Whenever a pull request is opened for your repo, we will run the
build for the respective pull request and notify you about the status.
You can decide whether to merge the request or not, based on the status
shown. If you accept the pull request, Shippable will run one more build
for the merged repo and will send email notifications for the merged
repo. To rerun a pull request build, go to your project's page -\> Pull
Request tab and then click on the **Build this Pull Request** button.

* * * * *

## Build badge

Badges will display the status of your default branch. You can find the
build badges on the project's page. Click on the **Badge** button andopy the markdown to your README file to display the status of most
recent build on your Github or Bitbucket repo page.

* * * * *

## Build termination

Build will be forcefully terminated in the following scenarios:

-   If there has not been any log output or a command hangs for 10 minutes
-   If the build is still running after 60 minutes for Free Plans or 120 minutes for Paid Plans

When a build is forcefully terminated, the build status will indicate **timeout**.

* * * * *

## Skipping a build

Any changes to your source code will trigger a build automatically on
Shippable. So if you do not want to run build for a particular commit,
then add **[ci skip]** or **[skip ci]** to your commit message.

Our webhook processor will look for the string **[ci skip]** or **[skip
ci]** in the commit message and if it exists, then that particular
webhook build will not be executed.

* * * * *

## Using Shippable with Gitlab or other types of source control

At the moment, Shippable supports repositories hosted either on GitHub
or Bitbucket. However, your development setup may involve using a
different provider or even hosting the repository server on your own. In
both cases, the easiest way to make your code available to Shippable is
to set up a mirror of your repository with either of the supported
services.

As [GitLab](https://about.gitlab.com/) is a very popular choice among
organizations managing their own repositories, the instructions below
outline how to set up a mirror of a repository hosted on GitLab
Community Edition 7.2.1. Other self-hosted solutions can be integrated
in a very similar manner, the only differences being the locations of
the files. If you experience any problems setting up a mirror using a
different technology, please do not hesitate to reach out to us. Please
also note that this method can be applied to mirror repositories using
different VCS than Git or Mercurial, if only an extension to push the
changes to Git is available. Hence, it allows using VCS of your choice
(such as Perforce or SVN) with Shippable.
First step of setting up a mirror is to create a target repository on
either GitHub or Bitbucket. Please note that in the case of Bitbucket,
you can create unlimited number of private repositories for free,
granted that no more than 5 users will have access to them. It makes it
especially appealing to host mirrors then, as you only need to associate
two users (the account you use with Shippable and an extra account for
your repository server) with all the mirrors.

Please write down the Git url of your newly created mirror. For the sake
of convenience, we will use the following urls throughout this guide:

-   GitHub: `git@github.com:Shippable/shippable-mirror-test.git`
-   Bitbucket: `git@bitbucket.org:Shippable/shippable-mirror-test.git`

Next, you need to grant write access for your repository server to the
mirror.

### Granting access to a GitHub mirror

In case of GitHub, it can be done by [adding a deployment
key](https://developer.github.com/guides/managing-deploy-keys/#deploy-keys)
to the repository. GitHub requires the deployment keys to be unique, so
we need to create a dedicated SSH key for every repository.

Switch to the `git` user on your GitLab server and create the key, using
a filename that clearly associates the key with the repository. Leave
the passphrase empty:

```bash
# su - git
$ bash
$ pwd
/var/opt/gitlab
$ ssh-keygen -f .ssh/shippable-mirror_key
```

Next, take the contents of the `.ssh/shippable-mirror_key.pub` file and
add it as a deploy key in the GitHub settings panel for your mirror
repository. To ensure that the right key gets picked when `git`
establishes the connection with the mirror, we will add a special host
entry in the SSH config. Open `/var/opt/gitlab/.ssh/config` file (create
it if it doesn't exist) with your favorite editor and add the following
section:

```bash
Host shippable-mirror
IdentityFile /var/opt/gitlab/.ssh/shippable-mirror_key
HostName github.com
User git
```
Now, connecting to `shippable-mirror` host (replace this alias with your
repository name) will result in establishing a connection with
`github.com` as user `git`, but using our dedicated deployment key. Test
it by issuing the following command (still as `git` user on your GitLab
server):

```bash
$ ssh shippable-mirror
Warning: Permanently added the RSA host key for IP address '192.30.252.129' to the list of known hosts.
PTY allocation request failed on channel 0
Hi Shippable/shippable-mirror-test! You've successfully authenticated, but GitHub does not provide shell access.
Connection to github.com closed.
```

If you see a message like this above, you have successfully set the
deployment key up.

### Granting access to a Bitbucket mirror

In case of Bitbucket, you need to create an SSH key and associate it
with a user account. As it is not advisable to deploy to a remote server
the key that grants access to your private account, we recommended
creating a separate user on Bitbucket.org for authenticating your GitLab
server.

You can create one key for `git` user on your GitLab server and use it
for all the services, but for security reasons you may create a separate
key for Bitbucket. Switch to the `git` user on your GitLab server and
create the key, leaving the passphrase empty:

```bash
# su - git
$ bash
$ pwd
/var/opt/gitlab
$ ssh-keygen -f .ssh/bitbucket_key
```

Next, take the contents of the `.ssh/bitbucket_key.pub` file and [add
the key for it in the account management
panel](https://confluence.atlassian.com/display/BITBUCKET/Add+an+SSH+key+to+an+account).
To ensure that the right key gets picked when `git` establishes the
connection with the mirror, we will add a special host entry in the SSH
config. Open `/var/opt/gitlab/.ssh/config` file (create it if it doesn't
exist) with your favorite editor and add the following section:

```bash
Host bitbucket
IdentityFile /var/opt/gitlab/.ssh/bitbucket_key
HostName bitbucket.org
User git
```

Now, connecting to `bitbucket` host will result in establishing a
connection with `bitbucket.org` as user `git`, but using our dedicated
deployment key. Test it by issuing the following command (still as `git`
user on your GitLab server):

```bash
$ ssh bitbucket
Warning: Permanently added the RSA host key for IP address '131.103.20.168' to the list of known hosts.
PTY allocation request failed on channel 0
logged in as Shippable.

You can use git or hg to connect to Bitbucket. Shell access is disabled.
Connection to bitbucket.org closed.
```

If you see a message like this above, you have successfully set the
deployment key up.

### Setting a git hook

The next step is to add mirror as a remote to the repository on your
GitLab server. Still as `git` user, go to the directory that contains
the repository and issue the following command:

```bash
$ cd /var/opt/gitlab/git-data/repositories/<GitLab namespace for the repo>/<repo name>.git
# for GitHub
$ git remote add mirror --mirror=push shippable-mirror:Shippable/shippable-mirror-test.git
# for Bitbucket
$ git remote add mirror --mirror=push bitbucket:Shippable/shippable-mirror-test.git
```

Next, add the `post-receive` hook in the same directory (you can read
more about `git` hooks [here](http://git-scm.com/docs/githooks.html)).

```bash
$ echo "exec git push --quiet mirror &" >> hooks/post-receive
$ chmod 755 hooks/post-receive
```

You are now all set! After you push new changes to the GitLab, they
whole repository will be automatically mirrored to either GitHub or
Bitbucket. If any errors occur, they should be visible in the output of
your local `git push` command.

### Mirroring Mercurial repository

At the very moment, Shippable supports only Git repositories. This will
likely change in the near future, but in the meantime, the only way to
use a Mercurial repository with Shippable is to set up a Git mirror.

A bridge between Mercurial and Git exists in form of [Hg-Git Mercurial
plugin](http://hg-git.github.io/). This plugin needs to be installed on
the machine from which pushes to Git repositories will be performed
(i.e. developer workstation or server that hosts Mercurial repository).

Installation of the plugin is simple:

1.  Install the plugin package with `easy_install hg-git` or
    `pip install hg-git`.
2.  Add the plugin to `.hgrc` file (either `~/.hgrc` to enable the
    plugin globally or `<repository>/.hg/hgrc` to switch it on for
    individual repository):

```bash
[extensions]
hggit =
```

Then, add the remote endpoint for the Git repository to the list of
paths in the `<repository>/.hg/hgrc` file. The url needs to have
`git+ssh` protocol in order to be picked up by `hg-git` plugin.

```bash
[paths]
default = ssh://hg@bitbucket.org/Shippable/hg-mirror-test
git = git+ssh://git@bitbucket.org/Shippable/hg-mirror-test-git.git
```

Then, it should be possible to send the changesets to the Git repository
with the following command (see the sections above for details how to
configure SSH authentication for GitHub and Bitbucket):

```bash
$ hg push git
pushing to git+ssh://git@bitbucket.org/Shippable/hg-mirror-test-git.git
["git-receive-pack '/Shippable/hg-mirror-test-git.git'"]
searching for changes
adding objects
added 1 commits with 1 trees and 0 blobs
adding reference refs/heads/master
```

```bash
[paths]
default = ssh://hg@bitbucket.org/Shippable/hg-mirror-test
git = git+ssh://git@bitbucket.org/Shippable/hg-mirror-test-git.git
```

Then, it should be possible to send the changesets to the Git repository
with the following command (see the sections above for details how to
configure SSH authentication for GitHub and Bitbucket):

```bash
$ hg push git
pushing to git+ssh://git@bitbucket.org/Shippable/hg-mirror-test-git.git
["git-receive-pack '/Shippable/hg-mirror-test-git.git'"]
searching for changes
adding objects
added 1 commits with 1 trees and 0 blobs
adding reference refs/heads/master
```

Next, we can create Mercurial hooks to perform the push automatically.
For example, when configuring the mirror on Mercurial repository server,
the best idea is to use `changegroup` hook that gets invoked after every
group of changesets that arrive at the repository.

Add the following section to `<repository>/.hg/hgrc` file:

```bash
[hooks]
changegroup = hg bookmark -f -r tip master && hg push -r tip git
```

The first command in the snippet above moves the `master` bookmark that
is used by `hg-git` to track the remote `master` branch from the Git
repository.

There are a few caveats related to branch handling differences between
Mercurial and Git. Git branches are most akin to Mercurial bookmarks and
`hg-git` handles them this way. No good equivalent of Mercurial branches
exist in Git, so these will not be synchronized to the Git repository,
unless an extra bookmark is created to track the branch. Please refer to
`hg-git` documentation for more details.

One particular problem connected to this issue is that Mercurial
bookmarks are bound to the local repository and are not pushed. This
means that the bookmarks are invisible to 'central' repository that has
the hook in place (and sends the changes to Git). The only solution here
is to clone (i.e. fork) Mercurial repositories and keep separate Git
mirrors for them, instead of creating branches.


