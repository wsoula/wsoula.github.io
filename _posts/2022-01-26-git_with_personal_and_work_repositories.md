Git with Personal and Work Repositories
---

My work uses github for some repos and I have my personal github repos.  The issue was that the github user for work
has to follow a specific format so you can be offboarded easily and that user was not the same as my personal user.
And since github only allows you to use a set of ssh keys with one user I had to make two sets and my personal repos
were committed to with my work email.  This was not ideal and I made a shell script to switch out the keys and I would
run that if my push failed.  This wasa the situation I was living with for a long time till I came across the below
blog post that illuminated a better way of doing this.

The below post is copied from https://dev.to/fadilnatakusumah/how-to-separate-your-git-for-work-and-git-for-personal-2n8b
I have copy pasted it for posterity

How to separate your Git for work and Git for personal
---

Have you ever run into a problem where you want to push your code to a company repo, but you don't want to push using git with your personal email?

Here's how easy you can separate your Git for work and Git for personal

First, you must make new SSH Keys with your work email and add it to your work account in your company repo. Make sure you name your ssh key files to be something like id_ed25519_work to differentiate your SSH key for personal and work. Here's how to generate SSH keys.

If you open your git config in ~/.gitconfig, you'll probably have something like this.
[user]
    name = yourusername
    email = youremail@mail.com
In Windows you can find your git config file in C:\Users\[YOURUSERNAME]\.gitconfig.

That is for your personal git account. For your working git account, you can make another git config file, for example .gitconfig-work, and you can fill your file with this
[user]
    name = yourusername
    email = yourworkemail@company.com
In your original git config file, add this line
[user]
    name = yourusername
    email = youremail@mail.com

[includeIf "gitdir/i:[location to your company's project folder]/"]
    path = ~/.gitconfig-work
for example, it would be like this.
[user]
    name = yourusername
    email = youremail@mail.com

[includeIf "gitdir/i:~/working/projectA/"]
    path = ~/.gitconfig-work
and update your gitconfig-work to be like this
[user]
    name = yourusername
    email = yourworkemail@company.com

[core]
    sshCommand = "ssh -i ~/.ssh/id_ed25519_work"
That's it! So, whenever you're in your company's project folder, your Git will automatically use your work email and username with your seperate ssh keys.

You can test your ssh file by typing this in your terminal.
ssh -i ~/.ssh/id_ed25519_work -T git@gitlab.com
and you can test if it's work by typing this from your company's project folder and check if your work email comes up.
git config --show-origin --get user.email
You can change that gitlab.com to github.com if you're using github.

You might want to close all the terminals, or try to logout/restart your computer if it's not working.

Hope this help and Thanks for reading!
