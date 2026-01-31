# Day 06 – Linux File Read & Write Practice

## Objective
Practice basic file creation, writing, appending, and reading using fundamental Linux commands.

---

## File Creation
```bash
touch notes.txt
````

* Created an empty file named `notes.txt`.

---

## Writing to File

```bash
echo "Linux file I/O basics" > notes.txt
```

* Wrote the first line using output redirection (`>`), overwriting any existing content.

```bash
echo "Practicing read and write commands" >> notes.txt
```

* Appended a second line using (`>>`).

```bash
echo "Using tee command for output" | tee -a notes.txt
```
**Output**

<img width="694" height="53" alt="image" src="https://github.com/user-attachments/assets/38778ab4-9f50-470c-b21f-a766c93d1989" />


* Displayed output on terminal and appended it to the file using `tee`.

---

## Reading the File

```bash
cat notes.txt
```
**Output**

<img width="378" height="89" alt="image" src="https://github.com/user-attachments/assets/f7ff0252-aa27-435e-964a-9eebd7be0a0c" />


* Displayed the full contents of the file.

```bash
head -n 2 notes.txt
```
**Output**

<img width="442" height="73" alt="image" src="https://github.com/user-attachments/assets/8b579142-ad76-48e3-ab1c-4eec37b918ca" />


* Displayed the first two lines of the file.

```bash
tail -n 2 notes.txt
```
**Output**

<img width="480" height="66" alt="image" src="https://github.com/user-attachments/assets/cae2e2fe-1081-4526-bcba-2eefdc68ac20" />


* Displayed the last two lines of the file.

