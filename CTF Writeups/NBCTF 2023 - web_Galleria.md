Challenge Name: web/Galleria  
Challenge Author: flocto

## Viewing the app

- Before doing anything, I just visited the live app to see what the app does and just browse around. On first visit, we are presented with a image upload:
- ![Screen Shot 2023-12-02 at 2.13.17 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.13.17%20PM.png)
- Once we upload, we are then provided with a gallery of images:
- ![Screen Shot 2023-12-02 at 2.13.28 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.13.28%20PM.png)
- Opening an image in a new tab, I notice the system to view an image is `https://galleria.chal.nbctf.com/gallery?file=gui-demo.png`, where file is a query parameter. Is this [Local File Inclusion](https://owasp.org/www-project-web-security-testing-guide/v42/4-Web_Application_Security_Testing/07-Input_Validation_Testing/11.1-Testing_for_Local_File_Inclusion)?

## Poking around

- Just testing, the first file most people try to get is the `/etc/passwd` file as it has a uniquely identifiable format and exists on practically every linux system, and is readable by all users.
- However, visiting `https://galleria.chal.nbctf.com/gallery?file=../../../../../../../../../../../../../etc/passwd` just results in a redirect back to the home page. hmm
- Let's try another method: `https://galleria.chal.nbctf.com/gallery?file=/etc/passwd`. If the program just tries to open the file path provided relative to the current directory rather than `/path/to/folder + input`, this will get the root file. Unfortunately this still does not work <sup>[^1]</sup>

## Code Diving

- Starting at the route for the file query parameter, we have this function:
![Screen Shot 2023-12-02 at 2.39.01 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.39.01%20PM.png)
- In other words, if we provide a file parameter, and if the `check_file_path` function succeeds, we get the file. Let's check that function:
![Screen Shot 2023-12-02 at 2.39.48 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.39.48%20PM.png)
- At the bottom, you can see that our input, which is unmodified, is simply checked for the path existing and a file existing, but we have to bypass this weird loop check. The loop check may look confusing, but the easiest way to see how this works is put a `print` statement in the loop on what `parts` is:
![Screen Shot 2023-12-02 at 2.42.55 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.42.55%20PM.png)
- Essentially, every loop modifies this array of the parts of the full path (the parts between the slashes in a file path), and if the length is 0 aka we hit the root directory, it immediately exits. In other words, we can never traverse to the root. This is bad: our starting directory is `/var/www/html`, and a required first step to uploads, but the flag is in `/tmp/flag.txt` as given by the Dockerfile. In order to get to this location, we'd have to go through the root folder at `/`, right?
- Nope! If the file system was a tree, we would have to go through the shared parent node, but filesystems, via symlinks, are actually *not* completely trees. Diving into the provided docker container, let's take a look at all of the symlinks available to us:

## Symlinks

- Using `la -l` we can see details of the files, and this will shows the symlinks that exist, as well as where they point to. For example, in the `var` directory, the lowest we can go without triggering the function, we have 2 symlinks: one to `/run`, and one to `/run/lock`:  
    ![Screen Shot 2023-12-02 at 2.47.15 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%202.47.15%20PM.png)
- Searching the rest of the `var` folder using `la -lR` to recursively search unfortunately (and expectedly) did not result in any symlinks to tmp. But wait: the algorithm that checks the path doesn't know that it is a symlink!

### Symlink bypass

- With symlinks, when you visit them, you simply move to that folder, and the operating system pretends you are in the path. For example, if we go into the `run` folder in the above screenshot, it'll tell us our current directory is `/var/run`, but we'll see the files in the `/run`. If we then go back a directory using `..`, it will follow the directory link *in the symlink folder*<sup>[^2]</sup>, and when we try to get the file at `/var/run/..`, which the loop (incorrectly) resolves as `/var`, the operating system will resolve this to give the root folder! In other words, we've reached the root folder without breaking the check!

## Final exploit

- Since we need to get the `/var` folder from `/var/www/html/uploads`, we go back 3 directories, go into the `run` symlink, go back one, so now we're at the root, then go into our `/tmp/flag.txt` file to get the flag.
- Final link: `https://galleria.chal.nbctf.com/gallery?file=../../../run/../tmp/flag.txt`

- This is the path that the script follows to get the file:
![Screen Shot 2023-12-02 at 3.00.42 PM.png](../_resources/Screen%20Shot%202023-12-02%20at%203.00.42%20PM.png)


[^1]: When I originally did this challenge, this did not work on remote (and is much more trivial than the bypass I found), but was working locally, so I found another way around this issue. However, turns out this was an (the?) intended solution, so the organizers fixed the remote server so it works later. So as of this update, `https://galleria.chal.nbctf.com/gallery?file=/etc/passwd` returns the passwd file, and `https://galleria.chal.nbctf.com/gallery?file=/tmp/flag.txt` would work! I will show how I solved this challenge originally in this writeup.
    
[^2]: This will not work with shell `cd` command, as your shell will smartly resolve `..` to mean "the previous directory back to the symlink place". Instead, you can test by simply doing like `ls -la ..` while in the symlink, and you'll notice that you get the parent directory *of the destination of the symlink folder*, not the directory where the symlink file is located, and any normal program that uses the operating system to resolve files instead of processing the paths themselves will follow the behavior of `ls` where it will get the parent of the destination, not going back up the symlink path.
You can also see this link for more technical details: https://superuser.com/questions/1217237/why-does-ls-show-real-parent-content-when-im-inside-a-symbolic-link-direct