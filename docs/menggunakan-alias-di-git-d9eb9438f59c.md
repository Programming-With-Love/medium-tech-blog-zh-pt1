# Menggunakan Alias di Git

> 原文：<https://medium.easyread.co/menggunakan-alias-di-git-d9eb9438f59c?source=collection_archive---------5----------------------->

## Berbicara mengenai salah satu fitur di Git

![](img/f6d8f1297e2ad4a240e95ea75fa3fbb7.png)

git-alias-meme

# **INTRO**

Menyambung tulisan saya sebelumnya mengenai [***Menggunakan Git dengan Command Line***](https://medium.com/@tulus.tobing90/menggunakan-git-dengan-command-line-3e53e4cfb752) , *nah* kali ini kita akan membahas yang namanya ***git alias*** . Git alias ini merupakan salah satu fitur dari si git itu sendiri. Cara penggunaan git alias ini sebenarnya sama persis dengan penggunaan alias di Linux yaitu dengan membuat sebuah *shortcut command* untuk *command* yang lebih lengkap.

Dengan git alias ini, kita dapat menggunakan *command* git melalui *console* dengan cara yang lebih mudah, *simple* dan tidak perlu mengetik keseluruhan *command* dari git itu sendiri.

Misalnya jika nama *branch-* nya sendiri sudah panjang, apabila ingin *push origin* atau *pull origin* akan cukup menyusahkan. Dengan git alias ini, jika kita ingin *pull origin* atau *push origin* cukup *make git pullo* atau *pusho* .

Berikut contoh dari git alias yang saya gunakan.

Untuk penjelasan tiap *command-* nya boleh dicek [*DISINI*](https://medium.com/@tulus.tobing90/menggunakan-git-dengan-command-line-3e53e4cfb752) ya. Berikut beberapa git alias yang paling sering dipakai :

> **git st**

alias untuk `**git status**`

> **git co {branch_name}**

alias untuk `**git checkout {branch_name}**`

> **git cob “new_branch_name”**

alias untuk `**git checkout -b {new_branch_name}**`

> **git cm
> git com “message”**

alias untuk *add* semua *changes* kemudian langsung *commit* . Bedanya jika menggunakan `**git cm**` , akan muncul editor untuk *input message-* nya, tapi jika `**git com "message"**` bisa digunakan apabila ingin meng- *input message commit* nya langsung di *single line* .

> **git pusho**

alias untuk `**git push origin {active_branch}**` atau *push branch* yang sedang aktif ke *upstream branch* . Tidak perlu lagi secara eksplisit menuliskan mau ke *branch origin* mana *branch* akan tersebut di push.

> **git pullo**

alias untuk `**git pull origin {branch_name}**` atau *pull origin branch* yang sedang aktif. Sama dengan alias *pusho* , *command* ini tidak perlu lagi menuliskan secara ekplisit akan melakukan *pull origin* dari *branch* mana. Akan disesuaikan dengan *branch* yang sedang aktif.

> **git last**

alias untuk `**git for-each-ref --sort=-committerdate --count=10 refs/heads/ | cut -d"/" -f 3**` . Git *command* ini salah satu git *command* yang paling berguna dan paling sering saya gunakan. Kegunaannya adalah untuk melakukan *list* terhadap 10 *branch* yang terakhir kali digunakan dan terurut oleh waktu pemakaian.

# Command Tambahan

*Command* berikut yang cukup jarang digunakan tapi lumayan membantu :

> **git ls
> git ll
> git lg**

Untuk melihat *log* tapi dengan penambahan dekorasi, dan ada juga :

> **pushm** #push origin master
> **pullm** #pull origin master
> **stsl** #stash list
> **stspop** #stash pop
> **stsave** #stash save

# Instalasi

Untuk proses instalasinya sendiri cukup mudah. Kita hanya perlu membuat sebuah file yang namanya `**.gitconfig**` di *folder home* kalian.

> **vi ~/.gitconfig**

lalu paste code berikut.

```
[alias]
bl = branch -l
co = checkout
pushm = push origin master
pullm = pull origin master
pusho = !git push origin “$(git rev-parse — abbrev-ref HEAD)”
pullo = !git pull origin “$(git rev-parse — abbrev-ref HEAD)”
cob = checkout -b
cm = !git add -A && git commit
com = !git add -A && git commit -m
st = status
stsl = stash list
stspop = stash pop
stsave = stash save
ls = log — pretty=format:”%C(yellow)%h%Cred%d\\ %Creset%s%Cblue\\ [%cn]” — decorate
ll = log — pretty=format:”%C(yellow)%h%Cred%d\\ %Creset%s%Cblue\\ [%cn]” — decorate — numstat
lg = log — all — decorate — graph — oneline
last = !git for-each-ref — sort=-committerdate — count=10 refs/heads/ | cut -d”/” -f 3
[user]
email = [your.name@example.com](mailto:your.name@example.com)
name = Your Name
[core]
editor = vim
```

Kemudian *save* , dan *voilà* . Git alias sudah siap digukan. *File* tersebut bisa kalian *download* [*DISINI*](https://gitlab.com/tulustobing/dot-files/-/blob/master/.gitconfig) *.* Sekian artikel tentang git alias ini. Kamu boleh share dan berikan respon jika ada git *command* yang terlewat yang seharusnya ada di git alias kalian ya. Salam!