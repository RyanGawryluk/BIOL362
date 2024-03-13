# Lab 9: Data Quality Control

Welcome to the bioinformatics portion of the lab! Over the next few weeks, we will explore a couple of computer-based tools that are important in the cleanup,
manipulation, and analysis of amplicon sequence data. These computer lab
sessions are meant to provide you with some experience in using “command line” software to investigate biological questions using sequence data. We realize that
bioinformatics will be new to many of you, and previous experience is not
required to complete any exercises.

### Introduction to Linux command line

The Linux command line interface (CLI) is a powerful tool for interacting with your computer's operating system. Unlike graphical user interfaces (GUIs), which rely on icons and windows, the command line allows users to execute commands by typing text-based instructions.

In the field of bioinformatics, command line programs are essential for data analysis, manipulation, and genome analysis. These tools allow researchers to process large datasets efficiently, perform complex calculations, and extract meaningful insights from genomic data. By leveraging the power of the command line, bioinformaticians can automate repetitive tasks, customize analysis pipelines, and conduct advanced computational analyses with ease.

### General Directory Structure

In Linux, the file system follows a hierarchical structure, with the root directory ("/") at the top. Directories (folders) contain files and other directories, forming a tree-like structure. Users navigate this structure using commands such as 'cd' (change directory) to move between directories.

### Moving Between Directories

To move between directories in Linux, you use the 'cd' command followed by the directory you want to navigate to. You can specify directories using either absolute paths or relative paths.

- **Absolute Path:** Specifies the exact location of a directory from the root directory ("/"). For example:
  
  ```bash
  cd /home/user/Documents
  ```
  
  This command navigates to the "Documents" directory within the "user" directory, which is located in the `/home` directory.

- **Relative Path:** Specifies the location of a directory relative to the current directory. For example:
  
  ```bash
  cd Documents
  ```
  
  This command navigates to the "Documents" directory within the current directory. If you're already in the `/home/user` directory, this command achieves the same result as the absolute path example above.

- To move up one directory (to the parent directory):
  
  ```bash
  cd ..
  ```
  
  This command moves up one level in the directory hierarchy.

- To move to the home directory:
  
  ```bash
  cd ~
  ```
  
  This command moves to the home directory of the current user.

### Linux File Structure vs. Windows and macOS

While Linux, Windows, and macOS all have hierarchical file systems, there are differences in their structures and naming conventions.

#### Linux:

- Root directory: "/"

- Directory separator: "/"

- Case-sensitive file names: "file.txt" and "File.txt" are considered different files

- #### Windows:

- Root directory: "C:" (on most systems)

- Directory separator: "\"

- Case-insensitive file names: "file.txt" and "File.txt" are considered the same file

- #### macOS:

- Root directory: "/"

- Directory separator: "/"

- Case-insensitive file names: "file.txt" and "File.txt" are considered the same file

- ### Understanding the Command Prompt

The command prompt in Linux typically displays information such as the username, hostname, current directory, and a special character called the prompt symbol. Here's a breakdown of what each part means:

- **username:** This is the name of the current user.
- **hostname:** This is the name of the computer or host.
- **current directory:** This shows the path to the current working directory, either absolute or relative.
- **prompt symbol:** This is a special character that signifies the command prompt is ready to accept input. It often ends with a dollar sign ('$') for regular users.

Understanding absolute and relative paths, along with the command prompt, helps users identify their current context within the system and provides important information while navigating and executing commands in the Linux environment.

### Command Descriptions and Examples

- **pwd (Print Working Directory):**
  
  ```bash
  pwd
  ```
  
  Example output:
  
  arduinoCopy code
  
  `/home/user/Documents`

- **ls (List):**
  
  ```bash
  ls
  ```
  
  Example output:
  
  `file1.txt  file2.txt  folder1  folder2`

- **less:**
  
  ```bash
  less filename.txt
  ```
  
  Use arrow keys to navigate. Press 'q' to exit.

- **zless:**
  
  ```bash
  zless compressed_file.gz
  ```
  
  Use arrow keys to navigate. Press 'q' to exit.

- **nano (Text Editor):**
  
  ```bash
  nano new_file.txt
  ```
  
  Use arrow keys to navigate. Write or edit text. Press Ctrl + X to exit.

- **grep (Global Regular Expression Print):**
  
  ```bash
  grep "pattern" filename.txt
  ```
  
  Example output:
  
  `line containing pattern another line containing pattern`

Understanding these commands and their examples empowers users to efficiently navigate the file system, view file contents, edit files, and search for information within the Linux environment.
