# _00_INDEX

## How to make submodules with git

# from https://stackoverflow.com/questions/14800669/git-create-repo-as-submodule

I'd like to create a new repository as a submodule of my project.

Usually, I create a Github repo and then add it as a submodule using the command git submodule add url_to_repo.git

Is there a way to create a new repo directly as a submodule without creating the repo somewhere else first (neither locally nor remote e.g. on Github)?

gitgit-submodules
Share
Improve this question
Follow
edited Feb 10, 2013 at 17:58
APerson's user avatar
APerson
8,03888 gold badges3535 silver badges4949 bronze badges
asked Feb 10, 2013 at 17:47
manuels's user avatar
manuels
1,50133 gold badges1414 silver badges2626 bronze badges
Add a comment
3 Answers
Sorted by:

Highest score (default)

7


Easy! Say submodule_dir is the name of the directory you wish to submodule-ize (assuming it's not already under git control).

cd submodule_dir
git init
git add .
git commit
# on github, create the new repo, then:
git remote add origin git@github.com:your_username/your_repo_name.git
git push -u origin master
cd ..
mv submodule_dir submodule_dir_delete_me
git submodule add git@github.com:your_username/your_repo_name.git submodule_dir
Later (once you're happy)

rm -rf submodule_dir_delete_me
Share
Improve this answer
Follow
answered Sep 20, 2015 at 8:18
William Denniss's user avatar
William Denniss
16k77 gold badges8181 silver badges123123 bronze badges
This worked for me great when I wanted to add a new folder as a new submodule. If anyone makes a mistake, like I did, and puts in the wrong path to the submodule_dir , in the git submodule add, then that can be fixed by the command of git mv wrong_path correct_path and git will fix up the submodule plumbing automatically, as well as move the submodule. (this mv a submodule requires > git version 1.8.5) – 
texdevelopers
 May 14, 2020 at 13:28 
Add a comment

6


I don't see how you could: a submodule is by definition a SHA1 from another repo (ie the other repo must exist for the parent repo to extract said SHA1): you must have its address referenced in the .gitmodules file that you keep in the parent repo.

Submodules are composed from a so-called gitlink tree entry in the main repository that refers to a particular commit object within the inner repository that is completely separate.

submodule.<name>.url
Defines a URL from which the submodule repository can be cloned. This may be either an absolute URL ready to be passed to git-clone(1) or (if it begins with ./ or ../) a location relative to the superproject's origin repository.

So you could create the submodule repo locally, but you must create it anyway.

Share
Improve this answer
Follow
edited May 23, 2017 at 12:00
Community's user avatar
CommunityBot
111 silver badge
answered Feb 10, 2013 at 18:00
VonC's user avatar
VonC
1.2m513513 gold badges43184318 silver badges51335133 bronze badges
5
How about git init .; mkdir a; cd a; git init .; cd..; git submodule add ./a a or is it a bad idea? – 
manuels
 Feb 10, 2013 at 18:04 
@manuels That's what I just added in my answer, but the point is: you must create a repo for you to be able to reference it in a parent repo as a submodule. However, I don't see how it would play out once your parent repo is pushed to GitHub and clone by another user. – 
VonC
 Feb 10, 2013 at 18:06 
2
so it's fine if I create the repo in the directory of the super-repo and use this dir as a submodule? – 
manuels
 Feb 10, 2013 at 18:19
@manuels yes: you can declare a submodule which is a nested repo of the main repo. The thing is: anyone cloning your main repo (that you owuld have pushed to GitHub) won't have access to your nested repo (even if pushed to GitHub too), because the ./myNestedRepo url will refer to an empty directory on their local workstation – 
VonC
 Feb 10, 2013 at 18:24
2
It plays out if you're too lazy to login to github (or offline) and want to create you submodule NOW and push it later. – 
Thibault D.
 Sep 18, 2015 at 7:42
Add a comment

6


If I understand you right this is what I'm frequently doing for eclipse projects and workspaces. Let's start with this structure:

$ find .
.
./projekt.txt
./sub1
./sub1/sub1.txt
./sub2
./sub2/sub2.txt
First initialize the submodules and master:

$ cd sub1
$ git init
$ git add *
$ git commit -m "init sub1"
$ cd ../sub2
$ git init
$ git add *
$ git commit -m "init sub2"
$ cd ..
$ git init
$ git status
# On branch master
#
# Initial commit
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#   projekt.txt
#   sub1/
#   sub2/
To add those folders as submodules instead of regular folders, simply do the following command, and use relative paths like ./sub instead of just sub

$ git submodule add ./sub1
$ git submodule add ./sub2
Now it should look like

$ git status
# On branch master
#
# Initial commit
#
# Changes to be committed:
#   (use "git rm --cached <file>..." to unstage)
#
#   new file:   .gitmodules
#   new file:   sub1
#   new file:   sub2
#
# Untracked files:
#   (use "git add <file>..." to include in what will be committed)
#
#   projekt.txt
Finally do a git add * and git commit -m "init parent" on the parent folder and there you are!

If you now change a file in one of your submodules, you have to commit the submodules first and then the parent repository as well in order to get the latest versions of your submodules when someone clones your parent repo.

Share
Improve this answer
Follow
answered Oct 27, 2013 at 14:24
dersimn's user avatar
dersimn
80599 silver badges1515 bronze badges
1
If the submodule is in another folder like ./sub1/sub2 then git submodule add ./sub1/sub2 will clone sub2 into . which is not what you want. To fix that use git submodule add ./sub1/sub2 ./sub1/sub2. The first path specifies from where to add and the second where to add it to. – 
nwp
 Jul 3, 2021 at 15:25 
Uhm, I am getting the error: cannot add to the index - missing --add option? when I try to commit the .gitmodules and the submodule files... – 
Bakaburg
 Jan 21, 2022 at 14:12 
solved, I had to commit first the .gitmodules file and then the subfolder – 
Bakaburg
 Jan 21, 2022 at 14:26
