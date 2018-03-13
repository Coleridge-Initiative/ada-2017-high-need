# How to solve a merge conflict in a notebook using nbdime

## What is a conflict (when using git)?

As you collaborate with others on projects using git you will inevitably
run into merge confliicts. A merge conflict is when you and another person
edit the same line of a file. Git will not know which line is the correct one
and create a conflict.

## Make a conflict

If we open up our example notebook `example.ipynb' we can see some very
basic code.

```
%pylab inline
x = list(range(100))
y = np.sin(x)
plt.plot(x,y)
```
This just plots a sin wave.

We are going to create a *branch* of our code. We will talk more about
this in later sessions; this is an easy way to mimic someone else modifying
our code. Our branch is called drama because we are going to
create arbitrary and unnecessary conflict.

```
git checkout -b drama
```
Open up the notebook `example.ipynb` and modify the `sin` function to
a `tan` function. Your code should look like this:
```
%pylab inline
x = list(range(100))
y = np.tan(x)
plt.plot(x,y)
```
Save the notebook and exit out of JupyterHub.
Let's commit our work.
```
git commit -am "changed sin to tan"
```
Let's go back to the main branch, the *master* branch.
```
git checkout master
```
Now we are going to change the same line of code. Just like before go into
JupyterHub and open the `example.ipynb` notebook. Now change the sin to a
cos function so your code looks like this:
```
%pylab inline
x = list(range(100))
y = np.cos(x)
plt.plot(x,y)
```
Save the notebook, exit out of JupyterHub, and commit the results like
before just with a different commit message:
```
git commit -am "changed sin to cos"
```
Now lets merge the `drama` branch wiht the `master` branch.
```
git merge drama
```
We should then have a conflict and see the following output
```
[W nbmergeapp:57] Conflicts occured during merge operation.
[I nbmergeapp:70] Merge result written to .merge_file_4s7ea3
Auto-merging example.ipynb
CONFLICT (content): Merge conflict in example.ipynb
Automatic merge failed; fix conflicts and then commit the result.
```

## Solve a conflict

### Super Quick Way
When we have a coflict it is between your version of the file
and someone else's. If you already know which version of the
file you want to keep, either your version or someone else's,
there is a shortcut.

First figure out which files are conflicting by running:
```
git status
```
This should tell you which files are in conflict. Then
if you want to keep your version:
```
git checkout <name of the file> --ours
git commit -m "fixed conflict by saving my version"
```

If you would like to keep the other person's version:
```
git checkout <name off the file> --theirs
git commit -m "fixed conflict by saving their version"
```

### Using nbdime

To solve this conflict we can use the `nbdime` tool.
```
git mergetool --tool nbdime
```
This should then bring up a graphical interface that shows the line in
question.

Click save the notebook.
On the commandline, you should then see two notebooks.
```
> ls

example.ipynb example.ipynb.orig
```
We can rename `example.ipynb.orig` as `example.ipynb` and open the notebook
in JupyterHub.
```
mv example.ipynb.orig example.ipynb
```

You should see the following in the code cell:
```
%pylab inline
x = list(range(100))
<<<<<<<<< local
y = np.cos(x)
==============
y = np.tan(x)
>>>>>>>>> remote
plt.plot(x,y)
```
The `<<<<<<<` and `>>>>>>>` denote the section of the conflicting code.
`local` means the following line are from the `master` branch while `remote`
shows the preceding line is from the drama branch. The lines of the two
branchs are separated by `=======`. Given a merge conflict we have three
choices: 1) either keep the line from the master branch, keep the line from
the drama branch, or create an entirely new line. In this case we are going
to keep the line from the drama branch. Your code should look like this:
```
%pylab inline
x = list(range(100))
y = np.tan(x)
plt.plot(x,y)
```
Now save the notebook on JupyterHub, commit your work.
```
git commit -am "fixed merge conflict"
```
Now you are fully equipped to solve any merge conflicts that may emerge.
