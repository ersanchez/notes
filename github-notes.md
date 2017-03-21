# GitHub Notes

## Add an existing local directory `blah` to a github repository

1. Make an **empty** repository on github
2. Navigate to the directory on the local machine
3. Initialize it: `$ git init`
4. Do a `git add` on everything: `$ git add .`
5. Commit everything: `$ git commit -m "initial commit"`
6. Copy the ssh address from the github repository: `git@github.com:ersanchez/newRepositoryNameGoesHere.git`
7. Back in the local directory, add the ssh address: `$ git remote add origin git@github.com:ersanchez/newRepositoryNameGoesHere.git` and `$ git remote -v` to make sure you entered it correctly.
8. Push it! `$ git push origin master` ...you might need to add the -f flag if it cries

## Convert HTTPS sync to SSH

	git remote set-url origin git@github.com:$GITUSERNAME/$GITREPONAME
