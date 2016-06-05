# this is a simple test

The repository on my local git server is copied to this github repository
with an post-update hook that is changing the commiter's email address.

## /git/repo-name/hooks/post-update

```bash
#!/bin/bash
echo "(server) hooks/post-update"

unset GIT_DIR

mkdir /tmp/github/ -p
cd /tmp/github/
rm repo-name.git -rf
git clone --bare /git/repo-name repo-name.git
cd repo-name.git

git filter-branch --env-filter '

  OLD_EMAIL="private@domain.tld"        # change this
  NAME="Your Public Name"               # change this
  EMAIL="USER@users.noreply.github.com" # change this

  if [ "$GIT_COMMITTER_EMAIL" = "$OLD_EMAIL" ]
  then
    export GIT_COMMITTER_NAME="$NAME"
    export GIT_COMMITTER_EMAIL="$EMAIL"
  fi

  if [ "$GIT_AUTHOR_EMAIL" = "$OLD_EMAIL" ]
  then
    export GIT_AUTHOR_NAME="$NAME"
    export GIT_AUTHOR_EMAIL="$EMAIL"
  fi

' --tag-name-filter cat -- --branches --tags

git remote add github https://username:token@github.com/Username/repo-name.git
git push --force --quiet --tags github 'refs/heads/*'
echo "To https://github.com/Username/repo-name.git"

cd ..
rm repo-name.git --rf
```
