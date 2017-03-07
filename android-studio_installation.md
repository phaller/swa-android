# Installing Android Studio on a Local Disk for Best Performance

1. Uninstall your current Android Studio by removing the following files and directories from your home directory (`~`):

    ```
    rm -rf .android/
    rm -rf .AndroidStudio2.2/
    rm -rf .AndroidStudio2.3/
    rm -rf Android/
    ```

2. Set up environment variables by adding the following lines to the file `.bashrc` in your home directory (replace `?????` with the number in your `tmpuser` name). The leading dot in `.bashrc` means that the file is hidden. Use `ctrl+h` in Ubuntus file explorer to toggle show hidden files, or open gedit from the command line by issuing `gedit ~/.bashrc`.

    ```
    export ANDROID_SDK_HOME='/NOBACKUP/tmpuser-?????/android-local'
    export ANDROID_AVD_HOME='/NOBACKUP/tmpuser-?????/android-local/.android'
    export ANDROID_HOME='/NOBACKUP/tmpuser-?????/android-local'
    ```

3. Restart your bash shell(s) to read the contents of `.bashrc` and make the changes take effect. To check that your enviromental variables are indeed setup correctly you can use the command.

    ```
    printenv | grep ANDROID
    ```

    *(`printenv` prints all enviromental variables, `|` pipes the output to another process, in this case `grep` which is used to filter the result.)*
4. Start the installation of Android Studio (the version of your ZIP file may be different):

    ```
    cd /NOBACKUP/tmpuser-?????
    rm -rf android-studio/
    unzip ~/Download/android-studio-ide-145.3537739-linux.zip
    android-studio/bin/studio.sh
    ```

5. In the step "Install Type" choose "Custom".

6. In the step "SDK Components Setup" make sure the Android SDK Location is set to `/NOBACKUP/tmpuser-?????/android-local`. If it is not, then your environment variables are not set up correctly.

7. Create a new AVD by selecting Tools > Android > AVD Manager from the main Android Studio menu.
   - Pick a device with a lower resolution, for example, the Nexus 4.
   - In step "System Image" select the tab "x86 Images", and choose API Level 22, ABI x86_64 (important for fast virtualization).
   - The message in "Component Installer" should say "Installing Google APIs Intel x86 Atom_64 System Image in /NOBACKUP/tmpuser-?????/android-local/system-images/android-22/google_apis/x86_64".
   - In step "Android Virtual Device (AVD)" click "Finish": this takes a few moments to complete.

8. Create or import an Android project to test your new installation.
