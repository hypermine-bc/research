## Mirroring Git repo

Say you have a repo in Gitlab and you want to clone that repo along with all commits,tags,releases etc to new repo in Github:

```
git clone --mirror <url_of_old_repo>
cd <name_of_old_repo>
git remote add new-origin <url_of_new_repo>
git push new-origin --mirror
```

## Resetting unpushed commit 

Let's say you have added and commited a code but you yet have to push, and realised that you have commited incorrect code :

```
git reset --hard origin/master 
```

