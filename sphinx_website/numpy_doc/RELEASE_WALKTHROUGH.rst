This is a walkthrough of the NumPy 1.21.0 release on Linux, modified for
building with GitHub Actions and cibuildwheels and uploading to the
`anaconda.org staging repository for NumPy <https://anaconda.org/multibuild-wheels-staging/numpy>`_.
The commands can be copied into the command line, but be sure to replace 1.21.0
by the correct version. This should be read together with the
:ref:`general release guide <prepare_release>`.

Facility preparation
====================

Before beginning to make a release, use the ``requirements/*_requirements.txt`` files to
ensure that you have the needed software. Most software can be installed with
pip, but some will require apt-get, dnf, or whatever your system uses for
software. You will also need a GitHub personal access token (PAT) to push the
documentation. There are a few ways to streamline things:

- Git can be set up to use a keyring to store your GitHub personal access token.
  Search online for the details.
- You can use the ``keyring`` app to store the PyPI password for twine. See the
  online twine documentation for details.


Prior to release
================

Add/drop Python versions
------------------------

When adding or dropping Python versions, three files need to be edited:

- .github/workflows/wheels.yml  # for github cibuildwheel
- .travis.yml  # for cibuildwheel aarch64 builds
- setup.py  # for classifier and minimum version check.

Make these changes in an ordinary PR against main and backport if necessary.
Using the `BLD:` prefix (build label) for the commit summary will cause the
wheel builds to be run so that the changes will be tested, We currently release
wheels for new Python versions after the first Python rc once manylinux and
cibuildwheel support it. For Python 3.11 we were able to release within a week
of the rc1 announcement.


Backport pull requests
----------------------

Changes that have been marked for this release must be backported to the
maintenance/1.21.x branch.


Make a release PR
=================

Five documents usually need to be updated or created for the release PR:

- The changelog
- The release-notes
- The ``.mailmap`` file
- The ``pyproject.toml`` file
- The ``pyproject.toml.setuppy`` file # 1.26.x only

These changes should be made in an ordinary PR against the maintenance branch.
The commit message should contain a ``[wheel build]`` directive to test if the
wheels build. Other small, miscellaneous fixes may be part of this PR. The
commit message might be something like::

    REL: Prepare for the NumPy 1.20.0 release

    - Create 1.20.0-changelog.rst.
    - Update 1.20.0-notes.rst.
    - Update .mailmap.
    - Update pyproject.toml
    - Update pyproject.toml.setuppy

    [wheel build]


Generate the changelog
----------------------

The changelog is generated using the changelog tool::

    $ spin changelog $GITHUB v1.20.0..maintenance/1.21.x > doc/changelog/1.21.0-changelog.rst

where ``GITHUB`` contains your GitHub access token. The text will need to be
checked for non-standard contributor names and dependabot entries removed. It
is also a good idea to remove any links that may be present in the PR titles
as they don't translate well to markdown, replace them with monospaced text. The
non-standard contributor names should be fixed by updating the ``.mailmap``
file, which is a lot of work. It is best to make several trial runs before
reaching this point and ping the malefactors using a GitHub issue to get the
needed information.


Finish the release notes
------------------------

If there are any release notes snippets in ``doc/release/upcoming_changes/``,
run ``spin docs`` to build the docs, incorporate the contents of the generated
``doc/source/release/notes-towncrier.rst`` file into the release notes file
(e.g., ``doc/source/release/2.3.4-notes.rst``), and delete the now-processed
snippets in ``doc/release/upcoming_changes/``. This is safe to do multiple
times during a release cycle.

The generated release note will always need some fixups, the introduction will
need to be written, and significant changes should be called out. For patch
releases the changelog text may also be appended, but not for the initial
release as it is too long. Check previous release notes to see how this is
done.


Set the release version
-----------------------

Check the ``pyproject.toml`` and ``pyproject.toml.setuppy`` files and set the
release version if needed::

    $ gvim pyproject.toml pyproject.toml.setuppy


Check the ``pavement.py`` and ``doc/source/release.rst`` files
--------------------------------------------------------------

Check that the ``pavement.py`` file points to the correct release notes. It should
have been updated after the last release, but if not, fix it now. Also make
sure that the notes have an entry in the ``release.rst`` file::

    $ gvim pavement.py doc/source/release.rst


Release walkthrough
===================

Note that in the code snippets below, ``upstream`` refers to the root repository on
GitHub and ``origin`` to its fork in your personal GitHub repositories. You may
need to make adjustments if you have not forked the repository but simply
cloned it locally. You can also edit ``.git/config`` and add ``upstream`` if it
isn't already present.


1. Prepare the release commit
-----------------------------

Checkout the branch for the release, make sure it is up to date, and clean the
repository::

    $ git checkout maintenance/1.21.x
    $ git pull upstream maintenance/1.21.x
    $ git submodule update
    $ git clean -xdfq

Sanity check::

    $ python3 -m spin test -m full

Tag the release and push the tag. This requires write permission for the numpy
repository::

    $ git tag -a -s v1.21.0 -m"NumPy 1.21.0 release"
    $ git push upstream v1.21.0

If you need to delete the tag due to error::

   $ git tag -d v1.21.0
   $ git push --delete upstream v1.21.0


2. Build wheels
---------------

Tagging the build at the beginning of this process will trigger a wheel build
via cibuildwheel and upload wheels and an sdist to the staging repo. The CI run
on github actions (for all x86-based and macOS arm64 wheels) takes about 1 1/4
hours. The CI runs on cirrus (for aarch64 and M1) take less time. You can check
for uploaded files at the `staging repository`_, but note that it is not
closely synched with what you see of the running jobs.

If you wish to manually trigger a wheel build, you can do so:

- On github actions -> `Wheel builder`_ there is a "Run workflow" button, click
  on it and choose the tag to build
- On Cirrus we don't currently have an easy way to manually trigger builds and
  uploads.

If a wheel build fails for unrelated reasons, you can rerun it individually:

- On github actions select `Wheel builder`_ click on the commit that contains
  the build you want to rerun. On the left there is a list of wheel builds,
  select the one you want to rerun and on the resulting page hit the
  counterclockwise arrows button.
- On cirrus we haven't figured it out.

.. _`staging repository`: https://anaconda.org/multibuild-wheels-staging/numpy/files
.. _`Wheel builder`: https://github.com/numpy/numpy/actions/workflows/wheels.yml


3. Download wheels
------------------

When the wheels have all been successfully built and staged, download them from the
Anaconda staging directory using the ``tools/download-wheels.py`` script::

    $ cd ../numpy
    $ mkdir -p release/installers
    $ python3 tools/download-wheels.py 1.21.0


4. Generate the README files
----------------------------

This needs to be done after all installers are downloaded, but before the pavement
file is updated for continued development::

    $ paver write_release


5. Upload to PyPI
-----------------

Upload to PyPI using ``twine``. A recent version of ``twine`` of is needed
after recent PyPI changes, version ``3.4.1`` was used here::

    $ cd ../numpy
    $ twine upload release/installers/*.whl
    $ twine upload release/installers/numpy-1.21.0.tar.gz  # Upload last.

If one of the commands breaks in the middle, you may need to selectively upload
the remaining files because PyPI does not allow the same file to be uploaded
twice. The source file should be uploaded last to avoid synchronization
problems that might occur if pip users access the files while this is in
process, causing pip to build from source rather than downloading a binary
wheel. PyPI only allows a single source distribution, here we have
chosen the zip archive.


6. Upload files to GitHub
-------------------------

Go to `<https://github.com/numpy/numpy/releases>`_, there should be a ``v1.21.0
tag``, click on it and hit the edit button for that tag. There are two ways to
add files, using an editable text window and as binary uploads. Start by
editing the ``release/README.md`` that is translated from the rst version using
pandoc. Things that will need fixing: PR lines from the changelog, if included,
are wrapped and need unwrapping, links should be changed to monospaced text.
Then copy the contents to the clipboard and paste them into the text window. It
may take several tries to get it look right. Then

- Upload ``release/installers/numpy-1.21.0.tar.gz`` as a binary file.
- Upload ``release/README.rst`` as a binary file.
- Upload ``doc/changelog/1.21.0-changelog.rst`` as a binary file.
- Check the pre-release button if this is a pre-releases.
- Hit the ``{Publish,Update} release`` button at the bottom.


7. Upload documents to numpy.org (skip for prereleases)
-------------------------------------------------------

.. note:: You will need a GitHub personal access token to push the update.

This step is only needed for final releases and can be skipped for pre-releases
and most patch releases. ``make merge-doc`` clones the ``numpy/doc`` repo into
``doc/build/merge`` and updates it with the new documentation::

    $ git clean -xdfq
    $ git co v1.21.0
    $ rm -rf doc/build  # want version to be current
    $ python -m spin docs merge-doc --build
    $ pushd doc/build/merge

If the release series is a new one, you will need to add a new section to the
``doc/build/merge/index.html`` front page just after the "insert here" comment::

    $ gvim index.html +/'insert here'

Further, update the version-switcher json file to add the new release and
update the version marked `(stable)`::

    $ gvim _static/versions.json

Otherwise, only the ``zip`` link should be updated with the new tag name. Since
we are no longer generating ``pdf`` files, remove the line for the ``pdf``
files if present::

    $ gvim index.html +/'tag v1.21'

You can "test run" the new documentation in a browser to make sure the links
work::

    $ firefox index.html  # or google-chrome, etc.

Update the stable link and update::

    $ ln -sfn 1.21 stable
    $ ls -l  # check the link

Once everything seems satisfactory, update, commit and upload the changes::

    $ python3 update.py
    $ git commit -a -m"Add documentation for v1.21.0"
    $ git push
    $ popd


8. Reset the maintenance branch into a development state (skip for prereleases)
-------------------------------------------------------------------------------

Create release notes for next release and edit them to set the version. These
notes will be a skeleton and have little content::

    $ cp doc/source/release/template.rst doc/source/release/1.21.1-notes.rst
    $ gvim doc/source/release/1.21.1-notes.rst
    $ git add doc/source/release/1.21.1-notes.rst

Add new release notes to the documentation release list and update the
``RELEASE_NOTES`` variable in ``pavement.py``::

    $ gvim doc/source/release.rst pavement.py

Update the ``version`` in ``pyproject.toml`` and ``pyproject.toml.setuppy``::

    $ gvim pyproject.toml pyproject.toml.setuppy

Commit the result::

    $ git commit -a -m"MAINT: prepare 1.21.x for further development"
    $ git push origin HEAD

Go to GitHub and make a PR. It should be merged quickly.


9. Announce the release on numpy.org (skip for prereleases)
-----------------------------------------------------------

This assumes that you have forked `<https://github.com/numpy/numpy.org>`_::

    $ cd ../numpy.org
    $ git checkout main
    $ git pull upstream main
    $ git checkout -b announce-numpy-1.21.0
    $ gvim content/en/news.md

- For all releases, go to the bottom of the page and add a one line link. Look
  to the previous links for example.
- For the ``*.0`` release in a cycle, add a new section at the top with a short
  description of the new features and point the news link to it.

commit and push::

    $ git commit -a -m"announce the NumPy 1.21.0 release"
    $ git push origin HEAD

Go to GitHub and make a PR.


10. Announce to mailing lists
-----------------------------

The release should be announced on the numpy-discussion, scipy-devel, and
python-announce-list mailing lists. Look at previous announcements for the
basic template. The contributor and PR lists are the same as generated for the
release notes above. If you crosspost, make sure that python-announce-list is
BCC so that replies will not be sent to that list.


11. Post-release update main (skip for prereleases)
---------------------------------------------------

Checkout main and forward port the documentation changes::

    $ git checkout -b post-1.21.0-release-update
    $ git checkout maintenance/1.21.x doc/source/release/1.21.0-notes.rst
    $ git checkout maintenance/1.21.x doc/changelog/1.21.0-changelog.rst
    $ git checkout maintenance/1.21.x .mailmap  # only if updated for release.
    $ gvim doc/source/release.rst  # Add link to new notes
    $ git status  # check status before commit
    $ git commit -a -m"MAINT: Update main after 1.21.0 release."
    $ git push origin HEAD

Go to GitHub and make a PR.


12. Update oldest-supported-numpy
---------------------------------

If this release is the first one to support a new Python version, or the first
to provide wheels for a new platform or PyPy version, the version pinnings
in https://github.com/scipy/oldest-supported-numpy should be updated.
Either submit a PR with changes to ``setup.cfg`` there, or open an issue with
info on needed changes.

