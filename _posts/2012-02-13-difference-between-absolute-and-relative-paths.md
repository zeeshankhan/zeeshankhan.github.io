---
title: "Absolute &amp; Relative Paths"
date: "2012-02-13"
---

**Absolute Paths** Absolute paths are very specific paths that include the domain name. The absolute path can often be pasted directly in the address bar to access the web element directly (often the same as a URL).

_[Example of an Absolute path](http://izeeshan.wordpress.com/2012/02/13/difference-between-absolute-and-relative-paths/)_ You typically use the absolute path with the domain to point to Web elements that are on another domain than your own.

It is a good idea to use absolute paths, without the domain name, on most Web sites. This format insures that the link or image will be usable no matter where you place the page. This may seem like a silly reason to use longer links, but if you share code across multiple pages and directories on your site, using absolute paths will speed up your maintenance.

**Relative Paths** Relative paths are paths the are defined from the location of the current page. _Some rules of creating a relative path:_ links in the same directory as the page have no path information listed: MyFile sub-directories are listed without any preceding slashes: Downloads/MyFile links up one directory are listed as: ../MyFile

Relative paths are sometimes more difficult to work with as the path to a web element will change depending on the location of the current page.  The upside is that relative paths are alot shorter than absoltue paths and decrease the size of the resulting HTML code.

In web or desktop programming/command-prompt use, we often come through such situations where we have to use relative/absolute paths. Is there are any differences between the notation “./” and “/” ?

# ./

Yes, there are! When you use “./” we mean an relative path. Suppose, you are in the location_**[http://www.somedomain.com/some\_directory/index.html](http://www.somedomain.com/some_directory/index.html)**_

Now, if you want to insert an Image file which is located at this directory “some\_directory”, you can write following code in your index.html page:

<img src = "./myimage.jpg">

If you want to use an image from a directory “another\_dir” **on level up**, you should use “../”

<img src = "../another\_dir/another\_image.jpg">

Both some\_dir and another\_dir are located under the home of your domain (www.somedomain.com).

# /

Now, let’s talk about using “/”

“/” means beginning from the Root level of your file system. Suppose, in Linux, you want to Change your current working directory to the directory “etc”. Since “etc” is placed just in the Root directory of your file system (see the image below), you should type something like this in your command-prompt:

cd /etc

If you tried with the command “cd ./etc”, it wouldn’t work! Why? Because “./” means beginning from the Current Working directory. Normally, when you use a command-prompt it selects the user’s Home folder as current working directory. So if you want to go to a directory based on root level, you should use “/” instead of “./”

Image: Unix/Linux File System. See, the folder “etc” stays just below the “root” or “/”

[![](images/unix_tree.gif "unix_tree")](http://izeeshan.files.wordpress.com/2012/02/unix_tree.gif)

Absolute path contains full name of file including the source, for instance, c:\\Windows\\Temp\\log.txt. Relative path contains only the path relativaly to a certain folder, for instance, relative path of log.txt relativaly to the folder Windows is Temp\\log.txt.

Read more: [http://wiki.answers.com/Q/What\_is\_the\_difference\_between\_relative\_path\_and\_absolute\_path#ixzz1mEyM6iBX](http://wiki.answers.com/Q/What_is_the_difference_between_relative_path_and_absolute_path#ixzz1mEyM6iBX)
