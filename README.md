# Introduction to sed

Learn how to get started with [sed](https://www.gnu.org/software/sed/)!

**We recommend going through this walkthrough in [Gitpod](https://gitpod.io/) as Gitpod will have everything we need for this walkthrough. Hit the button below to get started!**
</br>
</br>
[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io/#https://github.com/adp8ke/Introduction-to-sed) 
</br>
</br>
If you prefer to do this locally, then you may need to download the latest version of [GNU sed](https://www.gnu.org/software/sed/#download) if you do not have it on your local OS.

## **1. Reading Lines**

### **1.1 - You can run any of these following commands as they will do the same thing, but they have different syntax. The flag `-n` is used to suppress output, and the `p` command is used to print specific lines.**

**1.1.1 - In this version, we are using a piped input from the cat command and then printing only lines 5-8 of `lyrics.txt`.**

```bash
cat lyrics.txt | sed -n '5,8p'
```

**1.1.2 - The next version does the same; however, we use the input file directly verses reading it with `cat`.**

```bash
sed -n '5,8p' lyrics.txt
```

**1.1.3 - The third version also does the same thing; however, when using the `+` symbol, we are telling sed to start at line 5 and the operate on the next 3 lines as well.**

```bash
sed -n '5,+3p' lyrics.txt
```

## **2. Deleting Lines**

### **2.1 - Similar to reading lines, we can use various ways to do deletions like so:**

**2.1.1 - `cat` pipe**
```bash
cat lyrics.txt | sed '5,8d'
```

**2.1.2 - Input File**
```bash
sed '5,8d' lyrics.txt
```

**2.1.3 - Input File with `+`**
```bash
sed '5,+3d' lyrics.txt
```
### **2.2 - Additionally, we can also delete lines that contain specific patterns.**

**2.2.1 - This command will delete all lines that contain the pattern `thunder`.**
```bash
sed '/thunder/ d' lyrics.txt 
```

### **2.3 - We could additionally combine commands to run multiple commands at the same time. We could do this with 2 methods: `-e` and `;`.**

**2.3.1 - To combine commands with -e, we can do the following. This combination command will act on both the `thunder` and `magic` patterns and delete the    lines that contain them.**

```bash
sed -e '/thunder/ d' -e '/magic/ d' lyrics.txt  
```

**2.3.2 - The other method is to use `;` like below. The commands are separated by `;` and the commands are separated by line breaks. Additionally, the same   command can be written in one line, which is shown below as well.**

```bash
sed '
/thunder/ d;
/magic/ d
' lyrics.txt
```
**or**

```bash
sed '/thunder/ d; /magic/ d' lyrics.txt
```

## **3. Substitutions**

### **3.1 - Substitutions work for the first occurrence of the chosen sequence in each line unless specified with a global option.** 

**3.1.1 - If we run the below command, notice that only the first `thunder` in each applicable line changes to `lightning` and not both.**

```bash
sed 's/thunder/lightning/' lyrics.txt
```

### **3.2 - To make it change every `thunder` available, we can append a `g` option.**

```bash
sed 's/thunder/lightning/g' lyrics.txt
```

### **3.3 - Say we also want to change the capitalized `Thunder`, then we can also add an `i` option with the `g` option.**

```bash
sed 's/thunder/lightning/gi' lyrics.txt
```

### **3.4 - But wait, that also changed the `Thundercats` into `lightningcats` and we didn’t want to do that.** 

**3.4.1 - We can make changes to the command to account for this. We can change the pattern to be `Thunder,` to avoid changing `Thundercats` into `lightningcats`. Additionally, we can combine the first and second substitution commands to do it all at once.**

```bash
sed '
s/thunder/lightning/g;
s/Thunder,/Lightning,/g
' lyrics.txt
```

### **3.5 - If we want to display only the lines we changed, then we can run the following command:**

```bash
sed -n '
s/thunder/lightning/gp;
s/Thunder,/Lightning,/gp
' lyrics.txt
```

**3.5.1 - There are 8 lines in the output because it shows the output per command per line, so the first line of each pair is for the `s/thunder/lightning/gp` command and the second line of each pair is for the `s/Thunder,/Lightning,/gp` command.**

## **4. Writing**

### **4.1 - Currently, we have only been seeing changes in the terminal without actually changing the input file / pipe nor creating a new file from the output. We will cover 2 methods of writing using sed: `-i` and `>`.**

**4.1.1 - The `-i` method overwrites the input file with the output. However, we can append the `-i` with a `.bak` to create a backup file so we do not lose the original input file.**

```bash
sed '
s/thunder/lightning/g;
s/Thunder,/Lightning,/g
' lyrics.txt > edited.txt
```

**4.1.2 - In the output of edited.txt, we see that we were able to write the changes we made using the substitution commands.**
```bash
cat edited.txt
```

### **4.2 - To show how we can create a backup, we will revert the changes we made above and append a `.bak` to the `-i` option.**

```bash
sed -i.bak '
s/lightning/thunder/g;
s/Lightning,/Thunder,/g
' edited.txt
```

### **4.3 - Check Changes.**
**4.3.1 - Check `edited.txt` and notice that the changes were reverted and match the original `lyrics.txt`.**

```bash
cat edited.txt
```

**4.3.2 - Check `edited.txt.bak` and confirm that the file matches the original `edited.txt` before we made the reversion.**

```bash
cat edited.txt.bak
```

## **5. Scripts**

### **5.1 - Another method for running sed commands is using the `-f` option and a script file.**

**5.1.1 - In this example, we will take an input pipe of `lyrics.txt` and then use the script file to make further edits. The input pipe uses the `=` command, which prints the current input line number with a trailing newline. If you look at `script.txt`, we have 3 lines. In sed script files, we do not use apostrophes, and each line is a separate command. The first line, `N`, appends the current and next line to the pattern space. The second line, `s/\n/. /`, substitutes the new line break with period and space. And the third line, `G`, adds double spacing. So by combining the input pipe and the sed commands, we should get back a double-spaced, numbered list of the lyrics.**

```bash
sed = lyrics.txt | sed -f script.txt
```

## **6. Real World Application**

### **6.1 - In this “real-world-example”, say we get a CSV file from someone in our company or team and they tell us that it has some issues. They noticed that the first field is missing a value for at least 1 row, and they are unsure of how many other rows are affected. How can we approach this problem with sed?**

**6.1.1 - First things first, we could do a quick grep to see how many rows are affected. We can do this by using a `^` to signify the beginning of a line and use it with a comma to be more specific.** 

```bash
grep "^," spacecraft_journey_catalog.csv 
```

**6.1.2 - Great, we can see that at least 10 rows are affected and are missing values for field 1 of the CSV. Now, how can we use this information?**

**6.1.3 - The first thing we can do is create a file with the specific records that are missing values for field one. We can do this in 2 ways: `grep` input pipe or using the CSV as the input file. No matter the method we choose, the `sed` substitution command itself is the same for both. We will need to substitute the beginning of the line and comma (`^,`) with something like `Missing Summary`, and output only those specific lines using the `p` and `n` options. Then we can write the output to a file called `missing_items.csv`.**

```bash
grep "^," spacecraft_journey_catalog.csv | sed -n 's/^,/Missing Summary,/p' > missing_items.csv
```
**or**

```bash
sed -n 's/^,/Missing Summary,/p' spacecraft_journey_catalog.csv > missing_items.csv
```

**6.1.4 - We then run a `cat` to visualize the newly created CSV**

```bash
cat missing_items.csv
```

### **6.2 - Now that we have a file of the “corrupted” data, we could potentially also create a new CSV file that substitutes the rows with `Missing Summary` as a stopgap. Again, we could do this using the `grep` pipe, but we will just the input CSV moving forward.** 

**6.2.1 - In the `sed` command, we do the same thing we did to substitute the empty values, but we do not want to isolate them hence no `p` or `n` options. We then take the entire output and write it into a new CSV called `updated_items.csv`.**

```bash
sed 's/^,/Missing Summary,/' spacecraft_journey_catalog.csv > updated_items.csv
```

**6.2.2 - We then run a `grep` to confirm that the new CSV has used `Missing Summary` as a stopgap for the rows missing values for field one.**
```bash
grep "Missing Summary" updated_items.csv 
```

### **6.3 - Another thing we could do with this initial CSV is to create a new CSV that lacks the “corrupted” rows.**

**6.3.1 - We will utilize the deletion commands we introduced in the first part of the walkthrough. We use the same `^,` pattern to act on; however, instead of substitution, we are doing deletion. If you remember, when doing it the way we are, we are telling sed to delete the entire line if it contains the pattern.**

```bash
sed '/^,/ d' spacecraft_journey_catalog.csv > removed_items.csv
```

**6.3.2 - We can then confirm that the deletions occurred by running a `grep` on the newly created CSV, which should return nothing.**
```bash
grep "^," removed_items.csv
```

And that will wrap up our walkthrough on basic `sed` operations as well as a potential real-world scenario in which we can use a tool like sed to do some fast data engineering.

### Additional Resources
- [Live Demo]()
- [Accompanying Blog]()
- [Accompanying SlideShare]()
- https://github.com/adp8ke/Introduction-to-sed
- https://www.gnu.org/software/sed/manual/sed.html
- https://www.oracle.com/technical-resources/articles/dulaney-sed.html
- https://www.digitalocean.com/community/tutorials/the-basics-of-using-the-sed-stream-editor-to-manipulate-text-in-linux
- http://sed.sourceforge.net/sed1line.txt
- http://sed.sourceforge.net/#scripts
- https://www.linuxtechi.com/20-sed-command-examples-linux-users/
