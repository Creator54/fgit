```text
░█▀▀░█▀▀░▀█▀░▀█▀
░█▀▀░█░█░░█░░░█░
░▀░░░▀▀▀░▀▀▀░░▀░
```
# Why ??
for fetching sub-directories of a large git repo without cloning the whole repo itself.

## Fish
```Fish
function fgit
  if echo $argv[1]| grep "git@" &>/dev/null
    set dirname (echo $argv[1]|cut -d'/' -f2)
  else
    set dirname (echo $argv[1]| cut -d'/' -f5)
  end

  if [ -z $argv[1] ]
    echo "usage: fgit https://github.com/repo_owner/repo_name"
  else
    git clone --filter=blob:none --no-checkout --depth 1 --sparse $argv[1] &>/dev/null
    cd $dirname
    git sparse-checkout init --cone
    echo
    read -P "get $dirname/" subdir
    echo
    git sparse-checkout add $subdir
    git checkout
    mv $subdir ../ #move requested dir to current dir
    rm -rf ../$dirname #remove useless repo
  end
end
```

## Bash/ZSH
```Bash
function fgit() {
  if echo $1| grep "git@" &>/dev/null;then
    dirname=$(echo $1|cut -d'/' -f2)
  else
    dirname=$(echo $1| cut -d'/' -f5)
  fi

  if [ -z $1 ]; then
    echo "usage: fgit https://github.com/repo_owner/repo_name"
  else
    git clone --filter=blob:none --no-checkout --depth 1 --sparse $1 &>/dev/null
    cd $dirname
    git sparse-checkout init --cone
    echo
    read -p "get $dirname/" subdir
    echo
    git sparse-checkout add $subdir
    git checkout
    mv $subdir ../ #move requested dir to current dir
    rm -rf ../$dirname #remove useless repo
  fi
}
fgit $1
```
