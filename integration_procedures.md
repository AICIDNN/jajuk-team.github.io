---
title: Integration procedures
layout: default
---

# Integration procedures

## New langpack
* Add register in ``LocaleManager.LOCALES``
* Add a language in [Features / Supported languages ](/features.html)
* Add a ``Language_desc_`` line in ``jajuk.properties``
* Check the language has a auto-description (a ``Language_desc_`` line for its language)
* Add a langpack in ``jajuk.nsis``
* Commit the flag in ``resources/icons/16x16``
* Add the new langpack into QDWizard Langpack class

QDWizard specific :

* Edit ``history.apt``
* Generate and deploy : ``mvn deploy``

## Library change

### New library
- Add jar in ``jajuk/jajuk/lib`` directory
- Add jar in ``MANIFEST``
- Change ``pom.xml`` maven file for dependencies
- Update [Dependencies](/legals.html)

### Library upgrade
- Change ``/packaging/MANIFEST`` (check new versions)
- Change pom.xml maven file for dependencies

### Library removal
- Remove file from ``lib`` directory
- Change ``MANIFEST`` (check new versions)
- Change ``pom.xml`` maven file for dependencies
- Update [Dependencies](/legals.html)

## Build a release
<div class='info'>
[major] tasks applies on x or x.y releases only like 2.0 or 1.7 , 
[minor] applies to x.y.z fix releases like 1.6.7 ,
[RC] applies to release candidates
</div>

### [major] Legals
- Update the Dependencies and Derivative sections in [Legals](/legals.html)
- Paste the Dependencies and Derivative sections into ``src/legals`` txt files
- Update ``AUTHORS.txt`` file sorted by commits numbers using :
``git log --format='%aN <%aE>' | awk '{arr[$0]++} END{for (i in arr){print arr[i], i;}}' | sort -rn | cut -d\  -f2-``

### Code
<div class='info'>We assume that we are about to release branches created using [git-flow](http://jeffkreeftmeijer.com/2010/why-arent-you-using-git-flow/).</div>

- [all] Refresh workspace, make sure to pull every commit
- [all] Check copyright year in ``Const.java``
- [major] If mplayer binary changed
  - update file on jajuk.info server at ``/var/www/repository.jajuk.info`` (used by the ant script to build OSX and Windows packages)
  - Update the urls in ``build.xml`` / "Download_mplayer" target
  - Change ``MPLAYER_WINDOWS_EXE_SIZE`` constant value in ``Const`` class
- [all] Commit and push all changes in jajuk project
- [all] Check stashed code
  - ``git stash list``
  - ``git stash show -p stash@{<n>}``
- [major] Cleanup code (imports, formating and clean-up)
- [minor,major] Search for ``System.out`` (14 in ``src/main/java`` so far)
- [major] Detect and drop useless labels from every langpack (``jajuk.properties`` files)
- [major] Change release note URL in ``src/packaging/NSIS/jajuk.nsis``
- - [major] Change README notes version in ``src/doc/README.html``
- [RC] Merge fixes into develop branch (before bumping the release so we don't have to restore them in the develop branch):
``git rebase release/release-<release>``
- [minor,major] Update the ``src/packaging/pad/jajuk_pad.xml`` file still in release branch (date + version:+ exe url; 5 lines to change).
<div class='info'>The jajuk_pad.xml file to copied from master branch to <a href='http://repository.jajuk.info'>http://repository.jajuk.info</a> at each 
build. Version check from users (if enabled) then uses our own server and doesn't charge the Gihub ones.</div>
- [all] In ``build.xml`` and ``pom.xml`` of the hotfix[minor] or the release[major] branch, bump version :
  - Set the last ``version``.
  - ``type='final'``
  - Change version in ``pom.xml`` (remove ``dev``)
  - Commit the bump : ``git commit -a -m "Bump version for release x.y.z"``
  - [major] ``git-flow release finish release-x_y``
  - [minor] ``git-flow hotfix finish release-x_y`` (Changes from hotfix branch will be merged back to master AND develop branches. It should be avoided but if you already reported the changes or you can't do the merge, you can make the merge, commit it (even with conflicts) and then revert it from develop branch using : ``git revert -m 1 HEAD`` )
- Push : ``git push origin``
- Launch the final build from the CI server
- Disable the maintenance job from jenkins
- [all] Test it a minima under several OS
- [all] push tags (git-flow already tagged using the branch name) : ``git push origin --tags``
- [all] Update pad file into the web server : ``setup_pad.sh``
 
- [minor,major] Release on SF
  - Add files (6 files) from jajuk server :
<pre>
cd /tmp/jajuk-dist/packages
sftp bflorat,jajuk@frs.sourceforge.net
cd  /home/pfs/project/j/ja/jajuk/jajuk/
mkdir {release}
cd {release}
put *
exit
</pre>

- Change default OS for each SF file at [http://sourceforge.net/projects/jajuk/files/jajuk/](http://sourceforge.net/projects/jajuk/files/jajuk/)
- [major] Update the manual
- [major] Create a README page : [http://jajuk.info/index.php/Release_notes_x.y](http://jajuk.info/index.php/Release_notes_x.y)
- [minor,major] Change Download and Main page
- [minor,major] Check website (links, pages...)
- [all] Close bugs, known issues and Features

### Others
- [all] Check pad url to the .exe ( http://repository.jajuk.info/jajuk_pad.xml )
- [all] Make annonce on G+ and twitter
- [all] Send a message in the developer list
- [major] If ``default_webradios.xml`` list changed, update file at [http://svn2.assembla.com/svn/common-jukebox/common-jukebox/src/main/resources/preset_radios.xml](http://svn2.assembla.com/svn/common-jukebox/common-jukebox/src/main/resources/preset_radios.xml)

## Prepare the next version after a release
These actions should be done before or just after a major release to create a new major version environment.
 
- Create a maintenance branch
- Update ``Const.TEST_VERSION`` and ``Const.JAJUK_CODENAME`` constants
- Change the POM version