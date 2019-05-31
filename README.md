
# Saving to USB on Rooted Android Devices

This is a code snippet that should hopefully help someone save to USB on a rooted android device.

There are 2 tricks to point out:

1. Mounted USBs are stored in ```/mnt/media_rw/```
2. You need to run a process as su to write files to that directory. 

```
 /**
 * Copies the file path to all mounted devices
 * @param filePathToMove The path of the folder to move to usb
 * @return true all the time lol
 */
public static boolean moveFilesToUSB(String filePathToMove) {
    String mountedStoragePath = "/mnt/media_rw/*/"; //Location where usb is/are mounted

    Process p;
    try {
        // Preform su to get root privledges
        p = Runtime.getRuntime().exec("su");

        // Attempt to write a file to a root-only
        DataOutputStream os = new DataOutputStream(p.getOutputStream());

        //TODO: Make encounter dir to organize files better

        //loop through all mounted devices in the standard mount path and move files from the
        // filePathToMove into them.
        String command = "for d in " + mountedStoragePath + " ; do (cp -r " + filePathToMove + " \"$d\"); done\n";

        os.writeBytes(command);

        // Close the terminal
        os.writeBytes("exit\n");
        os.flush();
        try {
            p.waitFor();
            if (p.exitValue() != 255) {
                // TODO Code to run on success
                //toastMessage("root");
                return true;
            } else {
                // TODO Code to run on unsuccessful
                //toastMessage("not root");
                return true;
            }
        } catch (InterruptedException e) {
            // TODO Code to run in interrupted exception
            //toastMessage("not root");
            return false;
        }
    } catch (IOException ex) {
        // TODO Code to run in input/output exception
        // toastMessage("not root");
        return false;
    }
}
  ```
  
Thanks to [StealthCopter](http://www.stealthcopter.com/blog/2010/01/android-requesting-root-access-in-your-app/) for providing the code
to write a file to a directory that requires elevated permissions. 
