# repo-clean
example repo clean case

## get files size

git rev-list --objects --all |\
  git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |\
  sed -n 's/^blob //p' |\
  sort --numeric-sort --key=2 |\
  cut -c 1-12,41- |\
  $(command -v gnumfmt || echo numfmt) --field=2 --to=iec-i --suffix=B --padding=7 --round=nearest

## clean via bfg
https://rtyley.github.io/bfg-repo-cleaner

git clone --mirror git@github.com:grahovsky/repo-clean.git\
cd repo-clean\
git lfs fetch --all\
git symbolic-ref HEAD refs/heads/develop\
cd ..\
java -jar bfg-1.14.0.jar --delete-files '*.{swp,whl,jar,xz}' repo-clean\
java -jar bfg-1.14.0.jar --strip-blobs-bigger-than 10M repo-clean\
cd repo-clean\
git reflog expire --expire=now --all && git gc --prune=now --aggressive\
git push --mirror
