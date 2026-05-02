	*This project has been created as part of the 42 curriculum by cjegouss and ysporri*

	# Milestone 3 - MINISHELL

	<img src="https://bagage-cabine.fr/wp-content/uploads/2020/01/Mollusques-dans-les-bagages-1.jpg" alt="shell" width="900"/>

	## Summary

	- [I Introduction](#i-introduction)
	- [II Description](#ii-description)
	- [1. Parsing](#1-parsing)
	- [2. Execution](#2-execution)
	- [III Instructions](#iii-instructions)
	- [IV Ressources](#iv-ressources)
	- [1. Ressources to develop and understand the project](#1-ressources-to-develop-and-understand-the-project)
	- [2. Linux man allowed function](#2-linux-man-allowed-function)

	## I Introduction

	Welcome to our readme. We are about to walk you through Minishell, a marvelous project aiming to copy the behavior of shell. As overwhelming as this project might be when you first open the subject, we are a capable team, and we must keep in mind that the key to success is to:

	<img src="https://m.media-amazon.com/images/I/61EhUpQP3pL._AC_UF894,1000_QL80_.jpg" alt="shell" width="500"/>

	## II Description

	Minishell is the first core team project. We divided the work, as many of our peers advised, into parsing and execution with one question in mind: will our friendship survive it?

	<img src="https://static0.srcdn.com/wordpress/wp-content/uploads/2020/11/Anakin-and-Obi-Wan-in-Revenge-of-the-Sith.jpg?q=50&fit=crop&w=825&dpr=1.5" alt="peace and freedom" width="700"/>

	### 1. Parsing

	Parsing is divided into two main parts.

	First part: we validate the line entered by the user. At this stage, we chose not to handle the <> case, as it is a rare and specific scenario. We also normalize the input to create a consistent format for the rest of the program: removing tabs and extra spaces, and separating special characters (|, <, >) with spaces before and after them. All the files are in the clean_line directory.

	1. clear_balanced.c (check: all the quotes are close / there isn't only quotes)
	1. final_size.c (count the size of the "clean" string)
	1. multiple_chevron_pipe.c (Search errors: if there are more than 2 following chevrons /  wrong combo <> >< <| >| / chevron at the end of the line / line starts with a pipe / if there is more than one pipe right after a pipe)
	1. space_management.c (remove tabs / remove extra spaces / return clean line: consistent format )

```c
if the user enter ->'    <in cmd|   "cmd"   >out		'
the line is normalize ->'< in cmd | "cmd" > out'
```

	Second part: this stage aims to build a linked list. Each node contains a char ** for the command, infile, outfile, append outfile, and here-doc.  All the files are in the linked_list directory.

	1. allocation.c (malloc char** for a node, initialize struct value) 
	1. count_nb_cmds.c (count nb of cmd between pipes)
	1. count_tab_size.c (count nb of infile, outfile, append outfile, and here-doc between pipes)
	1. create_list.c (name is self-explanatory) 
	1. create_node.c (name is self-explanatory) 
	1. fill_cmd_and_infile.c (name is self-explanatory) 
	1. fill_infile_appout_heredoc.c (name is self-explanatory) 
	1. get_out.c (free functions) 
	1. redirections.c (Check which chevron it is so we can determine if it is an infile, an outfile, an append outfile, or a here-doc )
	1. word_len.c (Length of the word to put in the char **)

```c
the line '< in cmd1 | cmd2 > out'

is transformed in linked list containing tabs:

NODE 0

LAST INFILE IS = in
tab is: cmd
tab 0 is = -cmd1-
tab is: in
tab 0 is = -in-
________

NODE 1

LAST OUTFILE IS = out
tab is: cmd
tab 0 is = -cmd2-
tab is: out
tab 0 is = -out-
```

	### 2. Execution 

	## III Instructions

	This program accepts only one argument, which is the executable.

	If you struggle to find tests here 800 tests for minishell. 
	https://docs.google.com/spreadsheets/d/1BPW7k81LJPhGv2fbi35NIIoOC_mGZXQQJDnV0SjulFs/edit?gid=0#gid=0

	## IV Ressources

	### 1. Ressources to develop and undertand the project <br> 

	1. Signal managment: https://www.youtube.com/watch?v=jF-1eFhyz1U / http://brunogarcia.chez.com/Unix/Docs/Signaux.html
	2. Termcap Manual: https://www.gnu.org/software/termutils/manual/termcap-1.3/html_mono/termcap.html#:~:text=The%20termcap%20library%20is%20the,tgetnum%20%2C%20tgetflag%20%2C%20tgetstr%20).
	3. https://www.gnu.org/software/bash/manual/bash.html (mainly part 8)
	4. https://www-inf.telecom-sudparis.eu/COURS/CSC3102/Supports/ci6-tubes-signaux/ci-signaux.pptx.pdf

	### 2. Linux man allowed function <br> 

	unused before: readline, rl_clear_history, rl_on_new_line, rl_replace_line, rl_redisplay, add_history,  wait3, wait4, signal, sigaction, sigemptyset, sigaddset, getcwd, chdir, stat, lstat, fstat, opendir, readdir, closedir  isatty, ttyname, ttyslot, ioctl, getenv, tcsetattr, tcgetattr, tgetent, tgetflag, tgetnum tgetstr, tgoto, tputs. 

	already used in prior project: printf, malloc, free, write, access, open, read, close, fork, wait, waitpid, kill, exit, unlink, execve, dup, dup2, pipe, strerror, perror.

	<img src="https://i.pinimg.com/474x/10/60/81/10608135853c6b94f8fb9a6f79defc2d.jpg" alt="too much" width="500"/>

	1. Readline Library:

	- readline() - get a line from a user with editing the line will be malloc therefore we will have to free it https://man7.org/linux/man-pages/man3/readline.3.html / https://tiswww.case.edu/php/chet/readline/readline.html<br>
	- rl_clear_history() - clear the history list by deleting all of the entries we have collected via readline() https://tiswww.case.edu/php/chet/readline/readline.html
	- rl_on_new_line() - Tell the update functions that we have moved onto a new (empty) line, usually after outputting a newline. *same source*
	- rl_replace_line() - Replace the contents of rl_line_buffer with text. This preserves the point and mark, if possible. *same source*
	- rl_redisplay() - Change what’s displayed on the screen to reflect the current contents of rl_line_buffer. *same source*

```c
	#include <stdio.h>
	#include <readline/readline.h>
	#include <readline/history.h>

	char *readline (const char *prompt);
	void rl_clear_history(void);
	int rl_on_new_line(void);
	void rl_replace_line(const char *text, int clear_undo);
	int rl_redisplay(void);
```

	2. History Library

	- add_history() - Place string at the end of the history list. The associated data field (if any) is set to NULL. If the maximum number of history entries has been set using stifle_history(), and the new number of history entries would exceed that maximum, the oldest history entry is removed https://man7.org/linux/man-pages/man3/history.3.html
```c 
void add_history (const char *string)
```

	3. Signal

	- signal() - signal handling https://man7.org/linux/man-pages/man2/signal.2.html
	- sigaction() - it is used to change the action taken by a process on receipt of a specific signal - https://man7.org/linux/man-pages/man2/sigaction.2.html / https://www.ibm.com/docs/en/zos/2.5.0?topic=functions-sigaction-examine-change-signal-action
	- sigemptyset() - initialize and empty a signal set https://man7.org/linux/man-pages/man3/sigemptyset.3p.html
	- sigaddset() -  add a signal to a signal set https://man7.org/linux/man-pages/man3/sigaddset.3p.html
	- 
```c
	#include <signal.h>

	sighandler_t signal(int signum, sighandler_t handler);
	int sigaction(int signum, const struct sigaction *_Nullable restrict act, struct sigaction *_Nullable restrict oldact);
	//The sigaction structure is defined as follows: 
	struct sigaction {
		void       (*sa_handler)(int);
		sigset_t   sa_mask;
		int        sa_flags;
		void       (*sa_sigaction)(int, siginfo_t *, void *);

	int sigemptyset(sigset_t *set);
	int sigaddset(sigset_t *set, int signo);
	};
```

	4. Wait Librairy
	- wait3() - The wait3() and wait4() system calls are similar to waitpid(2), but additionally return resource usage information about the child in the structure pointed to by rusage https://linux.die.net/man/2/wait3 / https://www.ibm.com/docs/fr/aix/7.3.0?topic=w-wait-waitpid-wait3-wait364-wait4-subroutine / 
```c
	#include <sys/wait.h>
	pid_t wait3(int *status, int options, struct rusage *rusage);
	pid_t wait4(pid_t pid, int *status, int options,struct rusage *rusage);
```

	5. Directory management 

	- getcwd() - function copies an absolute pathname of the current working directory to the array pointed to by buf, which is of length size https://man7.org/linux/man-pages/man3/getcwd.3.html
	- chdir() - changes the current working directory of the calling process to the directory specified in path. https://man7.org/linux/man-pages/man2/chdir.2.html
	- opendir() - function opens a directory stream corresponding to the directory name, and returns a pointer to the directory stream. The stream is positioned at the first entry in the directory. https://man7.org/linux/man-pages/man3/opendir.3.html
	- readdir() - returns a pointer to a dirent structure representing the next directory entry in the directory stream pointed to by dirp.  It returns NULL on reaching the end of the directory stream or if an error occurred. https://man7.org/linux/man-pages/man3/readdir.3.html
	- closedir() - closes the directory stream associated with dirp.  A successful call to closedir() also closes the underlying file descriptor associated with dirp.  The directory stream descriptor dirp is not available after this call. https://man7.org/linux/man-pages/man3/closedir.3.html

```c
	#include <unistd.h>

	char *getcwd(size_t size, char buf[size], size_t size);
	int chdir(const char *path);

	#include <sys/types.h>
	#include <dirent.h>
	DIR *opendir(const char *name);
	struct dirent *readdir(DIR *dirp);
	int closedir(DIR *dirp);

	// In the glibc implementation, the dirent structure is defined as follows:
	struct dirent {
		ino_t          d_ino;       /* Icmd number */
		off_t          d_off;       /* Not an offset; see below */
		unsigned short d_reclen;    /* Length of this record */
		unsigned char  d_type;      /* Type of file; not supported
									by all filesystem types */
		char           d_name[256]; /* Null-terminated filename */
	};

```

	6. File info

	- stat() - retrieve information about the file pointed to by path https://man7.org/linux/man-pages/man2/lstat.2.html
	- lstat() - is identical to stat(), except that if path is a symbolic link, then it returns information about the link itself, not the file that the link refers to.*stat source*
	- fstat -is identical to stat(), except that the file about which information is to be retrieved is specified by the file descriptor fd. *stat source*

```c
	#include <sys/stat.h>

	int stat(const char *restrict path, struct stat *restrict statbuf);
	int lstat(const char *restrict path, struct stat *restrict statbuf);
	int fstat(int fd, struct stat *statbuf); 
```

	7. FD 

	- isatty() - tests whether fd is an open file descriptor referring to a terminal. https://man7.org/linux/man-pages/man3/isatty.3.html
	- ttyname() - returns a pointer to the null-terminated pathname of the terminal device that is open on the file descriptor fd, or NULL on error (for example, if fd is not connected to a terminal). https://man7.org/linux/man-pages/man3/ttyname.3.html
	- ttyslot() - returns the index of the current user's entry in some file. https://man7.org/linux/man-pages/man3/ttyslot.3.html 
	- ioctl() -call manipulates the underlying device parameters of special files.  In particular, man operating characteristics of character special files (e.gterminals) may be controlled with ioctl() operations. https://man7.org/linux/man-pages/man2/ioctl.2.html

```c
	#include <unistd.h>

	int isatty(int fd);
	char *ttyname(int fd);
	int ttyslot(void);


	#include <sys/ioctl.h>

	int ioctl(int fd, unsigned long op, ...);
```

	8. Environment

	- getenv() - searches the environment list to find the environment variable name, and returns a pointer to the corresponding value string. https://man7.org/linux/man-pages/man3/getenv.3.html

```c
	#include <stdlib.h>

	char *getenv(const char *name);
```

	9. Terminal

	- tcgetattr() shall return the actual state of the terminal device (reflecting both the changes made and not made in the previous tcsetattr() call). The tcsetattr() function shall not change the values found in the termios structure under any circumstances. https://man7.org/linux/man-pages/man3/tcsetattr.3p.html
	- tcsetattr() - shall set the parameters associated with the terminal referred to by the open file descriptor fildes (an open file descriptor associated with a terminal) from the termios structure referenced by termios_p *same source*

```c
	#include <termios.h>

	int tcgetattr(int fd, struct termios *termios_p);
	int tcsetattr(int fd, int optional_actions, const struct termios *termios_p);
```
	10. Termcap Library

	- tgetent() - routine loads the entry, mandatory before using any of the function bellow https://linux.die.net/man/3/tgetent
	- tgetflag() - routine gets the boolean entry for id, or zero if it is not available. *same source*
	- tgetnum() - routine gets the numeric entry for id, or -1 if it is not available. *same source*
	- tgetstr() - routine returns the string entry for id, or zero if it is not available. *same source*
	- tgoto() - routine instantiates the parameters into the given capability. The output from this routine is to be passed to tputs. *same source*
	- tputs() - retrieve capabilities by either termcap or terminfo name. *same source* 

```c
	#include <curses.h>
	#include <term.h>

	extern char PC;
	extern char * UP;
	extern char * BC;
	extern unsigned ospeed;

	int tgetent(char *bp, const char *name);
	int tgetflag(char *id);
	int tgetnum(char *id);
	char *tgetstr(char *id, char **area);
	char *tgoto(const char *cap, int col, int row);
	int tputs(const char *str, int affcnt, int (*putc)(int));
```