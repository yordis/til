# Find the Closest Git Directory

The following command will give you the absolute path to the `git` directory.

```bash
git rev-parse --absolute-git-dir
```

Quite handy when you forgot that you cloned another Git repository inside an
existing one and you are trying to figure that part out.
