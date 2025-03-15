# GIMP Flatpak

This is the official flatpak repository by the upstream GIMP team.

Though you can report bugs specific to this package here, you should
report non-package specific bugs to our main tracker:
https://gitlab.gnome.org/GNOME/gimp/

## How to do a flatpak release

A step by step procedure for the package maintainer(s) and contributors
when a new build needs to be released.

* Create a branch off the main branch (`master` for a stable release,
  `beta` for a development release), ideally a few days before the
  expected release date. For instance for a stable release:

```
$ git checkout master
$ git pull
$ git checkout -b wip/release/2-10-24
```

* In the manifest `org.gimp.GIMP.json`, update:

  - the "runtime-version" if needed.
  - any dependency module if not already updated by flathubbot or if they are
    behind the nightly manifest. You will find detailed and up-to-date info about
    manually updating modules on GIMP upstream repo: https://gitlab.gnome.org/GNOME/gimp/-/blob/master/build/linux/flatpak/README.md?ref_type=heads
  - the "version" of "org.gimp.GIMP.Plugin" extension with GIMP API version (`major`).
  - the "version" of "org.gimp.GIMP.Manual" extension with GIMP app version (`major.minor`)

* If the GIMP release has not been tagged yet, set the "gimp" module to
  `HEAD` of the source by changing "commit" (the same goes for babl and GEGL):

```
                 {
                     "type": "git",
                     "url": "https://gitlab.gnome.org/GNOME/gimp.git",
                     "commit": "full_commit_sha_here"
                 }
```

* Push the local release branch to the repo:

```
$ git push -u origin wip/release/2-10-24
```

* Make a pull request of it in: https://github.com/flathub/org.gimp.GIMP/pulls
  
  A build will be automatically triggered and the build bot will notify
  you through comments on the build startup, success or failure.
  The main build interface is: https://flathub.org/builds/

  Note: if any build issue arises which is not because of the manifest
  (temporary build server failure, failed archive download, etc.), you
  can request a new build by adding a command saying only "bot, build"
  in the pull request.

* If you had to update the "version" of "org.gimp.GIMP.Plugin" or the
  "runtime-version" (as previously described), also notify if possible
  maintainers of various known plug-ins, i.e. currently only Hubert
  (@hfiguiere), because the plug-ins may have to be rebuilt too. Making
  a comment to the pull request @-naming people's nickname is probably
  enough.

* Keep in touch with GIMP maintainer(s) to change again GIMP 'commit'
  a few hours before source release tag if needed, because the previous
  CI build was based on `HEAD` of the source from a few days before.

  Repeat as long they fixed all the eventual bugs.

* If all goes fine, wait for GIMP maintainer(s) to tag the main
  repository. When they do, switch the manifest to the new tag and
  corresponding commit, for instance for GIMP 2.10.22, it was:

```
                {
                    "type": "git",
                    "url": "https://gitlab.gnome.org/GNOME/gimp.git",
                    "tag": "GIMP_2_10_22",
                    "commit": "0c8a7891f75a9f41cd36fc0efe2ad506a53d0a15"
                },
```

* Push to the remote, wait for the CI build (it is mandatory), then merge.

* To be perfectionist, you may want to check https://flathub.org/builds/
  just in case some unexpected error occurs. You can also cancel the
  "Test" CI build (leave only the "Official" build) by logging with your
  Github account on Flathub build page, if you merged before the CI
  build ended.

  Also to speed up the release, you can manually publish the build after
  success, though it is not mandatory (Flathub will automatically
  publish after a few hours).

* Enjoy the rest of the day.

## Maintainers

This build is maintained by the following people:

* Upstream GIMP maintainer and original flatpak package author: @Jehan.
* Additional package maintainers: @hfiguiere, @HarryMichal, @novomesk and
  @brunvonlope.
* Flathub maintainers.
