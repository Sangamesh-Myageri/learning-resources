# Bash Beginner Cheat Sheet

Commands from freeCodeCamp's **Learn Bash by Building a Boilerplate**, grouped by topic.
In the tutorial you build a small website folder structure using only the terminal.

> **Note:** The tutorial file could only be read up to the `rmdir` lesson (step 1150). Any lessons after that are not covered here.

---

## Golden rules

- **Capitalization matters.** `Index.html` and `index.html` are different files.
- **"Folder" and "directory" mean the same thing.**
- **Press the Up arrow** to bring back your previous command instead of retyping it.
- **Hidden files** start with a dot (like `.gitignore`) and don't show up in a normal `ls`.
- Commands follow the pattern: `command  [flag]  [thing to act on]`
- A **flag** changes how a command works, e.g. `ls -l`.

---

## 1. Finding out where you are

| Command | Stands for | What it does |
|---|---|---|
| `pwd` | print working directory | Shows the full path of the folder you're in |
| `ls` | list | Shows what's inside the current folder |
| `ls -l` | long list | Same, but one item per line with more details |
| `ls -a` | all | Also shows hidden files (and `.` and `..`) |
| `ls --help` | | Shows all the flags `ls` supports |

```bash
pwd          # /workspace/project
ls           # freeCodeCamp
ls -a        # shows .gitignore and other hidden files too
```

**Tip:** In `ls` output, folders show up in color (blue/green) and files show their extension.

---

## 2. Moving around

| Command | What it does |
|---|---|
| `cd folder_name` | Go into a folder |
| `cd ..` | Go back (up) one level |
| `cd ../..` | Go back two levels |
| `cd ../../..` | Go back three levels |
| `cd .` | Stay where you are (`.` means "this folder") |
| `cd /full/path/here` | Jump straight to a folder using its full path |

```bash
cd freeCodeCamp      # go into freeCodeCamp
cd test              # go into test (inside freeCodeCamp)
cd ..                # back to freeCodeCamp
cd ../..             # back up two levels
```

**Remember:** `.` = this folder, `..` = the folder above. Your prompt shows the current folder name.

---

## 3. Reading and cleaning up the screen

| Command | What it does |
|---|---|
| `more filename` | Shows a file's contents. Press **Enter** to scroll until the end |
| `clear` | Empties the terminal screen (doesn't delete anything) |
| `echo some text` | Prints the text to the terminal |

```bash
more package.json
more README.md
echo hello terminal
clear
```

---

## 4. Creating things

| Command | Stands for | What it does |
|---|---|---|
| `mkdir name` | make directory | Creates a new folder |
| `mkdir parent/child` | | Creates a folder inside another folder, without going there first |
| `touch filename` | | Creates a new empty file |

```bash
mkdir website
cd website
touch index.html
touch styles.css
touch index.js
touch .gitignore     # a hidden file (starts with a dot)
```

You can make a folder inside another one from where you are:

```bash
mkdir client
mkdir client/src
```

---

## 5. Copying, moving and renaming

| Command | What it does |
|---|---|
| `cp file destination` | **Copy** a file (the original stays) |
| `mv file destination` | **Move** a file to another folder |
| `mv old_name new_name` | **Rename** a file |

```bash
cp background.jpg images          # copy into the images folder

mv roboto.font roboto.woff        # rename (change the extension)
mv roboto.woff fonts              # move into the fonts folder

mv index.html client/src          # move using a path
mv header.png ..                  # move up one folder
mv images/footer.jpeg client/assets/images   # path to path
```

**Key idea:** `mv` does two jobs. If the second thing is a **folder**, it moves. If it's a **new name**, it renames.

---

## 6. Deleting things

| Command | Stands for | What it does |
|---|---|---|
| `rm filename` | remove | Deletes a file |
| `rmdir foldername` | remove directory | Deletes an **empty** folder |

```bash
rm background.jpg
rm header.png
rmdir images
```

> **Careful:** The terminal has no recycle bin. Deleted files are gone. `rmdir` only works on empty folders; if the folder still has files in it, it will refuse.

---

## 7. Searching

| Command | What it does |
|---|---|
| `find` | Shows the whole file tree of the current folder |
| `find foldername` | Shows the file tree of a different folder |
| `find -name filename` | Searches for a file or folder by name |
| `find --help` | Shows what else `find` can do |

```bash
find                     # everything under the current folder
find client              # only what's inside client
find -name index.html    # where is index.html?
find -name src           # works for folders too
```

---

## 8. Getting help

Most commands have a `--help` flag:

```bash
ls --help
find --help
```

---

## Quick reference (all commands at a glance)

| Command | Meaning |
|---|---|
| `pwd` | Where am I? |
| `ls` / `ls -l` / `ls -a` | What's here? (plain / detailed / including hidden) |
| `cd` | Change folder |
| `more` | Read a file |
| `clear` | Clean the screen |
| `echo` | Print text |
| `mkdir` | Make a folder |
| `touch` | Make an empty file |
| `cp` | Copy |
| `mv` | Move or rename |
| `rm` | Delete a file |
| `rmdir` | Delete an empty folder |
| `find` / `find -name` | Look around / search |
| `--help` | Show help for a command |

---

## Practice: rebuild the tutorial project

Try this from memory, then check yourself with `find`:

```bash
mkdir website && cd website
touch index.html styles.css index.js
mkdir -p client/src client/assets/images    # (see note below)
mv index.html index.js styles.css client/src
touch header.png footer.jpeg
mv header.png footer.jpeg client/assets/images
find
```

Goal: your `find` output should look like this:

```
.
./client
./client/src
./client/src/index.html
./client/src/index.js
./client/src/styles.css
./client/assets
./client/assets/images
./client/assets/images/header.png
./client/assets/images/footer.jpeg
```

*Note: `mkdir -p` and `&&` weren't taught in the tutorial. `-p` creates parent folders in one go and `&&` runs the second command only if the first one worked. Without them, just run the commands one at a time as shown in sections 4 and 5.*

---

## Common mistakes

- **"No such file or directory"** → you're probably in the wrong folder (check with `pwd`) or misspelled the name (check with `ls`).
- **Nothing happens after `touch`** → that's normal; it creates the file silently. Run `ls` to confirm.
- **Can't see `.gitignore`** → use `ls -a`.
- **`rmdir` fails** → the folder isn't empty.
- **`ls -l` looks wrong** → that's a lowercase letter **L**, not the number 1.
