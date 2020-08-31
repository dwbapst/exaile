
Release process
===============

This is an attempt to document what needs to be done in order to create a
release for Exaile.


Step 1: Translations
--------------------

Ensure that the translations from `weblate <https://hosted.weblate.org/projects/exaile/master/>`_
are merged. Generally, this should happen automatically. It's probably easiest
to check via the command line in your repo.

If you haven't already, add weblate to your git remotes:

.. code-block:: sh

    $ git remote add weblate git://git.weblate.org/exaile.git

Check to see if the weblate repo has the same commits as the exaile
repo (assuming that origin is pointing at the main exaile repo).

.. code-block:: sh

    $ git fetch weblate
    $ git fetch origin
    $ git log -1 origin/master
    $ git log -1 weblate/master

If they're equivalent, then we're all set. If not, then figure out what needs
to be done to get them merged.


Step 2: gather release notes
----------------------------

There's a lot of ways to go about this. I find that the easiest way to see
what has changed is go to GitHub releases page, find the last release, and
click on XXX commits since this release. Then you can browse the list of
commits and pick out anything worth noting there.


Step 3: Tag the release locally
-------------------------------

Make sure you have the correct thing checked out in your git tree, and then
tag the release. 

.. code-block:: sh

    $ git tag -a RELEASE_VERSION

You can either add some release notes as the tag message or just write "Exaile
RELEASE_VERSION".


Step 4: Update plugin versions (if needed)
------------------------------------------

If the PLUGININFO files still refer to the old version number, update them:

.. code-block:: sh

    $ tools/plugin_tool.py fix

This currently must not be done from Windows because it will clobber the line
separators.

Note that the new version number in the PLUGININFO files does not include any
alpha/beta/rc label, so once you've done it for version a.b.c-alpha1 you don't
need to do this step again for version a.b.c.

Commit the changes and re-tag the release:

    $ git add plugins/*/PLUGININFO
    $ git commit
    $ git tag -d RELEASE_VERSION
    $ git tag -a RELEASE_VERSION


Step 5: Push the tag
--------------------

.. code-block:: sh

    $ git push origin RELEASE_VERSION

**Do not push to master** before doing this; our auto-release setup only works
when there is a new commit associated with a tag. If you've made this mistake,
delete the tag and create an empty commit:

.. code-block:: sh

    $ git tag -d RELEASE_VERSION
    $ git push -d origin RELEASE_VERSION
    $ git commit --allow-empty

then re-tag and re-push.


Step 6: Release the release
---------------------------

Once the tag is in GitHub, Travis CI will build a Linux dist and AppVeyor
will build a Windows installer and upload it to GitHub releases as a draft.
Once the assets are uploaded, you can edit the draft release and paste in
your release notes, then click 'Publish Release'.


Final steps
-----------

Once the tag is built and released, you can push to the master branch.

Next, close out the milestone (if applicable) on GitHub.


Sending release notices
-----------------------

After a release, we should:

* Update website (hosted via GitHub Pages at https://github.com/exaile/exaile.github.io)

  - Update versions in ``_config.yml``
  - Add a new post to ``_posts``
 
* Send email to exaile-dev and exaile-users mailing lists with the release notes

* Update the channel topic on IRC (``/msg ChanServ topic #exaile ...``)
