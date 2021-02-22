# New method to install Adobe CC series on case-sensitive volumes

As old tricks not working on new versions of adobe software and macOS since `FSGetVolumeParms` function deprecated, I found an even more simple way.<br>
No more coding and compiling needed!<br>
You will need another macOS running on an case-insensitive volume though. Another computer or virtual machine with diffent macOS version would be fine.<br>

## First, install your preferred adobe cc software on another macOS.

## Copy application files and libraries from installed one to case-sensitive one.

You can copy them via network share, or usb external drive, directly or compressed.<br>
Admin permissions needed.<br>
Put them exactly to the same path.<br>
```
1. /Applications/Adobe * (depends on which software you wanna use)
2. /Applications/Utilities/Adobe * (all of them)
3. /Library/Application Support/Adobe
```

## Fix attributes. 

Since macOS may add quarantine attribute to files we copied from another computer, we should remove them!<br>
And we should enable all sources.<br>
Run commands below in `terminal`.<br>
```
sudo spctl --master-disable
cd /Applications
xattr -dr com.apple.quarantine *
cd /Library/Application\ Support
xattr -dr com.apple.quarantine *
```
<br>
You may be informed that some files are not found, it's just fine.<br>

## You should be able to run your adobe software now!

This method is tested on macOS mojave (installed adobe 2021), big sur (case-sensitive).<br>


#
#
#
# Old readme from tzvetkoff below, for reference
#

# Installing Adobe CS6 on case-sensitive drives (Mac OS X)

Well, everybody knows that Adobe are a **[censored]** company.
Their products are the defacto standard for image/video editing and designing, but their codebase really suck. No excuses.

The problem addressed here is that Creative Studio™ refuses to install on a case-sensitive drive on Mac OS X.
And it doesn't just refuse to install on a case-sensitive drive, but it also requires to install on your *boot* drive as well! Srsly?

Well, there's a solution. I've just stumbled upon [this](https://bitbucket.org/lokkju/adobe_case_sensitive_volumes), and I'm really anxious to share it.
I've forked the code to update it for CS6.

## Prerequisites

1.  `Xcode`

    You can install it from [AppStore](https://itunes.apple.com/app/xcode/id497799835).
2.  Command Line Tools for Xcode.

    You can install it from Xcode's `Preferences` -> `Downloads`.

## A step-by-step installation instructions

1.  Create a `.sparsebundle` pseudo-image to install CS6:

    ``` bash
    mkdir -p ~/Stuff/Adobe
    cd ~/Stuff/Adobe
    hdiutil create -size 15g -type SPARSEBUNDLE -nospotlight -volname 'Adobe CS6 SparseBundle' -fs 'Journaled HFS+' ~/Stuff/Adobe/Adobe_CS6_SparseBundle.sparsebundle
    ```

2.  Mount the newly created image and create a `/Adobe` directory inside

    ``` bash
    open ~/Stuff/Adobe/Adobe_CS6_SparseBundle.sparsebundle
    mkdir -p /Volumes/Adobe\ CS6\ SparseBundle/Adobe
    ```

3.  Create an extra `/Applications/Adobe` folder on the boot drive (we will trick the installer with this temporary directory.)
    ``` bash
    mkdir -p /Applications/Adobe
    ```

4.  Get the hack, compile it, and run it

    OK, at this point you'll need to edit the `Makefile` and set the `CS6_INSTALLER_PATH` variable to point to the `Install.app` directory.
    The current one tries to find it automatically, but it *may* fail...

    ``` bash
    cd ~/Stuff/Adobe
    git clone git://github.com/tzvetkoff/adobe_case_sensitive_volumes.git
    cd adobe_case_sensitive_volumes
    make
    sudo make run
    ```

5.  When asked, select `/Applications/Adobe` for installation directory rather than just `/Applications`, but **don't** click the `Install` button!!
    Remember, **don't** click the `Install` button just yet.

6.  Now, time to do one more hack - remove the `/Applications/Adobe` directory and replace it with a symlink to the `/Adobe` directory from the SparseBundle.

    ``` bash
    rm -rf /Applications/Adobe
    ln -s /Volumes/Adobe\ CS6\ SparseBundle/Adobe/ /Applications/Adobe
    ```

7.  Now click the `Install` button

8.  You can now safely delete the intermediate files and probably move the SparseBundle somewhere easier to mount by just clicking it (the Desktop, probably?)

    ``` bash
    mv ~/Stuff/Adobe/Adobe_CS6_SparseBundle.sparsebundle ~/Desktop/Adobe_CS6_SparseBundle.sparsebundle
    rm -rf ~/Stuff/Adobe
    ```

9.  That's it!

    Just remember that you'll need to mount the SparseBundle every time you need to use Adobe's products.


## Thanks

[lokkju](https://bitbucket.org/lokkju), for writing [that awesome article and code](https://bitbucket.org/lokkju/adobe_case_sensitive_volumes) to start from
