![mp5JwKO](https://user-images.githubusercontent.com/94487022/159171277-5da6f607-f870-467c-a662-a8868ba79d70.png)

Based on the Mr. Robot show, can you root this box?

First thing we do is nmap scan.

![nmap](https://user-images.githubusercontent.com/94487022/159171757-09f0002c-e7e8-4ebf-938e-efe4e7f0e1a8.png)

We have 3 ports. Now let's go to the website.

![website](https://user-images.githubusercontent.com/94487022/159172149-c2332215-1b35-4d5b-80ec-6bf2020f682b.png)

We have a good looking page over here. You can explore there options if you want.

Now we will scan directories with dirb.

After that, we have a huge amount of files. Firstly let's check "robots.txt".

![robots txt](https://user-images.githubusercontent.com/94487022/159172331-eabb8aab-2d01-4546-9ecf-1dc46b4c1ea1.png)

Let's go to "key-1-of-3.txt" and see what is happening.

![firstkey](https://user-images.githubusercontent.com/94487022/159172406-a3e6c71d-6fc8-46db-b245-0ce38d9b1a1b.png)

There it is. We have found the first key. Time to find second key. I will download "fsocity.dic".

![fsocitydic](https://user-images.githubusercontent.com/94487022/159173077-4b4db829-7bbb-4aeb-9122-889fb1a0c616.png)

When we scanning files with dirb, i noticed there is WordPress login page. Let's visit.

![wplogin](https://user-images.githubusercontent.com/94487022/159172945-300ed97e-195d-4a4a-893f-1deb5669e3cb.png)

The file we downloaded was an wordlist. We can use it for brute force to login page. Before that, I try some usernames and there is a interesting error. It says "Invalid username". Pretty vulnerable.

![invalid username](https://user-images.githubusercontent.com/94487022/159174022-c083f12f-8fb4-4e5a-9094-f8dd45801ec4.png)

Keep trying some keywords from the show. When i tried "Elliot" things are changing.

![Elliot](https://user-images.githubusercontent.com/94487022/159174101-09897269-0ae2-46c0-bbf3-890280802877.png)

Now we know that the user is Elliot. Let's try brute force via wpscan. You can use hydra if you want.

````
wpscan --url 10.10.134.233 --wp-content-dir wp-login --usernames Elliot --passwords ~/Downloads/fsocity.dic
````


![wpusrnamepwd](https://user-images.githubusercontent.com/94487022/159174235-dd04d432-ac10-4b6f-aa43-23b5f30a03ba.png)

We got the password. Can login now.

![wpdashboard](https://user-images.githubusercontent.com/94487022/159174276-82ebc118-49e5-4609-8364-a7a6ee28d18c.png)

After exploring everywhere, found out if you go to Appearance -> Editor, clearly you can change things like php codes.

![wpphpedit](https://user-images.githubusercontent.com/94487022/159174407-618fc738-81fd-41ce-9ce7-dc7a1b958d56.png)

Let's change 404.php file. We can use pentestmonkey php reverse shell. When everything is done, update the file. Then setup netcat.

![netcat](https://user-images.githubusercontent.com/94487022/159174513-eabaa84b-791e-4c83-842e-f9c5357bb823.png)

We have a shell now. Just looking around. I found 2 files named "key-2-of-3.txt" and "password.raw-md5". We can't read second key but got the robot's encrypted password. Let's visit Crackstation.

![md5hash](https://user-images.githubusercontent.com/94487022/159174638-6af5e796-8120-4fb2-a71f-f5ce1e4d286b.png)

![crackstation](https://user-images.githubusercontent.com/94487022/159174639-d9bf9258-58b9-451e-b17d-c26e446fdfae.png)

There it is. We found the robot's password. Have to switch to user robot now.

But it says "su: must be run from terminal". Let's see if the machine have python. Answer is true. Now have to spawn pty. The pty spawn function spawns a new process (/bin/bash in this case) and then connects IO of the new process to the parent/controlling process. You can use this.
`
python -c 'import pty;pty.spawn("/bin/bash")'
`

![terminal](https://user-images.githubusercontent.com/94487022/159175000-71f13adc-37ae-49f5-addd-0f95c1f36325.png)

![surobot](https://user-images.githubusercontent.com/94487022/159175013-8f1e02d7-d840-471d-9a0d-4162159e81b9.png)

Now we should be able to see contents of "key-2-of-3.txt".

![secondkey](https://user-images.githubusercontent.com/94487022/159175079-9124dca8-962a-4f7e-bbc0-4405ce949cb4.png)

There it is.

Now we need to privilege escalate. I tried sudo -l command but user robot was not in sudoer’s list. In this case run this command to print files which have SUID (Set owner User ID) bit set.
`
find / -perm -u=s -type f 2>/dev/null
`

![findnmap](https://user-images.githubusercontent.com/94487022/159175703-9e7d406b-bf02-4233-aa40-4f3a54d0a5f2.png)

We found "nmap". Now jump to gtfobins website and search "nmap". Found this.

![gtfobins](https://user-images.githubusercontent.com/94487022/159175830-d936fe99-8bc0-46f6-a780-c38d2051e0ff.png)

These lines will give a shell as root. Let's try.

![finalkey](https://user-images.githubusercontent.com/94487022/159175884-12439b92-4adb-46aa-bf1c-8d514778b03c.png)

That's it! We are root now and also got the final key!

Challenge solved. Thank you for reading this write up.
