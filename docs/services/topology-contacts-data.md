Topology and Contacts Data
==========================

This is internal documentation intended for OSG Operations staff.
It contains information about the data provided by <https://topology.opensciencegrid.org>.

The topology data for the service is in <https://github.com/opensciencegrid/topology>, in the `projects/`, `topology/`, and `virtual-organizations/` subdirectories.
The contacts data is in <https://bitbucket.org/opensciencegrid/contact/>, in `contacts.yaml`.


Topology Data
-------------

Admins may request changes to data in the topology repo via either a GitHub pull request or a Freshdesk ticket.
These changes can be to a project, a VO, or a resource.
The [registration document](https://opensciencegrid.org/docs/common/registration) and
[topology README document](https://github.com/opensciencegrid/topology/README.md) should tell them how to do that.

The CI checks should catch most errors but you should still review the YAML changes.
Certain things to check are:

-   Do contact names and IDs match what's in the contacts data?
    (See below for instructions on how to get that information.)
    If the person is not in the contacts data, you will need to add them before approving the PR.

-   Is the PR submitter authorized to make changes to that project/VO/resource?
    Can you match them to a person affiliated with that project/VO/site?
    (The contacts data now includes the GitHub usernames for some people.
    See below for instructions on how to get that information.)


### Fixing issues

You may run into CI failures or other issues during the review that require changes to the PR.
If you believe the admin can easily make the changes themselves, request changes via GitHub comments or a review message.
Otherwise, make the changes yourself.

Fixing issues yourself requires a local clone of the topology repo.
Make sure `master` branch in your clone is up-to-date with the `master` branch in the `opensciencegrid` repo.

The easiest method of making changes is to directly push the changes to their PR branch.
You may only do this if both of the following are true:

-   They have enabled pushes by maintainers to their branch.
    If this is true, you should see text in the GitHub interface like this:

    > Add more commits by pushing to the `<BRANCH>` branch on `<USER>`/topology.

-   Their branch is not `master`.
    Modifying someone else's `master` branch gives them additional work to in order to sync up their local copies,
    so as a courtesy, we do not do it.

If both are true, follow the instructions in the [direct pushes to their PR branch](#direct-pushes-to-their-pr-branch) section.
Otherwise, follow the instructions in the [creating your own PR](#creating-your-own-pr) section.


#### Direct pushes to their PR branch

You may make changes and directly push to their branch.
This is the easiest method because your changes will show up immediately in the PR, and so will be tested by the CI scripts.

The procedure is as follows:

1.  Add their repo as a remote and fetch their changes:

        :::console
        $ git remote add <USER> https://github.com/<USER>/topology
        $ git fetch <USER>

1.  Create your own branch that tracks their PR branch:

        :::console
        $ git checkout -b <THEIR PR BRANCH>

    This both creates your a branch and sets it to follow the branch they created their PR from.

1.  Make and commit your changes.

1.  Push your changes to their branch:

        :::console
        $ git push

After performing these steps, their PR will be updated with your changes.


#### Creating your own PR

You may create your own PR that contains both the changes in their PR and your own fixes.

These instructions are based on the "command line instructions" link that GitHub provides next to the "Merge pull request" button.

1.  Check out a new branch based on master:

        :::console
        $ git checkout -b pr-1234-fixed master

1.  Merge the user's PR into your new branch:

        :::console
        $ git pull https://github.com/<SUBMITTING USER>/topology.git <THEIR BRANCH>

1.  Make your fixes and commit them.

1.  Instead of merging to `master`, push your new branch to your GitHub fork.

1.  Create your own PR from this branch.
    For reference, put the original PR number in your PR.
    Once your own PR is merged, the original PR will be marked as merged as well.


Contacts Data
-------------

Contacts data is either available as XML in <https://topology.opensciencegrid.org/miscuser/xml> or
editable YAML in <https://bitbucket.org/opensciencegrid/contact/>, in `contacts.yaml`.
The YAML file contains sensitive information and is only visible to people with access to that repo.


### Getting access to the contact repo

The contacts repo is hosted on BitBucket.
You will need an Atlassian account for access to BitBucket.
The account you use for OSG JIRA should work.
Once you have an account, request access from Brian Lin, Mat Selmeci, or Derek Weitzel.
You should then be able to go to <https://bitbucket.org/opensciencegrid/contact/>.


### Using the contact repo

BitBucket is similar to GitHub except you don't make a fork of the contact repo,
you just clone it to your local machine.
This means that any pushes go directly to the main repo instead of your own fork.

!!! danger
    Don't push to master.
    For any changes, always create your own branch, push your changes to that branch, then make a pull request.
    Have someone else review and merge your pull request.

All contact data is stored in `contacts.yaml`.
The contact info is keyed by a 40-character hexadecimal ID which was generated from their email address when they were first added.
An example entry is:
```yaml
25357f62c7ab2ae11ddda1efd272bb5435dbfacb:
# ^ this is their ID
  FullName: Example A. User
  Profile: This is an example user.
  GitHub: ExampleUser
  # ContactInformation data requires authorization to view
  ContactInformation:
    DNs:
    - ...
    IM: ...
    PrimaryEmail: user@example.net
    PrimaryPhone: ...
```

When making changes to the contact data, first see if a contact is already in the YAML file.
Search the YAML file for their name.
Be sure to try variations of their name if you don't find them --
someone may be listed as "Dave" or "David", or have a middle name or middle initial.

Follow the instructions below for adding or updating a contact, as appropriate.


#### Adding a new contact

Before adding a new contact, verify their VO, site, or project affiliation,
and get some brief profile information about them.
The profile does not need to be detailed -- examples are "Neutrino physicist at Fermilab working on the DUNE project"
or "Administrator for GridUNESP CEs."

After obtaining this information, fill out the values in `template-contacts.yaml` and add it to `contacts.yaml`.
To get the hash used as the ID, run `email-hash` on their email address.
For example:

```command
$ cd contact  # this is your local clone of the "contact" repo
$ bin/email-hash user@example.net
25357f62c7ab2ae11ddda1efd272bb5435dbfacb
```
Then your new entry will look like
```yaml
25357f62c7ab2ae11ddda1efd272bb5435dbfacb:
    FullName: Example A. User
    ....
```

The `FullName` and `Profile` fields in the main section,
and the `PrimaryEmail` field in the `ContactInformation` section are required.
The `PrimaryEmail` field in the `ContactInformation` section should match the hash that you used for the ID.

In addition, if they will be making pull requests against the topology repo,
e.g. for updating site information, reporting downtime, or updating project or VO information,
obtain their GitHub username and put it in the `GitHub` field.


#### Editing a contact

Once you have found a contact in the YAML file, edit the attributes by hand.
If you want to add information that is not present for that contact, look at `template-contacts.yaml` to find out what the attributes are called.

!!!note
    The ID of the contact never changes, even if the user's `PrimaryEmail` changes.

!!! important
    If you change the contact's `FullName`, you **must** make the same change to every place that the contact
    is mentioned in the `topology` repo.
    Get the contact changes merged in first.

